---
title: Restart server - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Learn how to restart all nodes in a Hyperscale (Citus) cluster from the Azure portal.
ms.custom: kr2b-contr-experiment
ms.author: jonels
author: jonels-msft
ms.service: cosmos-db
ms.subservice: postgresql
ms.topic: how-to
ms.date: 05/06/2022
---

# Restart Azure Cosmos DB for PostgreSQL

[!INCLUDE [PostgreSQL](../includes/appliesto-postgresql.md)]

You can restart your Hyperscale (Citus) cluster for the Azure portal. Restarting the cluster applies to all nodes; you can't selectively restart
individual nodes. The restart applies to all PostgreSQL server processes in the nodes. Any applications attempting to use the database will experience
connectivity downtime while the restart happens.

1. In the Azure portal, navigate to the cluster's **Overview** page.

1. Select **Restart** on the top bar.
   > [!NOTE]
   > If the Restart button is not yet present for your cluster, please open
   > an Azure support request to restart the cluster.

1. In the confirmation dialog, select **Restart all** to continue.

**Next steps**

- Changing some server parameters requires a restart. See the list of [all
  server parameters](reference-parameters.md) configurable on
  Hyperscale (Citus).
