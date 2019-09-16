---
title: LENGTH (Azure Cosmos DB)
description: Learn about SQL system function LENGTH in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
---
# LENGTH (Azure Cosmos DB)
 Returns the number of characters of the specified string expression.  
  
## Syntax
  
```  
LENGTH(<str_expr>)  
```  
  
## Arguments
  
*str_expr*  
   Is any valid string expression.  
  
## Return Types
  
  Returns a string expression.  
  
## Examples
  
  The following example returns the length of a string.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 Here is the result set.  
  
```  
[{"len": 3}]  
```  
  

## See Also
