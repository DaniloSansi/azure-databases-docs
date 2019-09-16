---
title: REPLICATE (Azure Cosmos DB)
description: Learn about SQL system function REPLICATE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
---
# REPLICATE (Azure Cosmos DB)
 Repeats a string value a specified number of times.  
  
## Syntax
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
## Arguments
  
*str_expr*  
   Is any valid string expression.  
  
*num_expr*  
   Is any valid numeric expression. If num_expr is negative or non-finite, the result is undefined.

  > [!NOTE]
  > The maximum length of the result is 10,000 characters i.e. (length(str_expr)  *  num_expr) <= 10,000.
  
## Return Types
  
  Returns a string expression.  
  
## Examples
  
  The following example shows how to use REPLICATE in a query.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 Here is the result set.  
  
```  
[{"replicate": "aaa"}]  
```  
  

## See Also
