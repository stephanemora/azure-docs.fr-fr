---
title: 'Tutoriel : Comment effectuer des interrogation avec le langage SQL dans Azure Cosmos DB ?'
description: 'Tutoriel : Découvrir comment effectuer des interrogations avec des requêtes SQL dans Azure Cosmos DB à l’aide du playground de test de requêtes'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 2a6033ef1d2b7dda04b1510d42fa49141e0b79b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88135995"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Tutoriel : Interroger Azure Cosmos DB à l’aide de l’API SQL

L’[API SQL](documentdb-introduction.md) Azure Cosmos DB prend en charge l’interrogation de documents à l’aide de SQL. Cet article fournit un exemple de document et deux exemples de requêtes SQL et de résultats.

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Interrogation des données avec SQL

## <a name="sample-document"></a>Exemple de document

L’exemple de document suivant est utilisé pour les interrogations SQL de cet article.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="where-can-i-run-sql-queries"></a>Où puis-je exécuter des requêtes SQL ?

Vous pouvez exécuter des requêtes à l’aide de l’Explorateur de données dans le portail Azure, via l’[API REST et Kits de développement logiciel (SDK) ](sql-api-sdk-dotnet.md)et même l’[interface de requête](https://www.documentdb.com/sql/demo), qui exécute des requêtes sur un ensemble d’exemples de données existant.

Pour plus d’informations sur les requêtes SQL, consultez :
* [Requête SQL et syntaxe SQL](sql-query-getting-started.md)

## <a name="prerequisites"></a>Prérequis

Ce didacticiel suppose que vous ayez un compte et une collection Azure Cosmos DB. Vous ne disposez d’aucune de ces ressources ? Procédez au [démarrage rapide en 5 minutes](create-cosmosdb-resources-portal.md).

## <a name="example-query-1"></a>Exemple de requête 1

Étant donné l’exemple de document de famille ci-dessus, la requête SQL suivante retourne les documents dans lesquels le champ ID correspond à `WakefieldFamily`. Comme il s’agit d’une instruction `SELECT *`, le résultat de la requête est le document JSON complet :

**Requête**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

**Résultats**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>Exemple de requête 2

La requête suivante retourne tous les prénoms des enfants de la famille dont l’ID correspond à `WakefieldFamily`.

**Requête**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**Résultats**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Effectuer des interrogations à l’aide de SQL  

Vous pouvez maintenant poursuivre avec le didacticiel suivant montrant comment distribuer vos données globalement.

> [!div class="nextstepaction"]
> [Distribuer vos données globalement](tutorial-global-distribution-sql-api.md)

