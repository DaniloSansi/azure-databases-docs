---
title: Cross-region replication
titleSuffix: Azure Cosmos DB for MongoDB vCore
description: Learn about Azure Cosmos DB for MongoDB vCore cross-region disaster recovery (DR) and read replicas.
author: niklarin
ms.author: nlarin
ms.service: cosmos-db
ms.subservice: mongodb-vcore
ms.topic: conceptual
ms.date: 04/28/2024
---

# Cross-region replication and read replicas in Azure Cosmos DB for MongoDB vCore

[!INCLUDE[MongoDB vCore](../../includes/appliesto-mongodb-vcore.md)]

> [!IMPORTANT]
> Cross-region replication in Azure Cosmos DB for MongoDB vCore is currently in preview.
> This preview version is provided without a service level agreement (SLA), and it's not recommended
> for production workloads. Certain features might not be supported or might have constrained
> capabilities.

This article discusses cross-region disaster recovery (DR) for Azure Cosmos DB for MongoDB vCore. It also covers read capabilities of the cluster replicas in other regions for read operations scalability.

The cross-region replication feature allows you to replicate data from a cluster to a read-only cluster in another Azure region. Replicas are updated asynchronously with physical replication technology. You can have one cluster replica in another region of choice for the primary Azure Cosmos DB for MongoDB vCore cluster. In a rare case of region outage, you can promote cluster replica in another region to become the new read-write cluster for continuous operation of your MongoDB database. Applications might continue to use the same connection strings after cluster replica in another region is promoted to become the new primary cluster.   

Replicas are new clusters that you manage similar to regular clusters. For each read replica, you're billed for the provisioned compute in vCores and storage in GiB/month. Compute and storage costs for replica clusters have the same structure as the regular clusters and prices of the Azure region where they're created.

## Disaster recovery (DR) using cluster read replicas

Cross-region replication is one of several important pillars in [the Azure business continuity and disaster recovery (BCDR) strategy](../../../reliability/business-continuity-management-program.md). Cross-region replication asynchronously replicates the same applications and data across other Azure regions for disaster recovery protection. Not all Azure services automatically replicate data or automatically fall back from a failed region to cross-replicate to another enabled region. Azure Cosmos DB for MongoDB vCore provides an option to create a cluster replica in another region and have data written on the primary cluster replicated to that replica automatically. The fallback to the cluster replica in case of an outage in the primary region needs to be initiated manually.

When cross-region replication is enabled on an Azure Cosmos DB for MongoDB vCore cluster, each shard gets replicated to another region continuously. This replication maintains a replica of data in the selected region. Such a replica is ready to be used as a part of disaster recovery plan in a rare case of the primary region outage. Replication is asynchronous. It means write operations on the primary cluster's shard don't wait for replication to the corresponding replica's shard to be completed before sending confirmation of a successful write to the application. Asynchronous replication helps to avoid increased latencies for write operations on the primary cluster.  

### Read operations on cluster replicas and connection strings

Replica clusters are also available for reads. It helps offload intensive read operations from the primary cluster or deliver reduced latency for read operations to the clients that are located closer to the replication region.

When a cross-region replication is enabled a read-only connection string is created. Applications can use this string to perform reads from the cluster replica. The primary cluster is available for read and write operations using a read-write connection string. 

### Replica cluster promotion

In the event of region outage you can perform disaster recovery operation by promoting your cluster replica in another region to become available for writes. During replica promotion operation the following is happening:

- Writes on the replica in region B are enabled in addition to reads. The former replica becomes a new read-write cluster. 
- The former primary cluster in region A is replaced with a replica cluster that is synchronized with the new read-write cluster (former replica cluster). The replica cluster is located in the original Azure region A and becomes the destination for the promoted replica cluster.
- Read-write connection string is now pointing to the promoted replica cluster in region B.
- Read-only connection string is now pointing to the new replica cluster in region A.

> [!IMPORTANT]
> The promotion process is irreversible. When cluster replica is promoted to become a new read-write cluster,
> all data that have NOT been replicated to it from the primary cluster is going to be lost.

## Next steps

- Learn how to enable cross-region replication and promote replica cluster
- [Learn about reliability in Azure Cosmos DB for MongoDB vCore](../../../reliability/reliability-cosmos-mongodb.md)
