---
title: REPLACE (Azure Cosmos DB)
description: Learn about SQL system function REPLACE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
---
# REPLACE (Azure Cosmos DB)
 Replaces all occurrences of a specified string value with another string value.  
  
## Syntax
  
```sql
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
## Arguments
  
*str_expr*  
   Is any valid string expression.  
  
## Return Types
  
  Returns a string expression.  
  
## Examples
  
  The following example shows how to use REPLACE in a query.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Here is the result set.  
  
```json
[{"replace": "This is a desk"}]  
```  

## See Also

- [String functions Azure Cosmos DB](sql-query-string-functions.md)
- [System functions Azure Cosmos DB](sql-query-system-functions.md)
- [Introduction to Azure Cosmos DB](introduction.md)
