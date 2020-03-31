---
title: Flux de modification dans l’API pour MongoDB d’Azure Cosmos DB
description: Découvrez comment utiliser les flux de modification dans l’API pour MongoDB d’Azure Cosmos DB pour obtenir les modifications apportées à vos données.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467775"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Flux de modification dans l’API pour MongoDB d’Azure Cosmos DB

La prise en charge du [flux de modification](change-feed.md) dans l’API pour MongoDB d’Azure Cosmos DB est disponible à l’aide de l’API de flux de modification. À l’aide de l’API de flux de modification, vos applications peuvent obtenir les modifications apportées à la collection ou aux éléments d’une seul partition. Par la suite, vous pouvez effectuer d’autres actions en fonction des résultats. Les modifications apportées aux éléments de la collection sont capturées dans l’ordre de leur modification, et l’ordre de tri est garanti par clé de partition.

> [!NOTE]
> Pour utiliser les flux de modification, créez le compte à l’aide de la version 3.6 ou une version ultérieure de l’API d’Azure Cosmos DB pour MongoDB. Si vous exécutez les exemples de flux de modification sur une version antérieure, vous pouvez voir l’erreur `Unrecognized pipeline stage name: $changeStream`. 

L’exemple suivant montre comment récupérer des flux de modification sur tous les éléments de la collection. Cet exemple crée un curseur pour surveiller les éléments lorsqu’ils sont insérés, mis à jour ou remplacés. L’étape $match, l’étape $project et l’option fullDocument sont nécessaires pour récupérer les flux de modification. La surveillance des opérations de suppression à l’aide de flux de modification n’est pas prise en charge actuellement. Pour contourner ce problème, vous pouvez ajouter un marqueur réversible sur les éléments en cours de suppression. Par exemple, vous pouvez ajouter un attribut dans l’élément appelé « deleted » et lui attribuer la valeur « true », puis définir une durée de vie pour l’élément afin de pouvoir le supprimer automatiquement et le suivre.

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

L’exemple suivant montre comment récupérer les modifications apportées aux éléments dans une seul partition. Cet exemple récupère les modifications des éléments dont la clé de partition est égale à « a » et la valeur de clé de partition égale à « 1 ».

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Limites actuelles

Les limitations suivantes s’appliquent quand plusieurs flux de modification sont utilisés :

* Les propriétés `operationType` et `updateDescription` ne sont pas encore prises en charge dans le document de sortie.
* Les types d'opérations `insert`, `update` et `replace` sont actuellement pris en charge. L’opération de suppression ou d’autres événements ne sont pas encore pris en charge.

En raison de ces limitations, l’étape $match, l’étape $project et les options fullDocument sont requises, comme indiqué dans les exemples précédents.

## <a name="error-handling"></a>Gestion des erreurs

Les codes d’erreur et messages suivants sont pris en charge lorsque des flux de modification sont utilisés :

* **Code d’erreur HTTP 429** : lorsque le flux de modification est limité, il retourne une page vide.

* **NamespaceNotFound (OperationType Invalidate)**  : si vous exécutez le flux de modification sur une collection qui n’existe pas ou si la collection est supprimée, une erreur `NamespaceNotFound` est retournée. Étant donné que la propriété `operationType` ne peut pas être retournée dans le document de sortie, au lieu de l’erreur `operationType Invalidate`, l’erreur `NamespaceNotFound` est retournée.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser la durée de vie pour faire expirer les données automatiquement dans l’API pour MongoDB d’Azure Cosmos DB](mongodb-time-to-live.md)
* [Indexation dans l’API pour MongoDB d’Azure Cosmos DB](mongodb-indexing.md)
