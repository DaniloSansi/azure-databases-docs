---
title: Elastic scale with Cassandra API
description: The different options for scaling up/down in a Cassandra API account and their advantages/disadvantages
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
---

# Elastic scale with Cassandra API

There are variety of options to explore the elastic nature of the Azure Cosmos DB API for Cassandra. To understand how to scale effectively in Cosmos DB, it is important to understand request units (RUs), and how to ensure they are provisioned properly to account for the performance demands in your system. To learn more about RUs, first consult our article on [request units](https://docs.microsoft.com/azure/cosmos-db/request-units). 

![Database operations consume Request Units](./media/request-units/request-units.png)

## Handling rate limiting (429 errors)

Cosmos DB will return rate limit (429) errors if clients are consuming more resources (RUs) than have been provisioned in the system. The Cassandra API in Azure Cosmos DB translates these exceptions to overloaded errors on the Cassandra native protocol. 

If your system is not sensitive to latency, it may be sufficient to handle higher than expected throughput using retries. See code sample [here](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) for how to handle rate limiting transparently using the [Cosmos DB Extension](https://github.com/Azure/azure-cosmos-cassandra-extensions) for [Cassandra Retry Policy](https://docs.datastax.com/en/drivers/java/2.0/com/datastax/driver/core/policies/RetryPolicy.html) in Java. We also have a [spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) extension.

If you need to minimize latency, there is a spectrum of options for managing scale and provisioning throughput (RUs) in the Cassandra API:

* Manually using the Azure portal
* Programmatically using the Control Plane
* Programmatically using CQL with your chosen SDK
* Dynamically using Autopilot

We will discuss the advantages and disadvantages of each approach, so you can decide on the best strategy for balancing the scale needs of your system, and the overall cost and efficiency needs for your solution.

## Using the Azure portal

You can consult our article [Provision throughput on containers and databases](https://docs.microsoft.com/azure/cosmos-db/set-throughput), which discusses the relative benefits of setting throughput at either [database](https://docs.microsoft.com/azure/cosmos-db/set-throughput#set-throughput-on-a-database) or [container](https://docs.microsoft.com/azure/cosmos-db/set-throughput#set-throughput-on-a-container) level in the [Azure portal](https://docs.microsoft.com/azure/cosmos-db/set-throughput#set-throughput-on-a-database-and-a-container). Note that the terms "database" and "container" mentioned in these articles map to "keyspace" and "table" respectively for the Cassandra API.

The advantage of this method is that it is a straightforward turnkey way to manage throughput capacity in the database. However, the disadvantage is that in many cases, your approach to scaling may require certain levels of automation to be both cost effective and high performing. We discuss relevant scenarios and methods below.

## Using the Control Plane

The Azure Cosmos DB API for Cassandra provides the capability to adjust throughput programmatically using our various control plane features. Consult our articles [Azure Resource Manager](https://docs.microsoft.com/azure/cosmos-db/manage-cassandra-with-resource-manager), [Powershell](https://docs.microsoft.com/azure/cosmos-db/powershell-samples-cassandra), and [Azure CLI](https://docs.microsoft.com/azure/cosmos-db/cli-samples-cassandra) for guidance and samples.

The advantage of this method is that you can automate the scaling up or down of resources based on a timer to account for peak activity, or periods of low activity. Take a look at our sample [here](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) for how to accomplish this using Azure Functions and Powershell.

A disadvantage with this approach may be that you cannot respond to unpredictable changing scale needs in real time. Instead, you may need to leverage the application context in your system, at the client/SDK level, or using [Autopilot](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autopilot).

## Using CQL with your chosen SDK

You can scale the system dynamically in code by executing [ALTER commands in CQL](https://docs.microsoft.com/azure/cosmos-db/cassandra-support#keyspace-and-table-options) for the given database or container.

The advantage of this approach is that it allows you to respond to scale needs dynamically and in a custom way that suits your application, while still leveraging the standard RU charges and rates. If your system's scale needs are mostly predictable (around 70% or more), using SDK with CQL may be a more cost-effective method of auto-scaling than using Autopilot. The disadvantage with this approach is that it can be quite complex to implement, and retries while rate limiting may increase latency.

## Using Autopilot

In addition to manual or programmatic provisioning of throughput, you can also configure Azure cosmos containers in Autopilot mode. Within specified RU ranges, Autopilot mode will automatically and instantly scale to your consumption needs without compromising SLAs. You can consult our article [Create Azure Cosmos containers and databases in autopilot mode](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autopilot) for details on how to configure Autopilot.

The advantage of this approach is that it is the easiest way of managing the scaling needs in your system, and guarantees not to apply rate limiting **within the configured RU ranges**. The disadvantage is that, if the scaling needs in your system are predictable, Autopilot may be a less cost effective way of handling your scaling needs than using the bespoke control plane or SDK level approaches mentioned above.

## Next steps

- Get started with [creating a Cassandra API account, database, and a table](create-cassandra-api-account-java.md) by using a Java application