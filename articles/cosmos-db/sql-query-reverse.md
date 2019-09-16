---
title: REVERSE (Azure Cosmos DB)
description: Learn about SQL system function REVERSE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
---
# REVERSE (Azure Cosmos DB)
 Returns the reverse order of a string value.  
  
## Syntax
  
```  
REVERSE(<str_expr>)  
```  
  
## Arguments
  
*str_expr*  
   Is any valid string expression.  
  
## Return Types
  
  Returns a string expression.  
  
## Examples
  
  The following example shows how to use REVERSE in a query.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 Here is the result set.  
  
```  
[{"reverse": "cbA"}]  
```  
  

## See Also
