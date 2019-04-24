---
title: Server concepts in Azure Database for PostgreSQL
description: This article provides considerations and guidelines for configuring and managing Azure Database for PostgreSQL servers.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 4/16/2019
---

# Nodes in Azure Database for PostgreSQL – Hyperscale (Citus) (preview)

The Hyperscale (Citus) (preview) hosting type allows Azure Database for
PostgreSQL servers (called nodes) to coordinate with one another in a "shared
nothing" architecture. The nodes form a server group that can hold more data
and use more CPU cores than would be possible on a single server. This
architecture also allows the database to scale by adding more nodes to the
server group.

## Coordinator and workers

Every server group has a coordinator node and multiple workers. Applications
send their queries to the coordinator node which relays it to the relevant
workers and accumulates their results. Applications are not able to connect
directly to workers. All queries must happen through the coordinator.

For each query, the coordinator either routes it to a single worker node, or
parallelizes it across several depending on whether the required data lives on
a single node or multiple. The coordinator knows how to do this by consulting
its metadata tables. These tables track the DNS names and health of worker
nodes, and the distribution of data across nodes.
