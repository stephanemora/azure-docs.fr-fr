---
title: Indexation dans l’API pour MongoDB d’Azure Cosmos DB
description: Présente une vue d’ensemble des fonctionnalités d’indexation avec l’API pour MongoDB d’Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: f3f369928270c77557337bfdb1037cc5174c39f2
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637959"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexation à l’aide de l’API pour MongoDB d’Azure Cosmos DB

L’API d’Azure Cosmos DB pour MongoDB tire parti des fonctionnalités de base de gestion des index d’Azure Cosmos DB. Ce document décrit comment ajouter des index à l’aide de l’API d’Azure Cosmos DB pour MongoDB. Vous pouvez également lire une [vue d’ensemble de l’indexation dans Azure Cosmos DB](index-overview.md) qui est pertinente pour toutes les API.

## <a name="indexing-for-version-36"></a>Indexation pour la version 3.6

Le champ `_id` est toujours automatiquement indexé et ne peut pas être supprimé. L’API d’Azure Cosmos DB pour MongoDB applique automatiquement l’unicité du champ `_id` par clé de partition.

Pour indexer des champs supplémentaires, vous devez appliquer les commandes de gestion d’index MongoDB. Comme dans MongoDB, seul le champ `_id` est automatiquement indexé. Cette stratégie d’indexation par défaut est différente de celle de l’API SQL Azure Cosmos DB, qui indexe tous les champs par défaut.

Pour appliquer un tri sur une requête, un index doit être créé sur les champs utilisés dans l’opération de tri.

## <a name="index-types"></a>Types d’index

### <a name="single-field"></a>Champ unique

Vous pouvez créer des index sur n’importe quel champ unique. L’ordre de tri d’un index monochamp n’a pas d’importance. La commande suivante crée un index sur le champ `name` :

`db.coll.createIndex({name:1})`

Une requête utilise plusieurs index monochamps, le cas échéant. Vous pouvez créer jusqu’à 500 index monochamp par conteneur.

### <a name="compound-indexes-36"></a>Index composés (3.6)

Les index composés sont pris en charge pour les comptes utilisant le protocole filaire 3.6. Vous pouvez ajouter jusqu’à 8 champs dans un index composé. Contrairement à MongoDB, vous devez créer un index composé uniquement si votre requête doit effectuer un tri efficace sur plusieurs champs à la fois. Pour les requêtes avec plusieurs filtres qui n’ont pas besoin de tri, vous devez créer plusieurs index monochamp au lieu d’un seul index composé.

La commande suivante crée un index composé sur les champs `name` et `age` :

`db.coll.createIndex({name:1,age:1})`

Les index composés peuvent être utilisés pour effectuer un tri efficace sur plusieurs champs à la fois, par exemple :

`db.coll.find().sort({name:1,age:1})`

L’index composé ci-dessus peut également être utilisé pour un tri efficace sur une requête avec l’ordre de tri inverse sur tous les champs. Voici un exemple :

`db.coll.find().sort({name:-1,age:-1})`

Toutefois, la séquence des chemins dans l’index composé doit correspondre exactement à la requête. Voici un exemple de requête nécessitant un index composé supplémentaire :

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Index multiclés

Azure Cosmos DB crée des index multiclés pour indexer le contenu stocké dans des tableaux. Si vous indexez un champ avec une valeur de tableau, Azure Cosmos DB indexe automatiquement chaque élément du tableau.

### <a name="geospatial-indexes"></a>Index géospatiaux

De nombreux opérateurs géospatiaux tirent parti des index géospatiaux. Actuellement, l’API d’Azure Cosmos DB pour MongoDB prend en charge les index `2dsphere`. Les index `2d` ne sont pas encore pris en charge.

Voici un exemple de création d’index géospatial sur le champ `location` :

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Index de texte

Les index de texte ne sont actuellement pas pris en charge. Pour les requêtes de recherche de texte sur les chaînes, vous devez utiliser l’intégration de la [recherche cognitive Azure](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) à Azure Cosmos DB.

## <a name="index-properties"></a>Propriétés d’index

Les opérations suivantes sont communes pour les comptes desservant la version 3.6 du protocole Wire et les comptes desservant les versions antérieures du protocole Wire. Vous pouvez également obtenir plus d’informations sur les [index pris en charge et les propriétés indexées](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Index uniques

[Les index uniques](unique-keys.md) sont utiles pour s’assurer que deux documents ou plus ne contiennent pas la même valeur pour les champs indexés.

>[!Important]
> Les index uniques peuvent être créés seulement quand la collection est vide (qu’elle ne contient aucun document).

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

Pour les collections partitionnées, la création d’un index unique nécessite la clé de partition. En d’autres termes, tous les index uniques dans une collection partitionnée sont des index composés où l’un des champs est la clé de partition.

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

### <a name="ttl-indexes"></a>Index TTL

Pour activer l’expiration de document dans une collection particulière, un [« index TTL » (index de durée de vie)](../cosmos-db/time-to-live.md) doit être créé. Un index TTL est un index sur le champ _ts avec une valeur « expireAfterSeconds ».

Exemple :

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

La commande précédente entraîne la suppression de tous les documents de la collection ```db.coll``` qui n’ont pas été modifiés au cours des 10 dernières secondes.

> [!NOTE]
> **_ts** est un champ spécifique d’Azure Cosmos DB qui n’est pas accessible à partir des clients MongoDB. Il s’agit d’une propriété (système) réservée qui contient le timestamp de la dernière modification du document.

## <a name="track-the-index-progress"></a>Suivre la progression de l’index

La version 3.6 de l’API d’Azure Cosmos DB pour les comptes MongoDB prend en charge la commande `currentOp()`, laquelle permet de suivre la progression de l’index sur une instance de base de données. Cette commande retourne un document qui contient des informations sur les opérations en cours sur une instance de base de données. La commande `currentOp` permet de suivre toutes les opérations en cours en MongoDB natif, tandis qu’elle ne prend en charge que le suivi de l’opération d’index dans l’API d’Azure Cosmos DB pour MongoDB.

Voici quelques exemples qui montrent comment utiliser la commande `currentOp` pour suivre la progression de l’index :

* Obtenir la progression de l’index pour une collection :

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Obtenir la progression de l’index pour toutes les collections d’une base de données :

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Obtenir la progression de l’index pour toutes les bases de données et collections d’un compte Azure Cosmos :

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Les détails de la progression de l’index contiennent le pourcentage de progression pour l’opération d’index active. L’exemple suivant montre le format du document de sortie pour les différentes étapes de la progression de l’index :

1. Si la progression en pourcentage de l’opération d’index sur une collection « foo » et une base de données « bar » est de 60 %, le document de sortie suivant est généré. `Inprog[0].progress.total` indique 100 comme complétion cible.

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

2. Pour une opération d’index qui vient de démarrer sur une collection « foo » et une base de données « bar », le document de sortie peut indiquer une progression de 0 % tant qu’un niveau mesurable n’est pas atteint.

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

### <a name="background-index-updates"></a>Mises à jour d’index en arrière-plan

Quelle que soit la valeur spécifiée pour la propriété d’index **En arrière-plan**, les mises à jour d’index sont toujours effectuées en arrière-plan. Les mises à jour d’index consomment les RU avec une priorité inférieure à celle des autres opérations de base de données. Par conséquent, les changements d’index n’entraînent pas de temps d’arrêt pour les écritures, les mises à jour ou les suppressions.

Quand vous ajoutez un nouvel index, les requêtes l’utilisent immédiatement. Cela signifie que les requêtes peuvent ne pas retourner tous les résultats de correspondance et qu’elles ne retourneront pas d’erreurs pour les résultats manquants. Une fois la transformation d’index effectuée, les résultats de requête sont cohérents. Vous pouvez [suivre la progression de l’index](#track-the-index-progress).

## <a name="migrating-collections-with-indexes"></a>Migration de collections avec des index

Actuellement, la création d’index uniques n’est possible que lorsque la collection ne contient aucun document. Les outils de migration MongoDB populaires tentent de créer des index uniques après l’importation des données. Pour contourner ce problème, il est recommandé aux utilisateurs de créer manuellement les collections et index uniques correspondants, au lieu de laisser l’outil de migration le faire (pour ```mongorestore``` ce comportement est obtenu à l’aide de l’indicateur `--noIndexRestore` dans la ligne de commande).

## <a name="indexing-for-version-32"></a>Indexation pour la version 3.2

Pour les comptes Azure Cosmos DB compatibles avec la version 3.2 du protocole filaire MongoDB, les fonctionnalités d’indexation disponibles et les valeurs par défaut sont différentes. Vous pouvez [vérifier la version de votre compte](mongodb-feature-support-36.md#protocol-support). Vous pouvez effectuer la mise à niveau vers la version 3.6 en envoyant une [demande de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Si vous utilisez la version 3.2, cette section décrit les principales différences avec la version 3.6.

### <a name="dropping-the-default-indexes-32"></a>Dépôt des index par défaut (3.2)

Contrairement à la version 3.6 de l’API d’Azure Cosmos DB pour MongoDB, la version 3.2 indexe chaque propriété par défaut. La commande suivante peut être utilisée pour supprimer ces index par défaut pour une collection ```coll``` :

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Après la suppression des index par défaut, vous pouvez ajouter des index supplémentaires comme dans la version 3.6.

### <a name="compound-indexes-32"></a>Index composés (3.2)

Les index composés comportent des références à plusieurs champs d’un document. Si vous voulez créer un index composé, passez à la version 3.6 en envoyant une [demande de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Étapes suivantes

* [Indexation dans Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Faire expirer automatiquement des données avec la durée de vie dans Azure Cosmos DB](../cosmos-db/time-to-live.md)
