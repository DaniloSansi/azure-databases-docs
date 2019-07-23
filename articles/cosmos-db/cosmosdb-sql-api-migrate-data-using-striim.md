---
title: Migrate data to Azure Cosmos DB SQL API account using Striim 
description: Learn how to use Striim to migrate data from an Oracle database to an Azure Cosmos DB SQL API account. 
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rimman
ms.reviewer: sngun
---

# Migrate data to Azure Cosmos DB SQL API account using Striim
 
The Striim platform in Azure offers continuous real-time data movement from data warehouses and databases to Azure. While moving the data, you can perform in-line denormalization and transformation capabilities to reduce end to end latency and enable real time analytics and reporting workloads. It’s easy to get started with Striim to continuously move enterprise data to Azure Cosmos DB SQL API. Azure provides a marketplace offering that make it easy to deploy Striim and migrate data. 

This article shows how to use Striim to migrate data from an Oracle database to an Azure Cosmos DB SQL API account.

## Prerequisites

* If you don't have an [Azure subscription](/azure/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), create a [free account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) before you begin.

* Make sure you have an Oracle database running on-premise with some sample data in it.

## Deploy the Striim Marketplace Solution

1. Sign into the [Azure portal](https://portal.azure.com/).

1. Select **Create a resource** and search for **Striim** in the Azure marketplace. Select the first option and **Create**.

   ![Find Striim marketplace item](./media/cosmosdb-sql-api-migrate-data-using-striim/striim-azure-marketplace.png)

1. Next, enter the configuration properties of the Striim instance. Striim environment is deployed in a virtual machine, so in the Basics pane, enter the **VM user name**, **VM password** (this password is used to SSH into the VM). Select your **Subscription**, **Resource Group**, and **Location details** where you’d like to deploy Striim. Once complete, select **OK**

   ![Configure basic settings for Striim](./media/cosmosdb-sql-api-migrate-data-using-striim/striim-configure-basic-settings.png)

1. In the **Striim Cluster settings** pane, choose the type of Striim deployment and the virtual machine size.

   |Setting	| Value | Description |
   | ---| ---| ---|
   |Striim deployment type |Standalone | Striim can run in a **Standalone** or **Cluster** deployment types. Standalone mode will deploy the Striim server on a single virtual machine. Select the size of the VMs depending on your data volume by default, “Standard_F4s” size is selected. Cluster mode will deploy the Striim server on two or more VMs with the selected size. Cluster environments with more than 2 nodes offer automatic high availability and failover. In this tutorial you can select Standalone option. | 
   | Name of the Striim cluster|	<Striim_cluster_Name>|	Name of the Striim cluster.|
   | Striim cluster password|	<Striim_cluster_password>|	Password for the cluster.|

   After you fill the form, select **OK** to continue.

1. In the **Striim access settings** pane, configure the **Public IP address** (choose the default values), **Domain name for Striim**, **Admin password** that you’d like to use to login to the Striim UI, and configure a VNET and Subnet, you can choose the default values. After filling in the details, select **OK** to continue.

   ![Striim access settings](./media/cosmosdb-sql-api-migrate-data-using-striim/striim-access-settings.png)

1. Azure will review your deployment and make sure everything looks good; validation takes few minutes to complete. Select **OK** after the validation has completed.
  
1. Finally, review the terms of use and select **Create** to create your Striim instance. 

## Configure the source database

In this section you configure Oracle database as the source.  You’ll need the Oracle JDBC driver to connect to Oracle. To read changes from your source Oracle database, you can use either the LogMiner or XStream API’s. To use OracleReader with LogMiner, to write to Oracle using DatabaseWriter, or to persist a WActionStore to Oracle, the Oracle JDBC driver must be present in Striim's Java classpath.

Navigate to Oracle.com and download the [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) driver to your local machine. You will install it in the Striim cluster later.

## Configure target Cosmos DB SQL API Instance

1. Create an [Azure Cosmos DB SQL API account](create-cosmosdb-resources-portal.md) using the Azure Portal.

1. Navigate to the **Data Explorer** pane in your Azure Cosmos account. Select **New Container** to create a new test container. Assume that you are migrating products and orders data from Oracle database to Azure Cosmos DB. Create a new database named **StriimDemo** with a container named **Orders**. Provision the container with **1000 RUs**, and **/ORDER_ID** as the partition key. These values will differ depending on your source data. 

   ![Create a SQL API account](./media/cosmosdb-sql-api-migrate-data-using-striim/create-sql-api-account.png)

## Configure Oracle to Azure Cosmos DB SQL API Data Flow

1. Now, let’s get back to Striim. Before interacting with Striim, install the Oracle JDBC driver that you downloaded earlier.

1. Navigate to the Striim instance that you deployed in the Azure Portal. Select the **Connect** button in the upper menu bar and from the **SSH** tab, copy the URL in **Login using VM local account** field.

   ![Get the SSH URL](./media/cosmosdb-sql-api-migrate-data-using-striim/get-ssh-url.png)

1. Open a new terminal window and run the SSH command you copied from the Azure portal. This article uses terminal in a Mac OS, you can follow the similar instructions using PuTTY or a different SSH client on a Windows machine. When prompted, type **yes** to continue and enter the **password** you have set for the virtual machine in the previous step.

   ![Connect to Striim VM](./media/cosmosdb-sql-api-migrate-data-using-striim/striim-vm-connect.png)

1. Now, open a new terminal tab to copy the ojdbc8.jar file you downloaded previously. Use the following SCP command to copy the jar file from your local machine to the tmp folder of the Striim instance running in Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Copy the Jar file from location machine to Striim](./media/cosmosdb-sql-api-migrate-data-using-striim/copy-jar-file.png)

1. Next, navigate back to the window where you did SSH to the Striim instance and Login as sudo. Move the ojdbc8.jar file from the **/tmp** directory into the **lib** directory of your Striim instance with the following commands:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Move the Jar file to lib folder](./media/cosmosdb-sql-api-migrate-data-using-striim/move-jar-file.png)


1. From the same terminal window, restart the Striim server by executing the following commands:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim will take a minute to startup. If you’d like to see the status, run the following command: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Now, navigate back to Azure and copy the Public IP address of your Striim VM. 

   ![Copy Striim VM IP address](./media/cosmosdb-sql-api-migrate-data-using-striim/copy-public-ip-address.png)

1. To navigate to the Striim’s Web UI, open a new tab in a browser and copy the public IP followed by :9080. Sign in by using the **admin** username, along with the admin password you specified in the Azure portal.

   ![Login to Striim](./media/cosmosdb-sql-api-migrate-data-using-striim/striim-login-ui.png)

1. Now you’ll arrive at Striim’s home page. There are three different panes – **Dashboards**, **Apps** and **SourcePreview**. The Dashboards pane allows you to move data in real-time and visualize it. The Apps pane contains your streaming data pipelines, or data flows. On the right hand of the page is SourcePreview where you can preview your data before moving it.

1. Select the **Apps** pane, we’ll focus on this pane for now. There are a variety of sample apps that you can use to learn about Striim, however in this article you will create our own. Select the **Add App** button in the top right-hand corner.

   ![Add the Striim app](./media/cosmosdb-sql-api-migrate-data-using-striim/add-striim-app.png)

1. There are a few different ways to create Striim applications. In this demo you will use an existing template, select **Start with Template**.

   ![Start the app with the template](./media/cosmosdb-sql-api-migrate-data-using-striim/start-with-template.png)

1. In the **Search templates** field, type “Cosmos” and select **Target: Azure Cosmos DB** and then select **Oracle CDC to Azure Cosmos DB**.

   ![Select Oracle CDC to Cosmos DB](./media/cosmosdb-sql-api-migrate-data-using-striim/oracle-cdc-cosmosdb.png)

1. In the next page, name your application. You can provide a name such as **oraToCosmosDB** and then select **Save**.

1. Next enter the source configuration of your source Oracle instance. Enter a value for the **Source Name**, it is just a naming convention for the Striim application, you can use something like “src_onPremOracle”. Enter values for rest of the source parameters **URL**, **Username**, **Password**, choose **LogMiner** as the reader to read data from Oracle. Select **Next** to continue.

   ![Configure source parameters](./media/cosmosdb-sql-api-migrate-data-using-striim/configure-source-parameters.png)

1. Striim will check your environment and make sure that it can connect to your source Oracle instance, have the right privileges, and that CDC was configured properly. Once all the values are validated, select **Next**.

   ![Validate source parameters](./media/cosmosdb-sql-api-migrate-data-using-striim/validate-source-parameters.png)

1. Select the tables from Oracle database that you’d like to migrate. For example, let’s choose the Orders table and select **Next**. 

   ![Select source tables](./media/cosmosdb-sql-api-migrate-data-using-striim/select-source-tables.png)

1. After selecting the source table, you can do more complicated operations such as mapping and Filtering. For this demo, you will just create a replica of your source table in Azure Cosmos DB. So, select **Next** to configure the target

1. Now, let’s configure the target:

   * **Target Name** - Provide a friendly name for the target. 
   * **Input From** - From the dropdown, select the input stream from the one you created in the source Oracle configuration. 
   * **Collections**- Enter the target Azure Cosmos DB configuration properties. The collections syntax is **SourceSchema.SourceTable,TargetDatabase.TargetContainer**, in this example, the value would be “SYSTEM.ORDERS, StriimDemo.Orders”. 
   * **AccessKey** - The PrimaryKey of your Azure Cosmos account.
   * **ServiceEndpoint** – The URI of your Azure Cosmos account, they can be found under the **Keys** section of the Azure Portal. 

   Click **Save** and **Next.

   ![Configure target parameters](./media/cosmosdb-sql-api-migrate-data-using-striim/configure-target-parameters.png)


1. Next you’ll arrive at the flow designer where you can drag and drop out of the box connectors to create your streaming applications. You will not make any modifications to the flow at this point so go ahead and deploy the application by selecting the **Deploy App** button.
 
   ![Deploy the app](./media/cosmosdb-sql-api-migrate-data-using-striim/deploy-app.png)

1. In the deployment window you can specify if you want to run certain parts of your application on specific parts of your deployment topology. Since we’re running in a simple deployment topology through Azure, we’ll use the default option.

   ![Use the default option](./media/cosmosdb-sql-api-migrate-data-using-striim/deploy-using-default-option.png)

1. After deploying, you can preview the stream to see data flowing through. Select the **wave** icon and the eyeball next to it. Select the **Deployed** button in the top menu bar, and select **Start App**.

   ![Start the app](./media/cosmosdb-sql-api-migrate-data-using-striim/start-app.png)

1. By using a CDC(Change Data Capture) reader, Striim will pick up only new changes on the database. If you have data flowing through your source tables, you’ll see it. However, since this is a demo table, the source that isn’t connected to any application. If you use a sample data generator and insert a chain of events into your Oracle database.

1. You’ll see data flowing through the Striim platform. Striim picks up all the metadata associated with your table as well, which is helpful to monitor the data make sure the data lands on the target.

   ![Configure CDC pipeline](./media/cosmosdb-sql-api-migrate-data-using-striim/configure-cdc-pipeline.png)

1. Finally, let’s sign into Azure and navigate to your Azure Cosmos DB account. Refresh the Data Explorer and you can see that data has arrived.  

   ![Validate migrated data in Azure](./media/cosmosdb-sql-api-migrate-data-using-striim/portal-validate-results.png)

By using the Striim solution in Azure, you can continuously migrate data to Azure Cosmos DB, transform a transactional database to a JSON document structure without any coding. 

## Next steps
