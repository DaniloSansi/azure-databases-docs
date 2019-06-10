---
title: SELECT clause
description: Learn about SQL SELECT clause for Azure Cosmos DB. Use SQL as an Azure Cosmos DB JSON query language.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: girobins
---

# <a id="SelectClause"></a>SELECT clause

Every query consists of a SELECT clause and optional FROM and WHERE clauses, per ANSI SQL standards. Typically, the source in the FROM clause is enumerated, and the WHERE clause applies a filter on the source to retrieve a subset of JSON items. The SELECT clause then projects the requested JSON values in the select list.

##  <a name="bk_syntax"></a> Syntax

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
##  <a name="bk_arguments"></a> Arguments
  
- `<select_specification>`  

  Properties or value to be selected for the result set.  
  
- `'*'`  

  Specifies that the value should be retrieved without making any changes. Specifically if the processed value is an object, all properties will be retrieved.  
  
- `<object_property_list>`  
  
  Specifies the list of properties to be retrieved. Each returned value will be an object with the properties specified.  
  
- `VALUE`  

  Specifies that the JSON value should be retrieved instead of the complete JSON object. This, unlike `<property_list>` does not wrap the projected value in an object.  
 
- `DISTINCT`
  
  Specifies that duplicates of projected properties should be removed.  

- `<scalar_expression>`  

  Expression representing the value to be computed. See [Scalar expressions](sql-query-scalar-expressions) section for details.  

##  <a name="bk_remarks"></a> Remarks

  
The `SELECT *` syntax is only valid if FROM clause has declared exactly one alias. `SELECT *` provides an identity projection, which can be useful if no projection is needed. SELECT * is only valid if FROM clause is specified and introduced only a single input source.  
  
Both `SELECT <select_list>` and `SELECT *` are "syntactic sugar" and can be alternatively expressed by using simple SELECT statements as shown below.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   is equivalent to:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   is equivalent to:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
##  <a name="bk_examples"></a> Examples

The following SELECT query example returns `address` from `Families` whose `id` matches `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

The results are:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a id="EscapingReservedKeywords"></a>Quoted property accessor
You can access properties using the quoted property operator []. For example, `SELECT c.grade` and `SELECT c["grade"]` are equivalent. This syntax is useful to escape a property that contains spaces, special characters, or has the same name as a SQL keyword or reserved word.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### Nested properties

The following example projects two nested properties, `f.address.state` and `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

The results are:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### JSON expressions

Projection also supports JSON expressions, as shown in the following example:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

The results are:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

In the preceding example, the SELECT clause needs to create a JSON object, and since the sample provides no key, the clause uses the implicit argument variable name `$1`. The following query returns two implicit argument variables: `$1` and `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

The results are:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

 
## <a id="References"></a>References

- [Azure Cosmos DB SQL specification](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Javascript Specification](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz. [Query evaluation techniques for large databases](https://dl.acm.org/citation.cfm?id=152611). *ACM Computing Surveys* 25, no. 2 (1993).
- Graefe, G. "The Cascades framework for query optimization." *IEEE Data Eng. Bull.* 18, no. 3 (1995).
- Lu, Ooi, Tan. "Query Processing in Parallel Relational Database Systems." *IEEE Computer Society Press* (1994).
- Olston, Christopher, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, and Andrew Tomkins. "Pig Latin: A Not-So-Foreign Language for Data Processing." *SIGMOD* (2008).

## Next steps

- [Introduction to Azure Cosmos DB][introduction]
- [Azure Cosmos DB .NET samples](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB consistency levels][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md