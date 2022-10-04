---
title: Apache Cassandra and Azure Cosmos DB consistency levels
description: Apache Cassandra and Azure Cosmos DB consistency levels.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 03/24/2022
ms.reviewer: mjbrown
---

# Apache Cassandra and Azure Cosmos DB Cassandra API consistency levels
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Unlike Azure Cosmos DB, Apache Cassandra does not natively provide precisely defined consistency guarantees. Instead, Apache Cassandra provides a write consistency level and a read consistency level, to enable the high availability, consistency, and latency tradeoffs. When using Azure Cosmos DB's Cassandra API:

* The write consistency level of Apache Cassandra is mapped to the default consistency level configured on your Azure Cosmos account. Consistency for a write operation (CL) can't be changed on a per-request basis.

* Azure Cosmos DB will dynamically map the read consistency level specified by the Cassandra client driver to one of the Azure Cosmos DB consistency levels configured dynamically on a read request.

## Multi-region writes vs single-region writes

Apache Cassandra database is a multi-master system by default, and does not provide an out-of-box option for single-region writes with multi-region replication for reads. However, Azure Cosmos DB provides turnkey ability to have either single region, or [multi-region](../how-to-multi-master.md) write configurations. One of the advantages of being able to choose a single region write configuration across multiple regions is the avoidance of cross-region conflict scenarios, and the option of maintaining strong consistency across multiple regions. 

With single-region writes, you can maintain strong consistency, while still maintaining a level of high availability across regions with [service-managed failover](../high-availability.md#region-outages). In this configuration, you can still exploit data locality to reduce read latency by downgrading to eventual consistency on a per request basis. In addition to these capabilities, the Azure Cosmos DB platform also provides the ability to enable [zone redundancy](/azure/architecture/reliability/architect) when selecting a region. Thus, unlike native Apache Cassandra, Azure Cosmos DB allows you to navigate the CAP Theorem [trade-off spectrum](../consistency-levels.md#rto) with more granularity.

## Mapping consistency levels

The Azure Cosmos DB platform provides a set of five well-defined, business use-case oriented consistency settings with respect to replication and the tradeoffs defined by the [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem) and [PACLC theorem](https://en.wikipedia.org/wiki/PACELC_theorem). As this approach differs significantly from Apache Cassandra, we would recommend that you take time to review and understand [Azure Cosmos DB consistency](../consistency-levels.md), or watch this short [video guide to understanding consistency settings](https://aka.ms/docs.consistency-levels) in the Azure Cosmos DB platform. The following table illustrates the possible mappings between Apache Cassandra and Azure Cosmos DB consistency levels when using Cassandra API. This shows configurations for single region, multi-region reads with single-region writes, and multi-region writes.

> [!NOTE]
> These are not exact mappings. Rather, we have provided the closest analogues to Apache Cassandra, and disambiguated any qualitative differences in the rightmost column. As mentioned above, we recommend reviewing Azure Cosmos DB's [consistency settings](../consistency-levels.md). 

:::image type="content" source="./media/apache-cassandra-consistency-mapping/account.png" alt-text="Cassandra consistency account level mapping" lightbox="./media/apache-cassandra-consistency-mapping/account.png" :::

:::image type="content" source="./media/apache-cassandra-consistency-mapping/dynamic.png" alt-text="Cassandra consistency dynamic mapping" lightbox="./media/apache-cassandra-consistency-mapping/dynamic.png" :::

If your Azure Cosmos account is configured with a consistency level other than the strong consistency, you can find out the probability that your clients may get strong and consistent reads for your workloads by looking at the *Probabilistically Bounded Staleness* (PBS) metric. This metric is exposed in the Azure portal, to learn more, see [Monitor Probabilistically Bounded Staleness (PBS) metric](../how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic bounded staleness shows how eventual is your eventual consistency. This metric provides an insight into how often you can get a stronger consistency than the consistency level that you have currently configured on your Azure Cosmos account. In other words, you can see the probability (measured in milliseconds) of getting strongly consistent reads for a combination of write and read regions.


## Global strong consistency for write requests in Apache Cassandra

Apache Cassandra, the setting of EACH_QUORUM or QUORUM gives a strong consistency. When a write request is sent to a region, EACH_QUORUM persists the data in a quorum number of nodes in each data center, which requires every data center to be available for the write operation to succeed. QUORUM is slightly less restrictive, with a QUORUM number of nodes across all the data centers needed to persist the data prior to acknowledging the write to be successful.

The following graphic illustrates a global strong consistency setting in Apache Cassandra between two regions 1 and 2. After data is written to region 1, the write needs to be persisted in a quorum number of nodes in both region 1, and region 2 before an acknowledgment is received by the application.

:::image type="content" source="./media/cassandra-consistency-overview/write-global-consistency-apache-cassandra.png" alt-text="Global consistency in Apache Cassandra":::


## Global strong consistency for write requests in Azure Cosmos DB for Apache Cassandra
In Azure Cosmos DB consistency is set at the account level. With Strong Consistency in Azure Cosmos DB Cassandra API, data is replicated synchronously to the read regions for the account. The further apart the regions for the Azure Cosmos DB account are, the higher the latency of the strongly consistent write operations.

:::image type="content" source="./media/cassandra-consistency-overview/write-global-consistency-azure-comos-db.png" alt-text="Global consistency in Azure Cosmos DB for Apache Cassandra":::

How the number of regions affects your read or write request: 
- Two regions: With strong consistency, quorum (N/2 + 1) = 2. So, if the read region goes down, the account can no longer accept writes with strong consistency since a quorum number of regions is not available for the write to be replicated to.
- Three or more regions: for N = 3, quorum = 2. If one of the read regions is down, the write region can still replicate the writes to a total of two regions that meet the quorum requirement. Similarly, with four regions, quorum = 4/2 + 1 = 3.  Even with one read region being down, quorum can be met.

> [!NOTE]
> If a globally strong consistency is required for all write operations, the consistency for the Cosmos DB Cassandra API account must be set to Strong. The consistency level for write operations cannot be overridden to a lower consistency level on a per request basis in Azure Cosmos DB.


## Weaker consistency for write requests in Apache Cassandra
A consistency level of ANY, ONE, TWO, THREE, LOCAL_QUORUM, Serial or Local_Serial. 
Consider a write request with LOCAL_QUORUM with an RF of 4 in a six-node datacenter. Quorum = 4/2 + 1 = 3.

:::image type="content" source="./media/cassandra-consistency-overview/write-not-global-consistency-apache-cassandra.png" alt-text="Non Global consistency for Apache Cassandra":::


## Weaker consistency for write requests in Azure Cosmos DB for Apache Cassandra
When a write request is sent with any of the consistency levels lower than Strong, a success response is returned as soon as the local region persists the write in at least 3 out of 4 replicas.

:::image type="content" source="./media/cassandra-consistency-overview/write-not-global-Azure-Cosmos-DB.png" alt-text="Non Global consistency in Azure Cosmos DB for Apache Cassandra":::

## Global strong consistency for read requests in Apache Cassandra
With a consistency of EACH_QUORUM, a strongly consistent read can be achieved in Apache Cassandra. In, a multi-region setup for EACH_QUORUM if the quorum number of nodes is not met in each region, then the read will be unsuccessful. 

:::image type="content" source="./media/cassandra-consistency-overview/read-global-consistency-apache-cassandra.png" alt-text="Read Global consistency in Apache Cassandra":::


## Global strong consistency for read requests in Azure Cosmos DB for Apache Cassandra
The read request is served from 2 replicas in the specified region. Since the write already took care of persisting to a quorum number of regions (and all regions if every region was available), simply reading from 2 replicas in the specified region provides Strong consistency.  This requires EACH_QUORUM to be specified in the driver when issuing the read against a region for the Cosmos DB account along with Strong Consistency as the default consistency level for the account.

:::image type="content" source="./media/cassandra-consistency-overview/read-global-consistency-Azure-Cosmos-DB.png" alt-text="Read Global consistency in Azure Cosmos DB for Apache Cassandra":::


## Local strong consistency in Apache Cassandra
A read   request with a consistency level of TWO, THREE, LOCAL_QUORUM will give us strong consistency reading from local region. With a consistency level of LOCAL_QUORUM, you need a response from two nodes in the specified datacenter for a successful read.

:::image type="content" source="./media/cassandra-consistency-overview/read-local-strong-apache-cassandra.png" alt-text="Read Local strong consistency for Apache Cassandra":::

## Local strong consistency in Azure Cosmos DB for Apache Cassandra
In Azure Cosmos DB Cassandra API, having a consistency level of TWO, THREE or LOCAL_QUORUM will give a local strong consistency for a read request. Since the write path guarantees replicating to a minimum of 3 out of 4 replicas, a read from 2 replicas in the specified region will guarantee a quorum read of the data in that region.

:::image type="content" source="./media/cassandra-consistency-overview/read-local-strong-consistency-Azure-Cosmos-DB.png" alt-text="Read Local strong consistency in Azure Cosmos DB for Apache Cassandra":::

## Eventual consistency in Apache Cassandra
A consistency level of LOCAL_ONE, One and ANY with LOCAL_ONE will result in eventual consistency. This is used in cases where the focus is on latency. 

:::image type="content" source="./media/cassandra-consistency-overview/read-eventual-consistency-apache-cassandra.png" alt-text="Read eventual consistency for Apache Cassandra":::

## Eventual consistency in Azure Cosmos DB for Apache Cassandra?
A consistency level of LOCAL_ONE, ONE or Any will give you eventual consistency. With eventual consistency a read is served from just one of the replicas in the specified region. 

:::image type="content" source="./media/cassandra-consistency-overview/read-eventual-consistency-Azure-Cosmos-DB.png" alt-text="Read eventual consistency in Azure Cosmos DB for Apache Cassandra":::



## Override consistency level for read operations in Azure Cosmos DB Cassandra API

Previously, the consistency level for read requests could only be overridden to a lower consistency than the default set on the account. For instance, with the default consistency of Strong, read requests could be issued with Strong by default and overridden on a per request basis (if needed) to a consistency level weaker than Strong. However, read requests could not be issued with an overridden consistency level higher than the account’s default. An account with Eventual consistency could not receive read requests with a consistency level higher than Eventual (which in the Apache Cassandra drivers translate to TWO, THREE, LOCAL_QUORUM or QUORUM).

The Azure Cosmos DB Cassandra API now facilitates overriding the consistency on read requests to a value higher than the account’s default consistency. For instance, with the default consistency on the Cosmos DB account set to Eventual (Apache Cassandra equivalent of One or ANY), read requests can be overridden on a per request basis to LOCAL_QUORUM. This ensures that a quorum number of replicas within the specified region are consulted prior to returning the result set, as required by LOCAL_QUORUM. 
This also prevents the need to set a default consistency that is higher than Eventual, when it is only needed for read requests.


## Next steps

Learn more about global distribution and consistency levels for Azure Cosmos DB:

* [Global distribution overview](../distribute-data-globally.md)
* [Consistency Level overview](../consistency-levels.md)
* [High availability](../high-availability.md)
