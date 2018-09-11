---
title: Apache Cassandra features & commands supported by Azure Cosmos DB Cassandra API | Microsoft Docs
description: Learn about the Apache Cassandra feature support in Azure Cosmos DB Cassandra API
services: cosmos-db
author: kanshiG
manager: kfile

ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/18/2018
ms.author: govindk
 
---
# Apache Cassandra features supported by Azure Cosmos DB Cassandra API 

Azure Cosmos DB is Microsoft's globally distributed multi-model database service. You can communicate with the Azure Cosmos DB Cassandra API through Cassandra Query Language(CQL) v4 [wire protocol](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) compliant open-source Cassandra client [drivers](http://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver). 

By using the Azure Cosmos DB Cassandra API, you can enjoy the benefits of the Apache Cassandra APIs as well as the enterprise capabilities that Azure Cosmos DB provides. The enterprise capabilities include [global distribution](distribute-data-globally.md), [automatic scale out partitioning](partition-data.md), availability and latency guarantees, encryption at rest, backups, and much more.

## Cassandra protocol 

The Azure Cosmos DB Cassandra API is compatible with CQL version **v4**. The supported CQL commands, tools, limitations, and exceptions are listed below. Any client driver that understands these protocols should be able to connect to Azure Cosmos DB Cassandra API.

## Cassandra driver

The following versions of Cassandra datastax drivers are supported by Azure Cosmos DB Cassandra API:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## CQL data types 

Azure Cosmos DB Cassandra API supports the following CQL data types:

* ascii  
* bigint  
* blob  
* boolean  
* counter  
* date  
* decimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* text  
* time  
* timestamp  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

For other datatypes and attributes, make a feature request or vote on the [uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db)

## CQL functions

Azure Cosmos DB Cassandra API supports the following CQL functions:

* Token  
* Aggregate MIN(), MAX(), SUM(), and AVG()  
* WRITETIME

For other functions, make a feature request or vote on the [uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db)

## Cassandra Query Language limits

Azure Cosmos DB Cassandra API does not have any limits on the size of data stored in a table. Hundreds of terabytes or Petabytes of data can be stored while ensuring partition key limits are honored. Similarly every entity or row equivalent does not have any limits on the number of columns however the total size of the entity should not exceed 2 MB.

## Tools 

Azure Cosmos DB Cassandra API is a managed service platform. It does not require any management overhead or utilities such as Garbage Collector, Java Virtual Machine(JVM), and nodetool to manage the cluster. It supports tools such as cqlsh that utilizes Binary CQLv4 compatibility.

* cqlsh  

* Azure portal's data explorer, metrics, log diagnostics, PowerShell, and cli are other supported mechanisms to manage the account.

## CQL Shell  

CQLSH command-line utility comes with Apache Cassandra 3.1.1 and works out of box with following environment variables enabled:

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
set SSL_VALIDATE=false 
set CQLSH_PORT=10350 
cqlsh.py <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```
**Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_VALIDATE=false 
cqlsh.py <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```

## CQL commands

Azure Cosmos DB supports the following database commands on all Cassandra API accounts.

* CREATE KEYSPACE - The following options of this command are not applicable to Azure Cosmos DB Cassandra API.

  * replication factor is not applicable because availability is guaranteed by Azure Cosmos DB.  

  * class option is not applicable because data in an Azure Cosmos DB account is always replicated.

  * Durable writes option is not applicable because Azure Cosmos DB always commits all the data.

* CREATE TABLE - The following options of this command are not applicable to Azure Cosmos DB Cassandra API.

  * The bloom filter, caching, dclocal_read_repair_chance, memtable_flush_period_in_ms, min_index_interval, max_index_interval, read_repair_chance, speculative_retry, compression, compaction options are not applicable because Cassandra API is a managed service with CQL V4 compatibility. It does not require read repair, compaction, it doesn't require additional data structures and their management to provide the SLA-based performance, and availability. The following command shows how to create a table with a specific throughput by using cqlsh: 

   ``` bash 
   CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=100000
   ```

* ALTER TABLE 

* USE 

* INSERT 

* SELECT 

* UPDATE 

* DELETE

All crud operations when executed through CQLV4 compatible SDK will return extra information about error, request units consumed, activity ID. Delete/update needs to be handled in resource governance safe way to avoid over use of provisioned resources. 

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            string value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

* BATCH - Only unlogged commands are supported 

## Consistency mapping 

Azure Cosmos DB Cassandra API provides choice of consistency for read operations. All write operations, irrespective of the account consistency are always written with write performance SLAs.

## Permission and role management

Azure Cosmos DB supports role-based access control (RBAC) and read-write and read-only passwords/keys that can be obtained through the [Azure portal](https://portal.azure.com. Azure Cosmos DB does not yet support users and roles for data plane activities. 

## Next steps

- Get started with [creating a Cassandra API account, database, and a table](create-cassandra-api-account-java.md) by using a Java application

