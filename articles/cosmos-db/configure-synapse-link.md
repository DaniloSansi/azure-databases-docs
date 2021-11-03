---
title: Configure and use Azure Synapse Link for Azure Cosmos DB
description: Learn how to enable Synapse link for Azure Cosmos DB accounts, create a container with analytical store enabled, connect the Azure Cosmos database to Synapse workspace, and run queries. 
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/12/2021
ms.author: rosouz
ms.custom: references_regions, synapse-cosmos-db, devx-track-azurepowershell
---

# Configure and use Azure Synapse Link for Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse Link for Azure Cosmos DB](synapse-link.md) is a cloud-native hybrid transactional and analytical processing (HTAP) capability that enables you to run near real-time analytics over operational data in Azure Cosmos DB. Synapse Link creates a tight seamless integration between Azure Cosmos DB and Azure Synapse Analytics.

Azure Synapse Link is available for Azure Cosmos DB SQL API or for Azure Cosmos DB API for Mongo DB accounts. Use the following steps to run analytical queries with the Azure Synapse Link for Azure Cosmos DB:

* [Enable Azure Synapse Link for your Azure Cosmos DB accounts](#enable-synapse-link)
* [Create an analytical store enabled container](#create-analytical-ttl)
* [Enable analytical store for an existing container](#update-analytical-ttl)
* [Optional - Update analytical store ttl for an container](#update-analytical-ttl)
* [Connect your Azure Cosmos database to an Azure Synapse workspace](#connect-to-cosmos-database)
* [Query the analytical store using Azure Synapse Spark Pool](#query-analytical-store-spark)
* [Query the analytical store using Azure Synapse serverless SQL pool](#query-analytical-store-sql-on-demand)
* [Use Azure Synapse serverless SQL pool to analyze and visualize data in Power BI](#analyze-with-powerbi)

You can also checkout the learn module on how to [configure Azure Synapse Link for Azure Cosmos DB.](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/)

## <a id="enable-synapse-link"></a>Enable Azure Synapse Link for Azure Cosmos DB accounts

> [!NOTE]
> If you want to use customer-managed keys with Azure Synapse Link, you must configure your account's managed identity in your Azure Key Vault access policy before enabling Synapse Link on your account. To learn more, see how to [Configure customer-managed keys using Azure Cosmos DB accounts' managed identities](how-to-setup-cmk.md#using-managed-identity) article.

> [!NOTE]
> If you want to use Full Fidelity Schema for SQL (CORE) API accounts, you can't use the Azure portal to enable Synapse Link. This option can't be changed after Synapse Link is enabled in your account and to set it you must use Azure CLI or PowerShell. For more information, check [analytical store schema representation documentation](analytical-store-introduction.md#schema-representation). 

### Azure portal

1. Sign into the [Azure portal](https://portal.azure.com/).

1. [Create a new Azure account](create-sql-api-dotnet.md#create-account), or select an existing Azure Cosmos DB account.

1. Navigate to your Azure Cosmos DB account and open the **Features** pane.

1. Select **Synapse Link** from the features list.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Find Synapse Link feature":::

1. Next it prompts you to enable synapse link on your account. Select **Enable**. This process can take 1 to 5 minutes to complete.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Enable Synapse Link feature":::

1. Your account is now enabled to use Synapse Link. Next see how to create analytical store enabled containers to automatically start replicating your operational data from the transactional store to the analytical store.

> [!NOTE]
> Turning on Synapse Link does not turn on the analytical store automatically. Once you enable Synapse Link on the Cosmos DB account, enable analytical store on containers to start using Synapse Link. 

### Command Line Tools

Enable Synapse Link in your Cosmos DB SQL API or MongoDB API account using Azure CLI or PowerShell.

#### Azure CLI

Use `--enable-analytical-storage true` for all accounts, in both **create** or **update** operations. If the API type is SQL, you can use `--analytical-storage-schema-type` with the values `FullFidelity` or `WellDefined`. For MongoDB API accounts, use `--analytical-storage-schema-type FullFidelity`.

* [Create a new Azure Cosmos DB account with Synapse Link enabled](/cli/azure/cosmosdb#az_cosmosdb_create-optional-parameters)
* [Update an existing Azure Cosmos DB account to enable Synapse Link](/cli/azure/cosmosdb#az_cosmosdb_update-optional-parameters)

#### PowerShell

Use `-EnableAnalyticalStorage  true` for all accounts, in both **create** or **update** operations. If the API type is SQL, you can use `-AnalyticalStorageSchemaType` with the values `FullFidelity` or `WellDefined`. For MongoDB API accounts, use `-AnalyticalStorageSchemaType FullFidelity`.


* [Create a new Azure Cosmos DB account with Synapse Link enabled](/powershell/module/az.cosmosdb/new-azcosmosdbaccount#description)
* [Update an existing Azure Cosmos DB account to enable Synapse Link](/powershell/module/az.cosmosdb/update-azcosmosdbaccount)


## <a id="create-analytical-ttl"></a> Create an analytical store enabled container

You can turn on analytical store on an Azure Cosmos DB container while creating the container. You can use the Azure portal or configure the `analyticalTTL` property during container creation by using the Azure Cosmos DB SDKs or command line tools.

### Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com/) or the [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Navigate to your Azure Cosmos DB account and open the **Data Explorer** tab.

1. Select **New Container** and enter a name for your database, container, partition key and throughput details. Turn on the **Analytical store** option. After you enable the analytical store, it creates a container with `AnalyicalTTL` property set to the default value of  -1 (infinite retention). This analytical store that retains all the historical versions of records.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Turn on analytical store for Azure Cosmos DB container":::

1. If you have previously not enabled Synapse Link on this account, it will prompt you to do so because it's a pre-requisite to create an analytical store enabled container. If prompted, select **Enable Synapse Link**. This process can take 1 to 5 minutes to complete.

1. Select **OK**, to create an analytical store enabled Azure Cosmos DB container.

1. After the container is created, verify that analytical store has been enabled by clicking **Settings**, right below Documents in Data Explorer, and check if the **Analytical Store Time to Live** option is turned on.

### .NET SDK

The following code creates a container with analytical store by using the .NET SDK. Set the analytical TTL property to the required value. For the list of allowed values, see the [analytical TTL supported values](analytical-store-introduction.md#analytical-ttl) article:

```csharp
// Create a container with a partition key, and analytical TTL configured to -1 (infinite retention)
ContainerProperties properties = new ContainerProperties()
{
    Id = "myContainerId",
    PartitionKeyPath = "/id",
    AnalyticalStoreTimeToLiveInSeconds = -1,
};
CosmosClient cosmosClient = new CosmosClient("myConnectionString");
await cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(properties);
```

### Java V4 SDK

The following code creates a container with analytical store by using the Java V4 SDK. Set the `AnalyticalStoreTimeToLiveInSeconds` property to the required value:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### Python V4 SDK

Python 2.7 and Azure Cosmos DB SDK 4.1.0 are the minimum versions required, and the SDK is only compatible with the SQL API.

The first step is to make sure that you are using at least version 4.1.0 of the [Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos):

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
The next step creates a container with analytical store by using the Azure Cosmos DB Python SDK:

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```
### Command Line Tools

Create analytical store enabled containers by setting `analytical ttl`. For information on the various Analytical TTL config options, see the [analytical TTL supported values](analytical-store-introduction.md#analytical-ttl) article.

#### Azure CLI

* [Create an Azure Cosmos DB MongoDB collection](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create-examples)
* [Create an Azure Cosmos DB SQL API container](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create) 

#### PowerShell

* [Create an Azure Cosmos DB MongoDB collection](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection#description)
* [Create an Azure Cosmos DB SQL API container](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)


## <a id="update-analytical-ttl"></a> Enable analytical store for an existing container

You can turn analytical store on for existing containers in Cosmos DB SQL API accounts only. Please note these important information about the process.

* The initial sync with analytical store total time will vary depending on your data volume and on the complexity of the documents.
* The throughput of your container or database account also influences the total sync time. Although RU/s are not used in this migration, the total RU/s available influences the performance of the sync process.
* You won't be able to query analytical store of an existing container until the end of the initial sync process.
 
> [!NOTE]
> Due to short-term capacity constraints, you need to register to enable Synapse Link on your existing containers. Depending on the pending requests, approving this request may take anywhere from a day to a week. Please come back here to check the status. If you have any issues or questions, please reach out to [cosmosdbsynapselink@microsoft.com](mailto:cosmosdbsynapselink@microsoft.com).


> [!NOTE]
> Currently it is not possible to turn off analytical store from a container. Click [here](analytical-store-introduction.md#analytical-store-pricing) for more information about analytical store pricing.

### Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com/) or the [Azure Cosmos DB Explorer](https://cosmos.azure.com/).
1. Navigate to your Azure Cosmos DB account and open the **Power Bi" / Enable Synapse Link"** tab in the **Integrations** section.
1. Click **Register** to request approval for your subscription. To see the status of request, please come back to this same portal pane. 
1. When approved, you will see your account’s containers list and you will be able to select those that will have analytical store enabled.
1. Optionally, you can click **Next** to create power BI dashboards on these containers.


### Command Line Tools

Create analytical store enabled containers by setting `analytical ttl` to `-1`. For information on the various Analytical TTL config options, see the [analytical TTL supported values](analytical-store-introduction.md#analytical-ttl) article. Please check that the registration request is also necessary for Azure CLI and PowerShell.

### Azure CLI

* [Register for approval](https://docs.microsoft.com/cli/azure/feature/registration?view=azure-cli-latest) using `az feature registration create --namespace Microsoft.DocumentDB --name AnalyticalStoreMigration `.
* [Check the request status](https://docs.microsoft.com/cli/azure/feature/registration?view=azure-cli-latest#az_feature_registration_show).
* [Update Analytical ttl](https://docs.microsoft.com/cli/azure/cosmosdb/sql/container?view=azure-cli-latest#az_cosmosdb_sql_container_update) to `-1` after the request approval.

### PowerShell

The following links show how to update containers analytical TTL by using PowerShell:

* [Register for approval](https://docs.microsoft.com/powershell/module/az.resources/register-azproviderfeature) using `Register-AzProviderFeature -ProviderName "Microsoft.DocumentDB" -FeatureName "AnalyticalStoreMigration"`.
* [Check the request status](https://docs.microsoft.com/powershell/module/az.resources/get-azproviderfeature).
* [Update Analytical ttl](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer) to `-1` after the request approval.


## <a id="update-analytical-ttl"></a> Optional - Update the analytical store time to live

After the analytical store is enabled with a particular TTL value, you may want to update it to a different valid value later. You can update the value by using the Azure portal, Azure CLI, PowerShell, or Cosmos DB SDKs. For information on the various Analytical TTL config options, see the [analytical TTL supported values](analytical-store-introduction.md#analytical-ttl) article.


### Azure portal

If you created an analytical store enabled container through the Azure portal, it contains a default analytical TTL of -1. Use the following steps to update this value:

1. Sign in to the [Azure portal](https://portal.azure.com/) or the [Azure Cosmos DB Explorer](https://cosmos.azure.com/).
1. Navigate to your Azure Cosmos DB account and open the **Data Explorer** tab.
1. Select an existing container that has analytical store enabled. Expand it and modify the following values:
   1. Open the **Scale & Settings** window.
   1. Under **Setting** find, **Analytical Storage Time to Live**.
   1. Select **On (no default)** or select **On** and set a TTL value.
   1. Click **Save** to save the changes.


### .NET SDK

The following code shows how to update the TTL for analytical store by using the .NET SDK:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### Java V4 SDK

The following code shows how to update the TTL for analytical store by using the Java V4 SDK:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

### Python V4 SDK

Currently not supported.


### Azure CLI

The following links show how to update containers analytical TTL by using Azure CLI:

* [Azure Cosmos DB API for Mongo DB](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_update)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_update)

### PowerShell

The following links show how to update containers analytical TTL by using PowerShell:

* [Azure Cosmos DB API for Mongo DB](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollection)
* [Azure Cosmos DB SQL API](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer)


## <a id="connect-to-cosmos-database"></a> Connect to a Synapse workspace

Use the instructions in [Connect to Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) on how to access an Azure Cosmos DB database from Azure Synapse Analytics Studio with Azure Synapse Link.

## <a id="query-analytical-store-spark"></a> Query analytical store using Apache Spark for Azure Synapse Analytics

Use the instructions in the [Query Azure Cosmos DB analytical store using Spark 3](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark-3.md) article on how to query with Synapse Spark 3. That article gives some examples on how you can interact with the analytical store from Synapse gestures. Those gestures are visible when you right-click on a container. With gestures, you can quickly generate code and tweak it to your needs. They are also perfect for discovering data with a single click.

For Spark 2 integration use the instruction in the [Query Azure Cosmos DB analytical store using Spark 2](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) article.

## <a id="query-analytical-store-sql-on-demand"></a> Query the analytical store using serverless SQL pool in Azure Synapse Analytics

Serverless SQL pool allows you to query and analyze data in your Azure Cosmos DB containers that are enabled with Azure Synapse Link. You can analyze data in near real-time without impacting the performance of your transactional workloads. It offers a familiar T-SQL syntax to query data from the analytical store and integrated connectivity to a wide range of BI and ad-hoc querying tools via the T-SQL interface. To learn more, see the [Query analytical store using serverless SQL pool](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) article.

## <a id="analyze-with-powerbi"></a>Use serverless SQL pool to analyze and visualize data in Power BI

You can create a serverless SQL pool database and views over Synapse Link for Azure Cosmos DB. Later you can query the Azure Cosmos DB containers and then build a model with Power BI over those views to reflect that query. There is no performance or cost impact to your transactional workloads, and no complexity of managing ETL pipelines. You can use either [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) or [import](/power-bi/connect-data/service-dataset-modes-understand#import-mode) modes. To learn more, see how to use [Serverless SQL pool to analyze Azure Cosmos DB data with Synapse Link](synapse-link-power-bi.md) article.


## Azure Resource Manager template

The [Azure Resource Manager template](./manage-with-templates.md#azure-cosmos-account-with-analytical-store) creates a Synapse Link enabled Azure Cosmos DB account for SQL API. This template creates a Core (SQL) API account in one region with a container configured with analytical TTL enabled, and an option to use manual or autoscale throughput. To deploy this template, click on **Deploy to Azure** on the readme page.

## <a id="cosmosdb-synapse-link-samples"></a> Getting started with Azure Synapse Link - Samples

You can find samples to get started with Azure Synapse Link on [GitHub](https://aka.ms/cosmosdb-synapselink-samples). These showcase end-to-end solutions with IoT and retail scenarios. You can also find the samples corresponding to Azure Cosmos DB API for MongoDB in the same repo under the [MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) folder. 

## Next steps

To learn more, see the following docs:

* Checkout the learn module on how to [configure Azure Synapse Link for Azure Cosmos DB.](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/)

* [Azure Cosmos DB analytical store overview.](analytical-store-introduction.md)

* [Frequently asked questions about Synapse Link for Azure Cosmos DB.](synapse-link-frequently-asked-questions.yml)

* [Apache Spark in Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Serverless SQL pool runtime support in Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
