---
title: LEFT (Azure Cosmos DB)
description: Learn about SQL system function LEFT in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
---
# LEFT (Azure Cosmos DB)
 Returns the left part of a string with the specified number of characters.  
  
## Syntax
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
## Arguments
  
*str_expr*  
   Is any valid string expression.  
  
*num_expr*  
   Is any valid numeric expression.  
  
## Return Types
  
  Returns a string expression.  
  
## Examples
  
  The following example returns the left part of "abc" for various length values.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Here is the result set.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  

## See Also
