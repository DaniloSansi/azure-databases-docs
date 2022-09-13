---
title: Enable private access - Azure Cosmos DB for PostgreSQL
description: How to set up private link in a cluster for Azure Cosmos DB for PostgreSQL
ms.author: jonels
author: jonels-msft
ms.service: cosmos-db
ms.subservice: postgresql
ms.topic: how-to
ms.date: 01/14/2022
---

# Private access in Azure Database for PostgreSQL Hyperscale (Citus)

[!INCLUDE [PostgreSQL](../includes/appliesto-postgresql.md)]

[Private access](concepts-private-access.md) allows resources in an Azure
virtual network to connect securely and privately to nodes in a Hyperscale
(Citus) cluster. This how-to assumes you've already created a virtual
network and subnet. For an example of setting up prerequisites, see the
[private access tutorial](tutorial-private-access.md).

## Create a cluster with a private endpoint

1. Select **Create a resource** in the upper left-hand corner of the Azure portal.

2. Select **Databases** from the **New** page, and select **Azure Database for
   PostgreSQL** from the **Databases** page.

3. For the deployment option, select the **Create** button under **Hyperscale
   (Citus) cluster**.

4. Fill out the new server details form with your resource group, desired
   cluster name, location, and database user password.

5. Select **Configure cluster**, choose the desired plan, and select
   **Save**.

6. Select **Next: Networking** at the bottom of the page.

7. Select **Private access**.

8. A screen appears called **Create private endpoint**. Choose appropriate values
   for your existing resources, and click **OK**:

	- **Resource group**
	- **Location**
	- **Name**
	- **Target sub-resource**
	- **Virtual network**
	- **Subnet**
	- **Integrate with private DNS zone**

9. After creating the private endpoint, select **Review + create** to create
   your Hyperscale (Citus) cluster.

## Enable private access on an existing cluster

To create a private endpoint to a node in an existing cluster, open the
**Networking** page for the cluster.

1. Select **+ Add private endpoint**.

   :::image type="content" source="media/howto-hyperscale-private-access/networking.png" alt-text="Networking screen":::

2. In the **Basics** tab, confirm the **Subscription**, **Resource group**, and
   **Region**. Enter a **Name** for the endpoint, such as `my-server-group-eq`.

	> [!NOTE]
	>
	> Unless you have a good reason to choose otherwise, we recommend picking a
	> subscription and region that match those of your cluster.  The
	> default values for the form fields may not be correct; check them and
	> update if necessary.

3. Select **Next: Resource >**. In the **Target sub-resource** choose the target
   node of the cluster. Generally `coordinator` is the desired node.

4. Select **Next: Configuration >**. Choose the desired **Virtual network** and
   **Subnet**. Customize the **Private DNS integration** or accept its default
   settings.

5. Select **Next: Tags >** and add any desired tags.

6. Finally, select **Review + create >**. Review the settings and select
   **Create** when satisfied.

## Next steps

* Learn more about [private access](concepts-private-access.md).
* Follow a [tutorial](tutorial-private-access.md) to see private access in
  action.
