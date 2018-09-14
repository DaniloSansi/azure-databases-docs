---
title: Read replicas in Azure Database for MySQL.
description: This article describes read replicas for Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mysql
ms.topic: conceptual
ms.date: 09/24/2018
---

# Read replicas in Azure Database for MySQL

The Read replica feature allows you to replicate data from an Azure Database for MySQL server (master) to up to five read-only servers (replicas) within the same Azure region. Read-only replicas are asynchronously updated using the MySQL engine's native binary log (binlog) file position-based replication technology. To learn more about binlog replication, see the [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Replicas created in the Azure Database for MySQL service are new servers that can be managed in the same way as normal/standalone MySQL servers. These servers are charged at the same rate as a standalone server.

To learn more about MySQL replication features, please see the [MySQL replication documentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## When to use read replicas

Applications and workloads that are read intensive can be served by the read-only replicas. Read replicas help increase the amount of read capacity available compared to if you were to just use a single server for both read and write. The read workloads can be isolated to the replicas, while write workloads can be directed to the master.

A common scenario is to have BI and analytical workloads use the read replica as the data source for reporting.

## Considerations and limitations

### Pricing tiers

Read replicas are currently only available in the General Purpose and Memory Optimized pricing tiers.

### Stopping replication

You can choose to stop replication between a master and a replica server. Stopping replication removes the replication relationship between the master and replica server.

Once replication has been stopped, the replica server becomes a standalone server. The data in the standalone server is the data that was available on the replica at the time the "stop replication" command was initiated. The standalone server does not catch up with the master server. This server cannot be made into a replica again.

### Replicas are new servers

Replicas are created as new Azure Database for MySQL servers. Existing servers cannot be made into replicas.

### Replica server configuration

Replica servers are created using the same server configurations as the master, which includes the following configurations:

- Pricing tier
- Compute generation
- vCores
- Storage
- Backup retention period
- Backup redundancy option
- MySQL engine version

After a replica has been created, you can change the pricing tier (except to and from Basic), compute generation, vCores, storage, and backup retention independently from the master server. It is recommended that if a master's server configuration is updated, the replicas' configuration should also be updated to equal or greater values. This ensures that the replica is able to keep up with changes made to the master.

### Deleting the master server

When a master server is deleted, replication is stopped to all read replicas. These replicas become standalone servers. The master server itself is deleted.

### User accounts

Users on the master server are replicated to the read replicas. You can only connect to a read replica using the user accounts available on the master server.

### Other

- Global transaction identifiers (GTID) are not supported.
- Creating a replica of a replica is not supported.

## Next steps

- Learn how to [create and manage read replicas using the Azure portal](howto-read-replicas-portal.md)

<!--
- Learn how to [create and manage read replicas using the Azure CLI](howto-read-replicas-using-cli.md)
-->
