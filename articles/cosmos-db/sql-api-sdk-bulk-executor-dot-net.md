---
title: 'Azure Cosmos DB: Bulk Executor .NET API, SDK & resources | Microsoft Docs'
description: Learn all about the Bulk Executor .NET API and SDK including release dates, retirement dates, and changes made between each version of the Azure Cosmos DB Bulk Executor .NET SDK.
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun

ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris

---

# .NET Bulk Executor library: Download information 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [Bulk Executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Description**</td><td>The Bulk Executor library allows client applications to perform bulk operations in Azure Cosmos DB accounts. Bulk Executor library provides BulkImport, BulkUpdate and BulkDelete namespaces. The BulkImport module can bulk ingest documents in an optimized way such that the throughput provisioned for a collection is consumed to its maximum extent. The BulkUpdate module can bulk update existing data in Azure Cosmos DB containers as patches. The BulkDelete module can bulk delete documents in an optimized way such that the throughput provisioned for a collection is consumed to its maximum extent.</td></tr>

<tr><td>**SDK download**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**BulkExecutor library in GitHub**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**API documentation**</td><td>[.Net API reference documentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Get started**</td><td>[Get started with the Bulk Executor library .NET SDK](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Current supported framework**</td><td>Microsoft .NET Framework 4.5.2 and .NET Standard 2.0 </td></tr>
</table></br>

## Release notes

### <a name="2.0.0-preview"/>2.0.0-preview

* Added the support of .NET Standard 2.0 as one of the target framework to make BulkExecutor work with .NET Core applications.

### <a name="1.3.0"/>1.3.0

* Added an overload for BulkDelete operation for SQL API accounts to accept partition key, document id tuples to delete.
* Added an overload for BulkDelete operation for SQL API accounts to accept RequestOptions containing the partition key to delete.
* Fixed an issue which caused a formatting issue in the user agent used by BulkExecutor.

### <a name="1.2.0"/>1.2.0

* Fixed an issue which caused BulkExecutor to throw internal exceptions resulting in incomplete import and update operations.

### <a name="1.1.2"/>1.1.2

* Bumped up the DocumentDB .NET SDK dependency to version 2.1.3.

### <a name="1.1.1"/>1.1.1

* Fixed an issue which caused BulkExecutor to throw JSRT error while importing to fixed collections.

### <a name="1.1.0"/>1.1.0

* Added support for BulkDelete operation for Azure Cosmos DB SQL API accounts.
* Added support for BulkImport operation for Azure Cosmos DB MongoDB API accounts.
* Bumped up the DocumentDB .NET SDK dependency to version 2.0.0. 

### <a name="1.0.2"/>1.0.2

* Added support for BulkImport operation for Azure Cosmos DB Gremlin API accounts.

### <a name="1.0.1"/>1.0.1

* Minor bug fix to the BulkImport operation for Azure Cosmos DB SQL API accounts.

### <a name="1.0.0"/>1.0.0

* Added support for BulkImport and BulkUpdate operations for Azure Cosmos DB SQL API accounts.
