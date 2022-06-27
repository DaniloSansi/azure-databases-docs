---
title: Quickstart - Azure Cosmos DB MongoDB API for .NET with MongoDB drier
description: Learn how to build a .NET app to manage Azure Cosmos DB MongoDB API account resources in this quickstart.
author: alexwolfmsft
ms.author: alexwolf
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/24/2022
ms.custom: devx-track-dotnet
---

# Quickstart: Azure Cosmos DB MongoDB API for .NET with MongoDB driver
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Get started with MongoDB to create databases, collections, and docs within your Cosmos DB resource. Follow these steps to  install the package and try out example code for basic tasks.

> [!NOTE]
> The [example code snippets](https://github.com/Azure-Samples/cosmos-db-mongodb-api-javascript-samples) are available on GitHub as a JavaScript project.

[MongoDB API reference documentation](https://docs.mongodb.com/drivers/node) | [MongoDB Package (NuGet)](https://www.npmjs.com/package/mongodb)

## Prerequisites

* An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free).
* [.NET 6.0](https://dotnet.microsoft.com/en-us/download)
* [Azure Command-Line Interface (CLI)](/cli/azure/) or [Azure PowerShell](/powershell/azure/)

### Prerequisite check

* In a terminal or command window, run ``dotnet --list-sdks`` to check that Node.js is one of the LTS versions.
* Run ``az --version`` (Azure CLI) or ``Get-Module -ListAvailable AzureRM`` (Azure PowerShell) to check that you have the appropriate Azure command-line tools installed.

## Setting up

This section walks you through creating an Azure Cosmos account and setting up a project that uses the MongoDB NuGet packages. 

### Create an Azure Cosmos DB account

This quickstart will create a single Azure Cosmos DB account using the MongoDB API.

#### [Azure CLI](#tab/azure-cli)

[!INCLUDE [Azure CLI - create resources](<./includes/azure-cli-create-resource-group-and-resource.md>)]

#### [PowerShell](#tab/azure-powershell)

[!INCLUDE [Powershell - create resource group and resources](<./includes/powershell-create-resource-group-and-resource.md>)]

#### [Portal](#tab/azure-portal)

[!INCLUDE [Portal - create resource](<./includes/portal-create-resource.md>)]

---

### Get MongoDB connection string

#### [Azure CLI](#tab/azure-cli)

[!INCLUDE [Azure CLI - get connection string](<./includes/azure-cli-get-connection-string.md>)]

#### [PowerShell](#tab/azure-powershell)

[!INCLUDE [Powershell - get connection string](<./includes/powershell-get-connection-string.md>)]

#### [Portal](#tab/azure-portal)

[!INCLUDE [Portal - get connection string](<./includes/portal-get-connection-string-from-resource.md>)]

---

### Create a new .NET app

Create a new .NET application in an empty folder using your preferred terminal. Use the [``dotnet new webapp``](/dotnet/core/tools/dotnet-newt) to create a new Razor Pages app. 

```console
dotnet new webapp -o <app-name>
```

### Install the NuGet package

Add the [MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver) NuGet package to the new .NET project. Use the [``dotnet add package``](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-add-package) command specifying the name of the NuGet package.

```console
dotnet add package MongoDb.Driver
```

### Configure environment variables

[!INCLUDE [Multi-tab](<./includes/environment-variables-connection-string.md>)]

## Object model

Before you start building the application, let's look into the hierarchy of resources in Azure Cosmos DB. Azure Cosmos DB has a specific object model used to create and access resources. The Azure Cosmos DB creates resources in a hierarchy that consists of accounts, databases, collections, and docs.

:::image type="complex" source="media/quickstart-javascript/resource-hierarchy.png" alt-text="Diagram of the Azure Cosmos D B hierarchy including accounts, databases, collections, and docs.":::
    Hierarchical diagram showing an Azure Cosmos D B account at the top. The account has two child database nodes. One of the database nodes includes two child collection nodes. The other database node includes a single child collection node. That single collection node has three child doc nodes.
:::image-end:::

You'll use the following MongoDB classes to interact with these resources:

- [``MongoClient``](https://mongodb.github.io/node-mongodb-native/4.5/classes/MongoClient.html) - This class provides a client-side logical representation for the MongoDB API layer on Cosmos DB. The client object is used to configure and execute requests against the service.
- [``Db``](https://mongodb.github.io/node-mongodb-native/4.5/classes/Db.html) - This class is a reference to a database that may, or may not, exist in the service yet. The database is validated server-side when you attempt to access it or perform an operation against it.
- [``Collection``](https://mongodb.github.io/node-mongodb-native/4.5/classes/Collection.html) - This class is a reference to a collection that also may not exist in the service yet. The collection is validated server-side when you attempt to work with it.

## Code examples

* [Authenticate the client](#authenticate-the-client)
* [Create a database](#create-a-database)
* [Create a container](#create-a-container)
* [Create an item](#create-an-item)
* [Get an item](#get-an-item)
* [Query items](#query-items)

The sample code described in this article creates a database named ``adventureworks`` with a collection named ``products``. The ``products`` collection is designed to contain product details such as name, category, quantity, and a sale indicator. Each product also contains a unique identifier.

### Authenticate the client

From the project directory, open the *Program.cs* file. In your editor, add a using directive for ``MongoDB.Driver``.

:::code language="csharp" source="~/azure-cosmos-dotnet-v3/001-quickstart/Program.cs" id="using_directives":::

Define a new instance of the ``MongoClient`` class using the constructor, and [``builder.Configuration.GetConnectionString``](/dotnet/api/microsoft.extensions.configuration.configurationextensions.getconnectionstring) to read the connection string you set earlier.

:::code language="csharp" source="~/azure-cosmos-dotnet-v3/001-quickstart/Program.cs" id="client_credentials" highlight="3-4":::

For more information on different ways to create a ``CosmosClient`` instance, see [Get started with Azure Cosmos DB SQL API and .NET](how-to-dotnet-get-started.md#connect-to-azure-cosmos-db-sql-api).

### Create a database

Use the [``MongoClient.GetDatabase``](https://mongodb.github.io/mongo-csharp-driver/2.16/apidocs/html/M_MongoDB_Driver_MongoClient_GetDatabase.htm) method to create a new database if it doesn't already exist. This method will return a reference to the existing or newly created database.

:::code language="csharp" source="~/azure-cosmos-mongodb-dotnet/001-quickstart/Program.cs" id="new_database" :::

### Create a collection

The [``MongoDatabase.GetCollection``](https://mongodb.github.io/mongo-csharp-driver/2.16/apidocs/html/M_MongoDB_Driver_MongoDatabase_GetCollection.htm) will create a new collection if it doesn't already exist. This method will also return a reference to the collection.

:::code language="csharp" source="~/azure-cosmos-mongodb-dotnet/001-quickstart/Program.cs" id="new_collection":::

### Create an item

The easiest way to create a new item in a collection is to create a C# [class](/dotnet/csharp/language-reference/keywords/class) or [record](/dotnet/csharp/language-reference/builtin-types/record) type with all of the members you want to serialize into JSON. In this example, the C# record has a unique identifier, a *category* field for the partition key, and extra *name*, *quantity*, and *sale* fields.

```csharp
public record Product(
    string Id,
    string Category,
    string Name,
    int Quantity,
    bool Sale
);
```

Create an item in the collection by calling [``IMongoCollection<TDocument>.InsertOne``](https://mongodb.github.io/mongo-csharp-driver/2.16/apidocs/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne_1.htm). 

:::code language="csharp" source="~/azure-cosmos-mongodb-dotnet/001-quickstart/Program.cs" id="new_item" :::

### Get an item

In Azure Cosmos DB, you can retrieve items by composing queries using Linq. In the SDK, call [``IMongoCollection.FindAsync<>``](https://mongodb.github.io/mongo-csharp-driver/2.16/apidocs/html/M_MongoDB_Driver_IMongoCollection_1_FindAsync__1.htm) and pass in a C# expression to filter the results.

:::code language="csharp" source="~/azure-cosmos-mongodb-dotnet/001-quickstart/Program.cs" id="read_item" :::

### Query multiple items

After you insert an item, you can run a query to get all items that match a specific filter by treating the collection as an `IQueryable`. This example uses an expression to filter products by category. Once the call to `AsQueryable`  is made, call [``MongoQueryable.Where``](https://mongodb.github.io/mongo-csharp-driver/2.16/apidocs/html/M_MongoDB_Driver_Linq_MongoQueryable_Where__1.htm) to get retrieve a set of filtered items.

:::code language="csharp" source="~/azure-cosmos-mongodb-dotnet/001-quickstart/Program.cs" id="query_item" :::

## Run the code

This app creates an Azure Cosmos MongoDb API database and collection. The example then creates an item and then reads the exact same item back. Finally, the example creates a second item and then performs a query that should returns multiple items. With each step, the example outputs metadata to the console about the steps it has performed.

To run the app, use a terminal to navigate to the application directory and run the application.

```dotnetcli
dotnet run
```

The output of the app should be similar to this example:

```output
Single product name: 
Yamba Surfboard
Multiple products:
Yamba Surfboard
Sand Surfboard
```

## Clean up resources

When you no longer need the Azure Cosmos DB SQL API account, you can delete the corresponding resource group.

### [Azure CLI / Resource Manager template](#tab/azure-cli+azure-resource-manager)

Use the [``az group delete``](/cli/azure/group#az-group-delete) command to delete the resource group.

```azurecli-interactive
az group delete --name $resourceGroupName
```

### [PowerShell](#tab/azure-powershell)

Use the [``Remove-AzResourceGroup``](/powershell/module/az.resources/remove-azresourcegroup) cmdlet to delete the resource group.

```azurepowershell-interactive
$parameters = @{
    Name = $RESOURCE_GROUP_NAME
}
Remove-AzResourceGroup @parameters
```

### [Portal](#tab/azure-portal)

1. Navigate to the resource group you previously created in the Azure portal.

    > [!TIP]
    > In this quickstart, we recommended the name ``msdocs-cosmos-quickstart-rg``.
1. Select **Delete resource group**.

   :::image type="content" source="media/delete-account-portal/delete-resource-group-option.png" lightbox="media/delete-account-portal/delete-resource-group-option.png" alt-text="Screenshot of the Delete resource group option in the navigation bar for a resource group.":::

1. On the **Are you sure you want to delete** dialog, enter the name of the resource group, and then select **Delete**.

   :::image type="content" source="media/delete-account-portal/delete-confirmation.png" lightbox="media/delete-account-portal/delete-confirmation.png" alt-text="Screenshot of the delete confirmation page for a resource group.":::

---

## Next steps

In this quickstart, you learned how to create an Azure Cosmos DB SQL API account, create a database, and create a container using the .NET SDK. You can now dive deeper into the SDK to import more data, perform complex queries, and manage your Azure Cosmos DB SQL API resources.

> [!div class="nextstepaction"]
> [Get started with Azure Cosmos DB SQL API and .NET](how-to-dotnet-get-started.md)
