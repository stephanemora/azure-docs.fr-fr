---
title: Mots clés SQL pour Azure Cosmos DB
description: Découvrez les mots clés SQL pour Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343191"
---
# <a name="keywords-in-azure-cosmos-db"></a>Mots clés dans Azure Cosmos DB
Cet article détaille les mots clés qui peuvent être utilisés dans les requêtes SQL Azure Cosmos DB.

## <a name="between"></a>BETWEEN

Comme dans SQL ANSI, vous pouvez utiliser le mot clé BETWEEN pour exprimer des requêtes sur des plages de valeurs numériques ou de chaîne. Par exemple, la requête suivante retourne tous les éléments dont la classe du premier enfant est comprise entre 1 et 5, inclus.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Contrairement à SQL ANSI, vous pouvez également utiliser la clause BETWEEN dans la clause FROM, comme dans l’exemple suivant.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Dans l’API SQL, contrairement à SQL ANSI, vous pouvez exprimer des requêtes de plage sur des propriétés de types mixtes. Par exemple, `grade` peut être un nombre comme `5` dans certains éléments et une chaîne comme `grade4` dans d’autres. Dans ces cas-là, comme dans JavaScript, la comparaison entre deux types différents a pour résultat `Undefined` ; l’élément est donc ignoré.

> [!TIP]
> Pour accélérer le temps d’exécution de requête, créez une stratégie d’indexation qui utilise un type d’index de plage sur tous les chemins ou propriétés numériques que filtre la clause BETWEEN.

## <a name="distinct"></a>DISTINCT

Le mot clé DISTINCT élimine les doublons dans la projection de la requête.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Dans cet exemple, la requête projette des valeurs pour chaque nom de famille.

Les résultats sont :

```json
[
    "Andersen"
]
```

Vous pouvez également projeter des objets uniques. Dans ce cas, le champ lastName n’existe pas dans un des deux documents ; ainsi, la requête retourne un objet vide.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Les résultats sont :

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT peut également être utilisé dans la projection au sein d’une sous-requête :

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Cette requête projette un tableau qui contient le givenName de chaque enfant, avec suppression des doublons. Ce tableau se voit attribuer l’alias ChildNames et est projeté dans la requête externe.

Les résultats sont :

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```
## <a name="in"></a> IN

Utilisez le mot clé IN pour vérifier si une valeur spécifiée correspond à une valeur dans une liste. Par exemple, la requête suivante retourne tous les éléments de famille dont le champ `id` a pour valeur `WakefieldFamily` ou `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

L’exemple suivant retourne tous les éléments dont l’État (« state ») est l’une des valeurs spécifiées :

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

L’API SQL prend en charge l’[itération sur les tableaux JSON](sql-query-object-array.md#Iteration), avec une nouvelle construction ajoutée par le biais du mot clé IN dans la source FROM. 

## <a name="top"></a>TOP

Le mot clé TOP retourne les `N` premiers résultats de la requête dans un ordre non défini. En guise de bonne pratique, utilisez TOP avec la clause ORDER BY pour limiter les résultats aux `N` premières valeurs ordonnées. La combinaison de ces deux clauses est le seul moyen d’indiquer de manière prévisible les lignes qui sont affectées par TOP.

Vous pouvez utiliser TOP avec une valeur constante, comme dans l’exemple suivant, ou avec une valeur variable à l’aide de requêtes paramétrables.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Les résultats sont :

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main](sql-query-getting-started.md)
- [Jointures](sql-query-join.md)
- [Sous-requêtes](sql-query-subquery.md)