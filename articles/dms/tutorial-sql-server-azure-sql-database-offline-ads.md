---
title: "Tutorial: Migrate SQL Server to Azure SQL Database (preview) offline in Azure Data Studio"
titleSuffix: Azure Database Migration Service
description: Learn how to migrate on-premises SQL Server to Azure SQL Database (preview) offline by using Azure Data Studio and Azure Database Migration Service.
services: dms
author: croblesm
ms.author: roblescarlos
manager: 
ms.reviewer: 
ms.service: dms
ms.workload: data-services
ms.custom: "seo-lt-2019"
ms.topic: tutorial
ms.date: 09/28/2022
---

# Tutorial: Migrate SQL Server to Azure SQL Database (preview) offline in Azure Data Studio

You can use Azure Database Migration Service and the Azure SQL Migration extension in Azure Data Studio to migrate databases from an on-premises instance of SQL Server to Azure SQL Database (preview) offline and with minimal downtime.

> [!NOTE]
> The option to migrate a SQL Server database to Azure SQL Database by using Azure Data Studio currently is in preview. Azure SQL Database targets are available only by using the [Azure Data Studio Insiders](/sql/azure-data-studio/download-azure-data-studio#download-the-insiders-build-of-azure-data-studio) version of the Azure SQL Migration extension.

In this tutorial, learn how to migrate the AdventureWorks2019 database from an on-premises instance of SQL Server to an instance of Azure SQL Database by using the Azure SQL Migration extension for Azure Data Studio. This tutorial focuses on the offline migration mode, which considers an acceptable downtime during the migration process.

In this tutorial, you learn how to:
> [!div class="checklist"]
>
> - Open the Migrate to Azure SQL wizard in Azure Data Studio
> - Run an assessment of your source SQL Server databases
> - Collect performance data from your source SQL Server instance
> - Get a recommendation of the Azure SQL Database SKU that will work best for your workload
> - Deploy your on-premises database schema to Azure SQL Database
> - Create a new instance of Azure Database Migration Service
> - Start your migration and monitor the progress to completion

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

> [!IMPORTANT]
> Currently, *online* migrations for Azure SQL Database targets aren't available.

## Prerequisites

Before you begin the tutorial:

- [Download and install Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).
- [Install the Azure SQL Migration extension](/sql/azure-data-studio/extensions/azure-sql-migration-extension) from Azure Data Studio Marketplace.
- Have an Azure account that is assigned to one of the following built-in roles:

  - Contributor for the target instance of Azure SQL Database.
  - Reader role for the Azure resource groups that contain the target instance of Azure SQL Database.
  - Owner or Contributor role for the Azure subscription (required if you create a new instance of Azure Database Migration Service).
  
  As an alternative to using one of the preceding built-in roles, you can [assign a custom role](resource-custom-roles-sql-database-ads.md).
  
  > [!IMPORTANT]
  > An Azure account is required only when you configure the migration steps. An Azure account isn't required for the assessment or for Azure recommendation in the migration wizard in Azure Data Studio.

- Create a target instance of [Azure SQL Database](/azure/azure/azure-sql/database/single-database-create-quickstart).

- Make sure that the SQL Server login that connects to the source SQL Server is a member of the db_datareader role and that the login for the target SQL Server instance is a member of the db_owner role.

- Migrate the database schema from source to target by using the [SQL Server dacpac extension](/sql/azure-data-studio/extensions/sql-server-dacpac-extension) or the [SQL Database Projects extension](/sql/azure-data-studio/extensions/sql-database-project-extension) in Azure Data Studio.

- If you're using Azure Database Migration Service for the first time, make sure that the Microsoft.DataMigration resource provider is registered in your subscription. You can complete the steps to [register the resource provider](quickstart-create-data-migration-service-portal.md#register-the-resource-provider).

## Open the Migrate to Azure SQL wizard in Azure Data Studio

1. In Azure Data Studio, go to Connections. Select and connect to your on-premises instance of SQL Server. You also can connect to SQL Server on an Azure virtual machine.

1. Right-click the server connection and select **Manage**.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/azure-data-studio-manage-panel.png" alt-text="Screenshot that shows a server connection and the Manage option.":::

1. On the server's home page, under **General**, select the **Azure SQL Migration** extension.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/launch-migrate-to-azure-sql-wizard-1.png" alt-text="Screenshot that shows the Azure Data Studio General pane.":::

1. In the Azure SQL Migration dashboard, select **Migrate to Azure SQL** to open the migration wizard.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/launch-migrate-to-azure-sql-wizard-2.png" alt-text="Screenshot that shows the Migrate to Azure SQL wizard.":::

1. On the first page of the wizard, start a new session or resume a previously saved session. If no earlier session is saved, the **Database assessment** page appears.

## Run database assessment, collect performance data, and get right-sized recommendations

1. Select the databases you want to assess, and then select **Next**.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/assessment-database-selection.png" alt-text="Screenshot that shows database assessment.":::

1. For the target, select **Azure SQL Database (Preview)**.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/assessment-target-selection.png" alt-text="Screenshot that shows selection of the Azure SQL Database target.":::

1. Select **View/Select** to view the assessment results.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/assessment.png" alt-text="Screenshot that shows view/select assessment results.":::

1. Select the database, and then review the assessment report to make sure that no issues were found.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/assessment-issues-details.png" alt-text="Screenshot that shows assessment report.":::

1. Select **Get Azure recommendation** to open the recommendations panel.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/get-azure-recommendation.png" alt-text="Screenshot that shows Azure recommendations.":::

1. Select the **Collect performance data now** option. Select a folder on your local computer to store the performance logs in, and then select **Start**.

   Azure Data Studio collects performance data until you either stop data collection or close Azure Data Studio.  

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/get-azure-recommendation-zoom.png" alt-text="Screenshot that shows performance data collection.":::

1. After 10 minutes, Azure Data Studio indicates that a recommendation is available for Azure SQL Database (Preview). After the first recommendation is generated, you can select **Restart data collection** to continue the data collection process to refine the SKU recommendation. An extended assessment if especially helpful if your usage patterns vary for an extended time.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/get-azure-recommendation-collected.png" alt-text="Screenshot that shows performance data collected.":::

1. Go to your Azure SQL target section. With **Azure SQL Database (Preview)** selected, select **View details** to open the detailed SKU recommendation report. You can also select  **Save recommendation report** at the bottom of this pane for later analysis.

    :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/azure-sku-recommendation-zoom.png" alt-text="Screenshot that shows SKU recommendation details.":::

1. Close the recommendations pane. Select **Next** to continue with your migration.

## Configure migration settings

1. To set your target, select your subscription, location, and resource group in the corresponding dropdowns, and then select **Next**.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/configuration-azure-target-account.png" alt-text="Screenshot that shows Azure account details.":::
  
1. In the next section, select the target Azure SQL Database server (logical server) in the dropdown. Set the target username and password. Then, select **Connect** to verify the connectivity by using the credentials.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/configuration-azure-target-database.png" alt-text="Screenshot that shows Azure SQL Database details.":::

1. Map the source and target databases for the migration by using the dropdown for the Azure SQL Database target. Then, select **Next** to move to the next step in the migration wizard.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/configuration-azure-target-map.png" alt-text="Screenshot that shows source and target mapping.":::

1. For the migration mode, select **Offline migration**, and then select **Next**.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/migration-mode.png" alt-text="Screenshot that shows offline migrations selection.":::

1. Enter the source SQL Server credentials. Select **Edit** to select the list of tables to migrate between source and target.  

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/migration-source-credentials.png" alt-text="Screenshot that shows source SQL Server credentials.":::

1. Select the tables that you want to migrate to the target. The **Has rows** column indicates whether the target table has rows in the target database. You can select one or more tables.

In the example below, a text filter is applied to select only  tables that contain the word **Employee**. Select a list of tables based on your migration needs.

When you're ready, select **Update** to go to the next step.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/migration-source-tables.png" alt-text="Screenshot that shows table selection.":::

1. You can update the list of selected tables anytime before you start the migration. When you're ready to move to the next step in the migration wizard, select **Next**.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/migration-target-tables.png" alt-text="Screenshot that shows selected tables to migrate.":::

> [!NOTE]
> If no tables are selected or if a username and password aren't entered, the **Next** button isn't available to select.

## Create a Database Migration Service instance

1. Create a new instance of Azure Database Migration Service or reuse an existing instance that you created earlier.

   > [!NOTE]
   > If you previously created a Database Migration Service instance by using the Azure portal, you can't reuse the instance in the migration wizard in Azure Data Studio. You can reuse an instance only if you created the instance by using Azure Data Studio.

1. Select the resource group where you have an existing instance of Database Migration Service, or create a new resource group. The **Azure Database Migration Service** dropdown lists any existing instance of Database Migration Service in the selected resource group.

1. To reuse an existing instance of Database Migration Service, select the instance in the dropdown list. Then select **Next** to view the summary screen. When you're ready to start the migration, press **Start migration**.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/create-dms.png" alt-text="Screenshot that shows Database Migration Service selection.":::

1. To create a new instance of Database Migration Service, select **Create new**. In **Create Azure Database Migration Service**, enter a name for your Database Migration Service instance, and then select **Create**.  

1. After you successfully create a new Database Migration Service  instance, you're prompted to set up an integration runtime.

   Select **Download and install integration runtime** to open the download link in a web browser. Complete the download. Install the integration runtime on a machine that meets the prerequisites of connecting to the source SQL Server instance.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/create-dms-integration-runtime-details.png" alt-text="Screenshot that shows Integration runtime.":::

1. When installation is finished, Microsoft Integration Runtime Configuration Manager automatically opens to begin the registration process.  

1. Copy one of the authentication keys that are provided in the wizard and paste it in Azure Data Studio. If the authentication key is valid, a green check icon is displayed in Integration Runtime Configuration Manager. A green check indicates that you can continue to **Register**.  

1. When registering the self-hosted integration runtime is finished, close Microsoft Integration Runtime Configuration Manager and return to the migration wizard in Azure Data Studio.

   > [!NOTE]
   > For more information about how to use the self-hosted integration runtime, see [Create and configure a self-hosted integration runtime](../data-factory/create-self-hosted-integration-runtime.md).

1. In **Create Azure Database Migration Service** in Azure Data Studio, select **Test connection**  to validate that the newly created Database Migration Service instance is connected to the newly registered self-hosted integration runtime.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/create-dms-integration-runtime-connected.png" alt-text="Screenshot that shows IR connectivity test.":::

1. Review the summary and select **Start migration** to start the database migration.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/summary-start-migration.png" alt-text="Screenshot that shows start migration.":::

## Monitor your migration

1. In the Azure SQL Migration dashboard, go to the **Database Migration Status** section.

1. You can use the different options in the dashboard to track migrations that are in progress, completed, and failed (if any), or you can list all database migrations.  

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/monitor-migration-dashboard.png" alt-text="Screenshot that shows monitor migration dashboard.":::

1. Select **Database migrations in progress** to view ongoing migrations. To get more information about a specific migration, select the database name.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/monitor-migration-dashboard-details.png" alt-text="Screenshot that shows database migration details.":::

1. Database Migration Service returns the latest known migration status every time the migration status section is refreshed. Use the following table to learn more about the possible statuses:

    | Status | Description |
    |--------|-------------|
    |Preparing for copy| Disabling autostats, triggers, and indexes for target table. |
    |Copying| Data is being copied from source to target. |
    |Copy finished| Data copy has finished and is waiting on other tables to finish copying to begin final steps to return tables to original schema. |
    |Rebuilding indexes| Rebuilding indexes on target tables. |
    |Succeeded| All data is copied and the indexes are rebuilt. |

1. The migration details page displays the current status per database. As you can see from the following screenshot, the AdventureWorks2019 database migration has the status **Creating**.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/monitor-migration-dashboard-creating.png" alt-text="Screenshot that shows creating migration status.":::

1. Select **Refresh** to update the migration status. In the next screenshot, Database Migration Service has updated the migration status to **In progress**.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/monitor-migration-dashboard-in-progress.png" alt-text="Screenshot that shows migration in progress status.":::

1. Select the database name to open the table-level view. The upper section of this dashboard displays the current status of the migration. The lower section provides a detailed status of each table.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/monitor-migration-monitoring-panel-in-progress.png" alt-text="Screenshot that shows monitoring table migration.":::

1. After all table data is migrated to the Azure SQL Database (Preview) target, Database Migration Service updates the migration status from **In progress** to **Succeeded**.

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/monitor-migration-dashboard-succeeded.png" alt-text="Screenshot that shows succeeded status.":::

1. Select the database name to open the table-level view. The upper section of this dashboard displays the current status of the migration. The lower section displays information you can use to verify that all data is the same on both the source and the target (*rows read vs. rows copied*).

   :::image type="content" source="media/tutorial-sql-server-azure-sql-database-offline-ads/monitor-migration-monitoring-panel-succeeded.png" alt-text="Screenshot that shows succeeded migration.":::  

> [!NOTE]
> Database Migration Service optimizes migration by skipping tables with no data (0 rows). Tables that don't have data don't appear in the list, even if you select the tables when you create the migration.

You've completed the migration to Azure SQL Database. We encourage you to go through a series of post-migration tasks to ensure that everything functions smoothly and efficiently.

> [!IMPORTANT]
> Be sure to take advantage of the advanced cloud-based features offered by Azure SQL Database. The features include [built-in high availability](/azure/azure-sql/database/high-availability-sla), [threat detection](/azure/azure-sql/database/azure-defender-for-sql), and [monitoring and tuning your workload](/azure/azure-sql/database/monitor-tune-overview).

## Next steps

- Complete a tutorial that [creates an Azure SQL Database by using the Azure portal, PowerShell, and Azure CLI commands](/azure/azure-sql/database/single-database-create-quickstart).
- Learn more about [Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview).
- Learn how to [connect apps to Azure SQL Database](/azure/azure-sql/database/connect-query-content-reference-guide).
