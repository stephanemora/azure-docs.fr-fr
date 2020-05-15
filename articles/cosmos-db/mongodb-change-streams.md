---
title: Flux de modification dans l’API pour MongoDB d’Azure Cosmos DB
description: Découvrez comment utiliser les flux de modification dans l’API pour MongoDB d’Azure Cosmos DB pour obtenir les modifications apportées à vos données.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 7a6060448175530ada5ba95ceda470056a7be002
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872143"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Flux de modification dans l’API pour MongoDB d’Azure Cosmos DB

La prise en charge du [flux de modification](change-feed.md) dans l’API pour MongoDB d’Azure Cosmos DB est disponible à l’aide de l’API de flux de modification. À l’aide de l’API de flux de modification, vos applications peuvent obtenir les modifications apportées à la collection ou aux éléments d’une seul partition. Par la suite, vous pouvez effectuer d’autres actions en fonction des résultats. Les modifications apportées aux éléments de la collection sont capturées dans l’ordre de leur modification, et l’ordre de tri est garanti par clé de partition.

> [!NOTE]
> Pour utiliser les flux de modification, créez le compte à l’aide de la version 3.6 ou une version ultérieure de l’API d’Azure Cosmos DB pour MongoDB. Si vous exécutez les exemples de flux de modification sur une version antérieure, vous pouvez voir l’erreur `Unrecognized pipeline stage name: $changeStream`.

## <a name="current-limitations"></a>Limites actuelles

Les limitations suivantes s’appliquent quand plusieurs flux de modification sont utilisés :

* Les propriétés `operationType` et `updateDescription` ne sont pas encore prises en charge dans le document de sortie.
* Les types d'opérations `insert`, `update` et `replace` sont actuellement pris en charge. 
* L’opération de suppression ou d’autres événements ne sont pas encore pris en charge.

En raison de ces limitations, l’étape $match, l’étape $project et les options fullDocument sont requises, comme indiqué dans les exemples précédents.

Contrairement au flux de modification dans l’API SQL d’Azure Cosmos DB, il n’existe pas de [bibliothèque de processeurs de flux de modification](change-feed-processor.md) distincte pour consommer des flux de modification ou un conteneur de baux. Il n’existe actuellement aucune prise en charge pour les [déclencheurs Azure Functions](change-feed-functions.md) permettant de traiter les flux de modification.

## <a name="error-handling"></a>Gestion des erreurs

Les codes d’erreur et messages suivants sont pris en charge lorsque des flux de modification sont utilisés :

* **Code d’erreur HTTP 16500** : lorsque le flux de modification est limité, il retourne une page vide.

* **NamespaceNotFound (OperationType Invalidate)**  : si vous exécutez le flux de modification sur une collection qui n’existe pas ou si la collection est supprimée, une erreur `NamespaceNotFound` est retournée. Étant donné que la propriété `operationType` ne peut pas être retournée dans le document de sortie, au lieu de l’erreur `operationType Invalidate`, l’erreur `NamespaceNotFound` est retournée.

## <a name="examples"></a>Exemples

L’exemple suivant montre comment récupérer des flux de modification sur tous les éléments de la collection. Cet exemple crée un curseur pour surveiller les éléments lorsqu’ils sont insérés, mis à jour ou remplacés. L’étape `$match`, l’étape `$project` et l’option `fullDocument` sont nécessaires pour récupérer les flux de modification. La surveillance des opérations de suppression à l’aide de flux de modification n’est pas prise en charge actuellement. Pour contourner ce problème, vous pouvez ajouter un marqueur réversible sur les éléments en cours de suppression. Par exemple, vous pouvez ajouter un attribut dans l’élément appelé « supprimé ». Si vous souhaitez supprimer l’élément, vous pouvez définir « supprimé » sur `true` et définir une durée de vie sur l’élément. Étant donné que la mise à jour de « supprimé » sur `true` reste une mise à jour, cette modification sera visible dans le flux de modification.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Modifications au sein d’une partition unique

L’exemple suivant montre comment récupérer les modifications apportées aux éléments au sein d’une partition unique. Cet exemple récupère les modifications des éléments dont la clé de partition est égale à « a » et la valeur de clé de partition égale à « 1 ». Il est possible que différents clients lisent en parallèle les modifications de différentes partitions.

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

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser la durée de vie pour faire expirer les données automatiquement dans l’API pour MongoDB d’Azure Cosmos DB](mongodb-time-to-live.md)
* [Indexation dans l’API pour MongoDB d’Azure Cosmos DB](mongodb-indexing.md)
