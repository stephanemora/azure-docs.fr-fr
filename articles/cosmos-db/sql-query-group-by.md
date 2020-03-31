---
title: Clause GROUP BY dans Azure Cosmos DB
description: Découvrez la clause GROUP BY pour Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819103"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Clause GROUP BY dans Azure Cosmos DB

La clause GROUP BY divise les résultats de la requête en fonction des valeurs d’une ou plusieurs propriétés spécifiées.

> [!NOTE]
> Azure Cosmos DB prend actuellement en charge GROUP BY dans le kit SDK .NET 3.3 et les versions ultérieures, ainsi que le kit SDK JavaScript 3.4 et les versions ultérieures.
> La prise en charge de kits SDK dans d’autres langages n’est pas encore disponible, mais elle est prévue.

## <a name="syntax"></a>Syntaxe

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Arguments

- `<scalar_expression_list>`

   Spécifie les expressions qui seront utilisées pour diviser les résultats de la requête.

- `<scalar_expression>`
  
   Toutes les expressions scalaires sont autorisées à l’exception des sous-requêtes scalaires et des agrégats scalaires. Chaque expression scalaire doit contenir au moins une référence de propriété. Il n’existe aucune limite quant au nombre d’expressions individuelles ou à la cardinalité de chaque expression.

## <a name="remarks"></a>Notes
  
  Quand une requête utilise une clause GROUP BY, la clause SELECT ne peut contenir que le sous-ensemble de propriétés et de fonctions système incluses dans la clause GROUP BY. Les [fonctions système d’agrégation](sql-query-aggregates.md) font exception. Elles peuvent apparaître dans la clause SELECT sans être incluses dans la clause GROUP BY. Par ailleurs, vous pouvez toujours inclure des valeurs littérales dans la clause SELECT.

  La clause GROUP BY doit être placée après les clauses SELECT, FROM et WHERE et avant la clause OFFSET LIMIT. Vous ne pouvez pas utiliser la clause GROUP BY avec une clause ORDER BY pour le moment, mais cela est prévu.

  La clause GROUP BY n’autorise pas les éléments suivants :
  
- Propriétés d’alias ou fonctions système d’alias (l’alias est toujours autorisé dans la clause SELECT)
- Sous-requêtes
- Fonctions système d’agrégation (uniquement autorisées dans la clause SELECT)

## <a name="examples"></a>Exemples

Ces exemples utilisent le jeu de données sur l’alimentation, disponible sur le site du [Playground de test de requêtes Azure Cosmos DB](https://www.documentdb.com/sql/demo).

Par exemple, voici une requête qui retourne le nombre total d’éléments dans chaque foodGroup :

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Certains résultats sont présentés ci-après (le mot clé TOP est utilisé pour limiter les résultats) :

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Cette requête comporte deux expressions utilisées pour diviser les résultats :

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Certains résultats sont présentés ci-après :

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Cette requête comporte une fonction système dans la clause GROUP BY :

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Certains résultats sont présentés ci-après :

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

Cette requête utilise des mots clés et des fonctions système dans l’expression de propriété de l’élément :

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Les résultats sont :

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer](sql-query-getting-started.md)
- [Clause SELECT](sql-query-select.md)
- [Fonctions d’agrégation](sql-query-aggregates.md)
