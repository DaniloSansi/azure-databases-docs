---
title: Diagnose, and troubleshoot Azure Cosmos DB Java Async SDK| Microsoft Docs
description: Use features like client-side logging, and other third-party tools to identify, diagnose, and troubleshoot Azure Cosmos DB issues.
services: cosmos-db
author: moderakh

ms.service: cosmos-db
ms.topic: article
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.custom: troubleshoot java async sdk
ms.topic: troubleshoot
---

## Troubleshooting issues when using Java Async ADK with Azure Cosmos DB SQL API accounts
This article covers common issues, workarounds, diagnostics steps, and tools when using [Java Async ADK](sql-api-sdk-async-java.md) with Azure Cosmos DB SQL API accounts.
Java Async SDK provides client-side logical representation to access Azure Cosmos DB SQL API. This article describes the tools and approaches to help you if you run into any issues.

Start with this list:
    1. Take a look at the [Common issues and workarounds] section in this article.
    2. Our SDK is [open-source on github](https://github.com/Azure/azure-cosmosdb-java) and we have [issues section](https://github.com/Azure/azure-cosmosdb-java/issues) that we actively monitor. Check if there is any similar issue with a workaround already filed.
    3. Review [performance tips](performance-tips-async-java.md) and follow the suggested practices.
    4. Follow the rest of this article, if you didn't find a solution, file a [GitHub issue](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Common issues and workarounds

### Network issues, Netty read timeout failure, low throughput, high latency

#### General suggestions
* Make sure the app is running on the same region as your Cosmos DB account. 
* Check the CPU usage on the host where the app is running. If CPU usage is 90% or more, consider running your app on a host with higher configuration or distribute the load on more machines.

#### Connection throttling
Connection throttling can happen due to either [Connection limit on host machine], or [Azure SNAT (PAT) Port Exhaustion]:

##### <a name="connection-limit-on-host"></a>Connection limit on host machine
Some Linux systems (like 'Red Hat') have an upper limit on the total number of open files. Sockets in Linux are implemented as files, so this number limits the total number of connections too.
Run the following command:

```bash
ulimit -a
```
The number of open files ("nofile") needs to be large enough, (at least as double as your connection pool size). Read more detail in [performance tips](performance-tips-async-java.md).

##### <a name="snat"></a>Azure SNAT (PAT) Port Exhaustion

If your app is deployed on Azure VM, by default [Azure SNAT ports](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) are used to establish connections to any endpoints outside of your VM. The number of connections allowed from the VM to the Cosmos DB endpoint is also limited by the [Azure SNAT configuration](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).
There are two workarounds to avoid Azure SNAT limitation:
    * Add your Azure Cosmos DB endpoint to the VNET of your Azure VM as explained [Enabling VNET Service Endpoint](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-service-endpoints-overview).
    * Assign a public IP to your Azure VM. The Azure SNAT ports are used only when your Azure VM has a private IP address.

#### Http Proxy

If you use an HttpProxy, make sure your HttpProxy can support the number of connections configured in the SDK `ConnectionPolicy`.
Otherwise, you face connection issues.

#### Invalid coding pattern: blocking Netty IO thread

The SDK uses [netty](https://netty.io/) IO library for communicating to Azure Cosmos DB Service. We have async API and we use non-blocking IO APIs of netty. The SDK's IO work is performed on IO netty threads. The number of IO netty threads is configured to be the same as the number of the CPU cores of the app machine. The netty IO threads are only meant to be used for non blocking netty IO work. The SDK returns the API invocation result on one of the netty IO threads to the apps's code. If the app after receiving results on the netty thread performs a long lasting operation on the netty thread, that may result in SDK to not have enough number of IO threads for performing its internal IO work. Such app coding may result in low throughput, high latency, and `io.netty.handler.timeout.ReadTimeoutException` failures. The workaround is to switch the thread when you know the operation will take time.

   For example, the following code snippet shows that if you perform long lasting work, which takes more than a few milliseconds, on the netty thread, you eventually can get into a state where no netty IO thread is present to process IO work, and as a result you get ReadTimeoutException:
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   The workaround is to change the thread on which you perform time taking work. Define a singleton instance of Scheduler for your app:
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Whenever you need to do time taking work (for example, computationally heavy work, blocking IO), switch the thread to a worker provided by your `customScheduler` using `.observeOn(customScheduler)` API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
By using `observeOn(customScheduler)`, you are releasing the netty IO thread and switching the thread to your own custom thread provided by customScheduler. 
This modification will solve the problem in the above, and you won't get `io.netty.handler.timeout.ReadTimeoutException` failure anymore.

### Connection Pool Exhausted Issue

`PoolExhaustedException` is a client-side failure. If you get this failure often, that's indication that your app workload is higher than what the SDK connection pool can serve. Increasing connection pool size or distributing the load on multiple apps may help.

### Request Rate Too Large.
This failure is a service side failure indicating that you consumed your provisioned throughput and should retry later. If you get this failure often, consider increasing the collection throughput.

### Failure in connecting to Cosmos DB Emulator

Cosmos DB emulator HTTPS certificate is self-signed. For SDK to work with emulator you should import the emulator certificate to Java TrustStore. As explained [here](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator-export-ssl-certificates).


## <a name="enable-client-sice-logging"></a>Enable client SDK logging

The async Java SDK uses SLF4j as the logging facade. SFL4J is the logging interface, which makes logging into popular logging frameworks (log4j, logback, etc.) possible.

For example, if you want to use log4j as the logging framework, add the following libs in your Java classpath:

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Also add a log4j config:
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Review [sfl4j logging manual](https://www.slf4j.org/manual.html) for more information.

## <a name="netstats"></a>OS Network Statistics
Run netstat command to get a sense of how many connections are in `Established` state, `CLOSE_WAIT` state, etc.

On Linux you can run the following command:
```bash
netstat -nap
```
Filter the result to only connections to Cosmos DB endpoint.

Apparently, the number of connections to Cosmos DB endpoint in `Established` state should be not greater than your configured connection pool size.

If there are many connections to Cosmos DB endpoint in `CLOSE_WAIT` state, for example more than 1000 connections, that's an indication of connections are established and torn down quickly, which may potentially cause problems. Review [Common issues and workarounds] section for more detail.

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Connection limit on host machine]: #connection-limit-on-host
[Azure SNAT (PAT) Port Exhaustion]: #snat


