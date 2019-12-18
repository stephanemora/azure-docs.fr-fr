---
title: Bien démarrer avec les requêtes SQL dans Azure Cosmos DB
description: Découvrez comment utiliser des requêtes SQL pour interroger des données à partir d’Azure Cosmos DB. Vous pouvez charger des exemples de données dans un conteneur dans Azure Cosmos DB et les interroger.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873333"
---
# <a name="getting-started-with-sql-queries"></a>Bien démarrer avec les requêtes SQL

Les comptes d’API SQL Azure Cosmos DB prennent en charge l’interrogation d’éléments en utilisant le langage SQL (Structured Query Language) comme langage de requête JSON. Les objectifs de conception du langage de requête Azure Cosmos DB sont les suivants :

* Prendre en charge SQL, un des langages de requête les plus connus et les plus populaires, au lieu d’inventer un nouveau langage de requête. Le langage SQL fournit un modèle de programmation formel pour créer des requêtes élaborées sur les éléments JSON.  

* Utiliser le modèle de programmation de JavaScript comme base pour le langage de requête. Le système de type, l’évaluation d’expression et l’appel de fonction de JavaScript sont les racines de l’API SQL. Ces racines donnent un modèle de programmation naturel pour les fonctionnalités telles que les projections relationnelles, la navigation hiérarchique entre les éléments JSON, les jointures réflexives, les requêtes spatiales et l’appel de fonctions définies par l’utilisateur écrites entièrement en JavaScript.

## <a name="upload-sample-data"></a>Charger l’exemple de données

Dans votre compte Cosmos DB de l’API SQL, créez un conteneur appelé `Families`. Créez deux éléments JSON simples dans le conteneur. Vous pouvez exécuter la plupart des exemples de requêtes dans la documentation de requête Azure Cosmos DB à l’aide de ce jeu de données.

### <a name="create-json-items"></a>Créer des éléments JSON

Le code suivant crée deux éléments JSON simples sur les familles. Les éléments JSON simples pour les familles Andersen et Wakefield incluent les parents, les enfants et leurs animaux de compagnie, l’adresse et les informations d’inscription. Le premier élément se compose de chaînes, de nombres, de valeurs booléennes, de tableaux et de propriétés imbriquées.


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

Le deuxième élément utilise `givenName` et `familyName` au lieu de `firstName` et `lastName`.

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
        "gender": "female",
        "grade": 1,
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

### <a name="query-the-json-items"></a>Interroger les éléments JSON

Appliquez quelques requêtes sur les données JSON pour comprendre certains aspects clés du langage de requête SQL d’Azure Cosmos DB.

La requête suivante retourne les éléments dont le champ `id` correspond à `AndersenFamily`. Comme il s’agit d’une requête `SELECT *`, son résultat est l’élément JSON complet. Pour plus d’informations sur la syntaxe SELECT, consultez [Instruction SELECT](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Voici le résultat de la requête : 

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

La requête suivante remet en forme la sortie JSON. La requête projette un nouvel objet JSON `Family` avec deux champs sélectionnés, `Name` et `City`, où la ville de l’adresse est identique à l’État. « NY, NY » correspond à ce cas.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Voici le résultat de la requête :

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

La requête suivante retourne tous les noms donnés des enfants de la famille dont l’`id` correspond à `WakefieldFamily`, classés par ville.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Les résultats sont :

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Remarques

Les exemples précédents illustrent plusieurs aspects du langage de requête Cosmos DB :  

* Comme l’API SQL fonctionne avec les valeurs JSON, il traite des entités d’arborescence au lieu des lignes et des colonnes. Vous pouvez faire référence aux nœuds de l’arborescence quel que soit le niveau auquel ils se trouvent, comme `Node1.Node2.Node3…..Nodem`, à l’image de la référence en deux parties de `<table>.<column>` dans SQL ANSI.

* Étant donné que le langage de requête fonctionne avec des données sans schéma, le système de type doit être lié dynamiquement. La même expression peut produire différents types sur différents éléments. Le résultat d’une requête est une valeur JSON valide, mais n’est pas forcément un schéma fixe.  

* Azure Cosmos DB prend uniquement en charge les éléments JSON stricts. Le système de type et les expressions peuvent uniquement traiter des types JSON. Pour en savoir plus, consultez la [spécification JSON](https://www.json.org/).  

* Un conteneur Cosmos est une collection sans schéma d’éléments JSON. Les relations dans et entre les éléments d’un conteneur sont capturées de façon implicite par l’autonomie, et non par les relations de clé primaire et de clé étrangère. Cette fonctionnalité est importante pour les jointures intra-éléments décrites plus loin dans cet article.

## <a name="next-steps"></a>Étapes suivantes

- [Présentation d’Azure Cosmos DB](introduction.md)
- [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Clause SELECT](sql-query-select.md)
