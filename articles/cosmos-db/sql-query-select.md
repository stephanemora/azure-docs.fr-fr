---
title: Clause SELECT dans Azure Cosmos DB
description: Découvrez la clause SQL SELECT pour Azure Cosmos DB. Utilisez SQL comme langage de requête JSON Azure Cosmos DB.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: d34b1c39d9789409dc365cd4cf07fdc3d5a780fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003527"
---
# <a name="select-clause"></a>Clause SELECT

Chaque requête se compose d’une clause SELECT et de clauses [FROM](sql-query-from.md) et [WHERE](sql-query-where.md) facultatives, conformes aux normes ANSI-SQL. En règle générale, la source dans la clause FROM est énumérée, tandis que la clause WHERE applique un filtre sur la source pour récupérer un sous-ensemble d’éléments JSON. Ensuite, la clause SELECT projette les valeurs JSON demandées dans la liste sélectionnée.

## <a name="syntax"></a>Syntaxe

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Arguments
  
- `<select_specification>`  

  Propriétés ou valeur à sélectionner pour le jeu de résultats.  
  
- `'*'`  

  Spécifie que la valeur doit être récupérée sans apporter de modifications. Plus spécifiquement, si la valeur traitée est un objet, toutes les propriétés sont récupérées.  
  
- `<object_property_list>`  
  
  Spécifie la liste des propriétés à récupérer. Chaque valeur retournée sera un objet avec les propriétés spécifiées.  
  
- `VALUE`  

  Spécifie que la valeur JSON doit être récupérée au lieu de l’objet JSON complet. Cela, contrairement à `<property_list>`, n’encapsule pas la valeur projetée dans un objet.  
 
- `DISTINCT`
  
  Spécifie que les doublons de propriétés projetées doivent être supprimés.  

- `<scalar_expression>`  

  Expression représentant la valeur à calculer. Consultez la section [Expressions scalaires](sql-query-scalar-expressions.md) pour plus d’informations.  

## <a name="remarks"></a>Remarques

La syntaxe `SELECT *` est valide uniquement si la clause FROM a déclaré exactement un alias. `SELECT *` fournit une projection d’identité, ce qui peut être utile si aucune projection n’est nécessaire. SELECT * est valide uniquement si la clause FROM est spécifiée et n’a introduit qu’une seule source d’entrée.  
  
`SELECT <select_list>` et `SELECT *` sont ce qu’on appelle du « sucre syntaxique » et que vous pouvez également les exprimer à l’aide d’instructions SELECT simples, comme indiqué ci-dessous.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   équivaut à :  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   équivaut à :  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exemples

L’exemple d’instruction SELECT suivante retourne `address` de `Families` dont `id` correspond à `AndersenFamily` :

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats sont :

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Accesseur de propriété entre guillemets
Vous pouvez accéder aux propriétés à l’aide de l’opérateur de propriété entre guillemets []. Par exemple, `SELECT c.grade` and `SELECT c["grade"]` sont équivalentes. Cette syntaxe est utile pour placer dans une séquence d’échappement une propriété qui contient des espaces, des caractères spéciaux, ou qui a le même nom qu’un mot clé SQL ou un mot réservé.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Propriétés imbriquées

L’exemple suivant projette deux propriétés imbriquées : `f.address.state` et `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats sont :

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>Expressions JSON

La projection prend également en charge les expressions JSON, comme le montre l’exemple suivant :

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats sont :

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

Dans l’exemple précédent, la clause SELECT a besoin de créer un objet JSON, et étant donné que l’exemple ne fournit aucune clé, la clause utilise le nom de variable d’argument implicite `$1`. La requête suivante retourne deux variables d’argument implicites : `$1` et `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Les résultats sont :

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

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main](sql-query-getting-started.md)
- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Clause WHERE](sql-query-where.md)