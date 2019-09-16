---
title: TAN (Azure Cosmos DB)
description: Learn about SQL system function TAN in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
---
# TAN (Azure Cosmos DB)
 Returns the tangent of the specified angle, in radians, in the specified expression.  
  
## Syntax
  
```  
TAN (<numeric_expression>)  
```  
  
## Arguments
  
*numeric_expression*  
   Is a numeric expression.  
  
## Return Types
  
  Returns a numeric expression.  
  
## Examples
  
  The following example calculates the tangent of PI()/2.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 Here is the result set.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  

## See Also
