---
title: Comment effectuer des interrogation avec le langage SQL dans Azure Cosmos DB ?
description: Apprendre à effectuer des interrogations avec le langage SQL dans Azure Cosmos DB
services: cosmos-db
author: rafats
ms.author: rafats
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 05/10/2017
ms.openlocfilehash: 7f8c3f6d11ba7551cb0d0c9398b46bb001b9b75b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832630"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Didacticiel : Interroger Azure Cosmos DB à l’aide de l’API SQL

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
* [Requête SQL et syntaxe SQL](how-to-sql-query.md)

## <a name="prerequisites"></a>Prérequis

Ce didacticiel suppose que vous ayez un compte et une collection Azure Cosmos DB. Cela n’est pas le cas ? Procédez au [démarrage rapide en 5 minutes](create-mongodb-nodejs.md).

## <a name="example-query-1"></a>Exemple de requête 1

Étant donné l’exemple de document de famille ci-dessus, la requête SQL suivante renvoie les documents où le champ id correspond à `WakefieldFamily`. Comme il s’agit d’une instruction `SELECT *`, le résultat de la requête est le document JSON complet :

**Requête**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

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

La requête suivante renvoie tous les noms donnés des enfants de la famille dont l’ID correspond à `WakefieldFamily` classés par classe.

**Requête**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'

**Résultats**

[ { "givenName": "Jesse" }, { "givenName": "Lisa" } ]


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * Effectuer des interrogations à l’aide de SQL  

Vous pouvez maintenant poursuivre avec le didacticiel suivant montrant comment distribuer vos données globalement.

> [!div class="nextstepaction"]
> [Distribuer vos données globalement](tutorial-global-distribution-sql-api.md)

