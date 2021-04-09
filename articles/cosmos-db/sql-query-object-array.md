---
title: Utilisation de tableaux et objets dans Azure Cosmos DB
description: Découvrez la syntaxe SQL permettant de créer des tableaux et des objets dans Azure Cosmos DB. Cet article fournit également quelques exemples pour effectuer des opérations sur des objets de tableau
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493785"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Utilisation de tableaux et objets dans Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Une fonctionnalité essentielle de l’API SQL Azure Cosmos DB est la création de tableau et d’objet. Ce document utilise des exemples qui peuvent être recréés à l’aide du [jeu de données des familles](sql-query-getting-started.md#upload-sample-data).

Voici un exemple d’élément de ce jeu de données :

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

## <a name="arrays"></a>Tableaux

Vous pouvez construire des tableaux, comme l’illustre l’exemple suivant :

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

Les résultats sont :

```json
[
  {
    "CityState": [
      "Seattle",
      "WA"
    ]
  },
  {
    "CityState": [
      "NY", 
      "NY"
    ]
  }
]
```

Vous pouvez également utiliser l’[expression ARRAY](sql-query-subquery.md#array-expression) pour construire un tableau à partir des résultats d’une [sous-requête](sql-query-subquery.md). Cette requête obtient tous les noms donnés distincts des enfants dans un tableau.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

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

## <a name="iteration"></a><a id="Iteration"></a>Itération

L’API SQL prend en charge l’itération sur les tableaux JSON, avec le [mot clé IN](sql-query-keywords.md#in) dans la source FROM. Dans l’exemple suivant :

```sql
SELECT *
FROM Families.children
```

Les résultats sont :

```json
[
  [
    {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy"}]
    }
  ], 
  [
    {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1
    }, 
    {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }
  ]
]
```

La requête suivante effectue une itération sur `children` dans le conteneur `Families`. Le tableau obtenu est différent de la requête précédente. Cet exemple fractionne `children` et regroupe les résultats en un seul tableau :  

```sql
SELECT *
FROM c IN Families.children
```

Les résultats sont :

```json
[
  {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy" }]
  },
  {
      "familyName": "Merriam",
      "givenName": "Jesse",
      "gender": "female",
      "grade": 1
  },
  {
      "familyName": "Miller",
      "givenName": "Lisa",
      "gender": "female",
      "grade": 8
  }
]
```

Vous pouvez même filtrer chaque entrée du tableau, comme dans l’exemple suivant :

```sql
SELECT c.givenName
FROM c IN Families.children
WHERE c.grade = 8
```

Les résultats sont :

```json
[{
  "givenName": "Lisa"
}]
```

Vous pouvez également effectuer des agrégations sur le résultat d’une itération de tableau. Par exemple, la requête suivante compte le nombre d’enfants parmi toutes les familles :

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

Les résultats sont :

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> Lorsque vous utilisez le mot clé IN pour l’itération, vous ne pouvez pas filtrer ou projeter des propriétés en dehors du tableau. Utilisez plutôt [JOINs](sql-query-join.md).

Pour obtenir des exemples supplémentaires, consultez notre [billet de blog sur l’utilisation de tableaux dans Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer](sql-query-getting-started.md)
- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Joins](sql-query-join.md)
