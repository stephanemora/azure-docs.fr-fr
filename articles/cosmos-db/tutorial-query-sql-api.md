---
title: 'Tutoriel : Comment effectuer des interrogation avec le langage SQL dans Azure Cosmos DB ?'
description: 'Tutoriel : Découvrir comment effectuer des interrogations avec des requêtes SQL dans Azure Cosmos DB à l’aide du playground de test de requêtes'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 08/26/2021
ms.reviewer: sngun
ms.openlocfilehash: 33825ba4d340e8422ed8a61e6dbb2e219aaa5c39
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123034865"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Tutoriel : Interroger Azure Cosmos DB à l’aide de l’API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

L’[API SQL](./introduction.md) Azure Cosmos DB prend en charge l’interrogation de documents à l’aide de SQL. Cet article fournit un exemple de document et deux exemples de requêtes SQL et de résultats.

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

Vous pouvez exécuter des requêtes à l’aide de l’Explorateur de données du portail Azure et via l’[API REST et les SDK](sql-api-sdk-dotnet.md).

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

Vous tentez d’effectuer une planification de capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
* Si vous ne connaissez que le nombre de vCore et de serveurs dans votre cluster de bases de données existant, lisez l’article sur l’[estimation des unités de requête à l’aide des vCore ou des processeurs virtuels](convert-vcore-to-request-unit.md) 
* Si vous connaissez les taux de requête typiques de votre charge de travail actuelle, lisez la section [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)