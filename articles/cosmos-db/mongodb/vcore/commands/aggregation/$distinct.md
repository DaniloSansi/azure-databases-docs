---
  title: Distinct command usage in Azure Cosmos DB for MongoDB vCore
  titleSuffix: Azure Cosmos DB for MongoDB vCore
  description: The distinct command is used to find the unique values for a specified field across a single collection.
  author: avijitgupta
  ms.author: avijitgupta
  ms.service: azure-cosmos-db
  ms.subservice: mongodb-vcore
  ms.topic: reference
  ms.date: 09/12/2024
---

# distinct

[!INCLUDE[MongoDB (vCore)](~/reusable-content/ce-skilling/azure/includes/cosmos-db/includes/appliesto-mongodb-vcore.md)]

The `distinct` command is used to find the unique values for a specified field across a single collection. This command is particularly useful when you need to identify the set of distinct values for a field without retrieving all the documents or when you need to perform operations like filtering or grouping based on unique values.

## Syntax

The basic syntax of the `distinct` command is as follows:

```javascript
db.collection.distinct(field, query, options)
```

- `field`: The field for which to return the distinct values.
- `query`: Optional. A query that specifies the documents from which to retrieve the distinct values.
- `options`: Optional. Additional options for the command.

## Examples

Below are examples using the provided sample JSON structure.

### Example 1: Find distinct categories in sales

To find the distinct `categoryName` in the `salesByCategory` array:

```javascript
db.stores.distinct("sales.salesByCategory.categoryName")
```

### Example 2: Find distinct event names in promotion events

To find the distinct `eventName` in the `promotionEvents` array:

```javascript
db.stores.distinct("promotionEvents.eventName")
```

### Example 3: Find distinct discount percentages for a specific event

To find the distinct `discountPercentage` in the `discounts` array for the "Summer Sale" event:

```javascript
db.stores.distinct("promotionEvents.discounts.discountPercentage", { "promotionEvents.eventName": "Incredible Discount Days" })
```

## Related content

[!INCLUDE[Related content](../includes/related-content.md)]
