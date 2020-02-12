---
title: Indexation dans l’API pour MongoDB d’Azure Cosmos DB
description: Présente une vue d’ensemble des fonctionnalités d’indexation avec l’API pour MongoDB d’Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: c8879884cf3d882e6a6b441244ed139072bedeeb
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029467"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexation à l’aide de l’API pour MongoDB d’Azure Cosmos DB

L’API pour MongoDB d’Azure Cosmos DB tire parti des fonctionnalités de gestion automatique des index de Cosmos DB. Par conséquent, les utilisateurs ont accès aux stratégies d’indexation par défaut de Cosmos DB. Ainsi, à partir du moment où aucun index n’a été défini par l’utilisateur ou n’a été supprimé, tous les champs seront automatiquement indexés par défaut lorsqu’ils seront insérés dans la collection. Pour la plupart des scénarios, nous vous recommandons d’utiliser la stratégie d’indexation par défaut définie sur le compte.

## <a name="indexing-for-version-36"></a>Indexation pour la version 3.6

Les comptes desservant la version 3.6 du protocole Wire fournissent une stratégie d’indexation par défaut différente de la stratégie fournie par les versions antérieures. Par défaut, seul le champ _ID est indexé. Pour indexer des champs supplémentaires, l’utilisateur doit appliquer les commandes de gestion d’index MongoDB. Pour appliquer le tri d’une requête, un index doit actuellement être créé sur les champs utilisés dans l’opération de tri.

### <a name="dropping-the-default-indexes-36"></a>Dépôt des index par défaut (3.6)

Pour les comptes desservant la version 3.6 du protocole Wire, le seul index par défaut est _ ID, qui ne peut pas être déposé.

### <a name="creating-a-compound-index-36"></a>Création d’un index composé (3.6)

Les véritables index composés sont pris en charge pour les comptes utilisant le protocole Wire 3.6. La commande suivante crée un index composé sur les champs « a » et « b » : `db.coll.createIndex({a:1,b:1})`

Les index composés peuvent être utilisés pour trier efficacement sur plusieurs champs à la fois, par exemple : `db.coll.find().sort({a:1,b:1})`

### <a name="track-the-index-progress"></a>Suivre la progression de l’index

La version 3.6 de l’API d’Azure Cosmos DB pour les comptes MongoDB prend en charge la commande `currentOp()`, laquelle permet de suivre la progression de l’index sur une instance de base de données. Cette commande retourne un document qui contient des informations sur les opérations en cours sur une instance de base de données. La commande `currentOp` permet de suivre toutes les opérations en cours en MongoDB natif, tandis qu’elle ne prend en charge que le suivi de l’opération d’index dans l’API d’Azure Cosmos DB pour MongoDB.

Voici quelques exemples qui montrent comment utiliser la commande `currentOp` pour suivre la progression de l’index :

• Obtenir la progression de l’index pour une collection :

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

• Obtenir la progression de l’index pour toutes les collections d’une base de données :

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

• Obtenir la progression de l’index pour toutes les bases de données et collections d’un compte Azure Cosmos :

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Les détails de la progression de l’index contiennent le pourcentage de progression pour l’opération d’index active. L’exemple suivant montre le format du document de sortie pour les différentes étapes de la progression de l’index :

1. Si le pourcentage de complétion de l’opération d’index sur une collection « foo » et une base de données « bar » est de 60 %, le document de sortie suivant est généré. `Inprog[0].progress.total` indique 100 comme complétion cible.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. Pour une opération d’index qui vient de démarrer sur une collection « foo » et une base de données « bar », le document de sortie peut indiquer une progression de 0 % jusqu’à ce qu’un niveau mesurable soit atteint.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. Une fois l’opération d’index en cours terminée, le document de sortie indique des opérations en cours vides.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="indexing-for-version-32"></a>Indexation pour la version 3.2

### <a name="dropping-the-default-indexes-32"></a>Dépôt des index par défaut (3.2)

La commande suivante peut être utilisée pour supprimer les index par défaut d’une collection ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>Création d’un index composé (3.2)

Les index composés comportent des références à plusieurs champs d’un document. Logiquement, ils équivalent à la création de plusieurs index individuels par champ. Pour tirer parti des optimisations fournies par les techniques d’indexation de Cosmos DB, nous vous recommandons de créer plusieurs index individuels au lieu d’un seul index composé (non unique).

## <a name="common-indexing-operations"></a>Opérations d’indexation communes

Les opérations suivantes sont communes pour les comptes desservant la version 3.6 du protocole Wire et les comptes desservant les versions antérieures du protocole Wire. 

## <a name="creating-unique-indexes"></a>Création d’index uniques

[Les index uniques](unique-keys.md) sont utiles pour s’assurer que deux documents ou plus ne contiennent pas la même valeur pour les champs indexés.

>[!Important]
> Actuellement, les index uniques ne peuvent être créés que lorsque la collection est vide (lorsqu’elle ne contient aucun document).

La commande suivante crée un index unique sur le champ « student_id » :

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Pour les collections partitionnées, en fonction du comportement de MongoDB, la création d’un index unique nécessite la clé de partition. En d’autres termes, tous les index uniques dans une collection partitionnée sont des index composés où l’un des champs est la clé de partition.

Les commandes suivantes créent une collection partitionnée ```coll``` (la clé de partition est ```university```) avec un index unique sur les champs student_id et university :

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

Dans l’exemple ci-dessus, si la clause ```"university":1``` a été omise, une erreur avec le message suivant est renvoyée :

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>Index TTL

Pour activer l’expiration de document dans une collection particulière, un [« index TTL » (index de durée de vie)](../cosmos-db/time-to-live.md) doit être créé. Un index TTL est un index sur le champ _ts avec une valeur « expireAfterSeconds ».

Exemple :

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

La commande précédente entraîne la suppression de tous les documents de la collection ```db.coll``` qui n’ont pas été modifiés au cours des 10 dernières secondes.

> [!NOTE]
> **_ts** est un champ spécifique de Cosmos DB qui n’est pas accessible à partir des clients MongoDB. Il s’agit d’une propriété (système) réservée qui contient le timestamp de la dernière modification du document.

## <a name="migrating-collections-with-indexes"></a>Migration de collections avec des index

Actuellement, la création d’index uniques n’est possible que lorsque la collection ne contient aucun document. Les outils de migration MongoDB populaires tentent de créer des index uniques après l’importation des données. Pour contourner ce problème, il est recommandé aux utilisateurs de créer manuellement les collections et index uniques correspondants, au lieu de laisser l’outil de migration le faire (pour ```mongorestore``` ce comportement est obtenu à l’aide de l’indicateur `--noIndexRestore` dans la ligne de commande).

## <a name="next-steps"></a>Étapes suivantes

* [Indexation dans Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Faire expirer automatiquement des données avec la durée de vie dans Azure Cosmos DB](../cosmos-db/time-to-live.md)
