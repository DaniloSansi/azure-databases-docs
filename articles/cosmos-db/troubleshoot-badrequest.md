---
title: Troubleshoot Azure Cosmos DB bad request exceptions
description: Learn how to diagnose and fix bad request exceptions such as input content or partition key is invalid, partition key doesn't match in Azure Cosmos DB.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
---

# Diagnose and troubleshoot bad request exceptions in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

The HTTP status code 400 represents the request contains invalid data or it's missing required parameters.

## <a name="missing-id-property"></a>Missing the ID property
On this scenario, it's common to see the error:

```
The input content is invalid because the required properties - 'id; ' - are missing
```

### Solution
This error means the JSON document that is being sent to the service is lacking the required ID property. Specify an ID property with a string value as per the [REST specification](/rest/api/cosmos-db/documents), the SDKs do not autogenerate values for this property.

## <a name="invalid-partition-key-type"></a>Invalid partition key type
On this scenario, it's common to see errors like:

```
Partition key ... is invalid.
```

### Solution
This error means the value provided as partition key is invalid. The value of the partition key should be a string or a number.

## <a name="wrong-partition-key-value"></a>Wrong partition key value
On this scenario, it's common to see the error:

```
PartitionKey extracted from document doesn’t match the one specified in the header
```

### Solution
This error means you are executing an operation and passing a partition key value that does not match the document's body value for the expected property. If the collection's partition key path is `/myPartitionKey`, the document has a property called `myPartitionKey` with a value that does not match what was provided as partition key value when calling the SDK method. Make sure to correct it and execute the request.

## Next steps
* [Diagnose and troubleshoot](troubleshoot-dot-net-sdk.md) issues when you use the Azure Cosmos DB .NET SDK.
* Learn about performance guidelines for [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) and [.NET v2](performance-tips.md).
* [Diagnose and troubleshoot](troubleshoot-java-sdk-v4-sql.md) issues when you use the Azure Cosmos DB Java v4 SDK.
* Learn about performance guidelines for [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
