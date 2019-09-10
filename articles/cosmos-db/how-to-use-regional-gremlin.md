---
title: Regional end-points for Azure Cosmos DB Graph database
description: Learn how to connect to nearest Graph database end-point for your application
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/09/2019
ms.author: olignat
---

# Regional end-points for Azure Cosmos DB Graph account
Azure Cosmos DB Graph database is [globally distributed](distribute-data-globally.md). Applications can use multiple read end-points if their graph is replicated to multiple Azure regions. Applications that need write access in multiple geographic locations can enable [multi-master](how-to-multi-master.md) capability.

Reasons to choose more than one region:
1. **Horizontal read scalability** - as application load increases it may be prudent to route read traffic to different Azure regions.
2. **Lower latency** - to reduce network latency overhead of each traversal it is recommended to route read and write traffic to the nearest Azure region.

Note that **data residency** requirement is achieved by setting Azure Resource Manager policy on Cosmos DB account. Customer can limit regions into which Cosmos DB replicates data.

## Traffic routing

Cosmos DB Graph database engine is running in multiple regions, each of which is comprised of multiple clusters, that are hosted on hundreds of machines. Cosmos DB Graph account DNS CNAME *accountname.gremlin.cosmos.azure.com* resolves to DNS A record of a cluster. A single IP address of a load-balancer hides internal cluster topology.

A regional DNS CNAME record is created for every region of Cosmos DB Graph account. Format of regional end-point is *accountname-region.gremlin.cosmos.azure.com*. Region segment of regional end-point is obtained by removing all spaces from [public Azure region](https://azure.microsoft.com/global-infrastructure/regions) name. For example, `"East US 2"` region for `"contoso"` global database account would have a DNS CNAME *contoso-eastus2.gremlin.cosmos.azure.com*

TinkerPop Gremlin client is designed to work with a single server. To connect Gremlin SDK to Cosmos DB Graph database application can use global writable DNS CNAME for read and write traffic. Region-aware applications should use regional end-point for read traffic. Use regional end-point for write traffic only if specific region is configured to accept writes, like in case of multi-master account. 

> [!NOTE]
> Cosmos DB Graph engine can accept write operation in read region by proxying traffic to write region. It is not recommended to send writes into read-only region as it increases traversal latency and is subject to restrictions in the future.

Global database account CNAME always points to a valid write region. During server-side failover of write region, Cosmos DB updates global database account CNAME to point to new region. If application is not instrumented to handle traffic re-routing after failover it is recommended to use global database account DNS CNAME.

> [!NOTE]
> Cosmos DB does not route traffic based on geographic proximity of the caller. It is up to each application to select the right region according to unique application needs.

## Portal end-point discovery

The easiest way to get the list of regions for Azure Cosmos DB Graph account is overview blade in Azure Portal. This approach is recommended for applications that do not change regions often, or have a way to update the list via application configuration.

![Retrieve regions of Cosmos DB Graph account from the portal](./media/how-to-use-regional-gremlin/get-end-point-portal.png )

Example below demonstrates general principles of accessing regional Gremlin end-point. Application should consider number of regions to send the traffic to and number of corresponding Gremlin clients to instantiate.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
```

## SDK end-point discovery

Application can use [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) to discover read and write locations for Graph account. Keep in mind that these locations can change at any time through manual reconfiguration on the server side or automatic failover.

There is no API to discover regional end-points via TinkerPop Gremlin SDK. Applications that need runtime end-point discovery need to host 2 separate SDKs in the process space.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional end-points for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## Next steps
* [How to manage database accounts control](how-to-manage-database-account.md) in Azure Cosmos DB
* [High availability](high-availability.md) in Azure Cosmos DB
* [Global distribution with Azure Cosmos DB - under the hood](global-dist-under-the-hood.md)
* [Azure CLI Samples](cli-samples.md) for Azure Cosmos DB
