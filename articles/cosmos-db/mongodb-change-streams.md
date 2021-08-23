---
title: Flux de modification dans l’API pour MongoDB d’Azure Cosmos DB
description: Découvrez comment utiliser les flux de modification dans l’API pour MongoDB d’Azure Cosmos DB pour obtenir les modifications apportées à vos données.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: b898d55607caab185c9dc382e6323aca415500aa
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113359404"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Flux de modification dans l’API pour MongoDB d’Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

La prise en charge du [flux de modification](change-feed.md) dans l’API pour MongoDB d’Azure Cosmos DB est disponible à l’aide de l’API de flux de modification. À l’aide de l’API de flux de modification, vos applications peuvent obtenir les modifications apportées à la collection ou aux éléments d’une seul partition. Par la suite, vous pouvez effectuer d’autres actions en fonction des résultats. Les modifications apportées aux éléments de la collection sont capturées dans l’ordre de leur modification, et l’ordre de tri est garanti par clé de partition.

> [!NOTE]
> Pour utiliser les flux de modifications, créez l’API d’Azure Cosmos DB pour le compte MongoDB avec la version 3.6 ou ultérieure du serveur. Si vous exécutez les exemples de flux de modifications sur une version antérieure, vous pouvez voir l’erreur *Nom de phase de pipeline non reconnu : $changeStream*.

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

# <a name="java"></a>[Java](#tab/java)

L’exemple suivant montre comment utiliser la fonctionnalité de flux de modifications dans Java. Pour obtenir un exemple complet, consultez ce [Dépôt GitHub](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/main/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java). Cet exemple montre également comment utiliser la méthode `resumeAfter` pour rechercher toutes les modifications de la dernière lecture. 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

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

## <a name="current-limitations"></a>Limites actuelles

Les limitations suivantes s’appliquent quand plusieurs flux de modification sont utilisés :

* Les propriétés `operationType` et `updateDescription` ne sont pas encore prises en charge dans le document de sortie.
* Les types d'opérations `insert`, `update` et `replace` sont actuellement pris en charge. Toutefois, l’opération de suppression ou d’autres événements ne sont pas encore pris en charge.

En raison de ces limitations, l’étape $match, l’étape $project et les options fullDocument sont requises, comme indiqué dans les exemples précédents.

Contrairement au flux de modification dans l’API SQL d’Azure Cosmos DB, il n’existe pas de [bibliothèque de processeurs de flux de modification](change-feed-processor.md) distincte pour consommer des flux de modification ou un conteneur de baux. Il n’existe actuellement aucune prise en charge pour les [déclencheurs Azure Functions](change-feed-functions.md) permettant de traiter les flux de modification.

## <a name="error-handling"></a>Gestion des erreurs

Les codes d’erreur et messages suivants sont pris en charge lorsque des flux de modification sont utilisés :

* **Code d’erreur HTTP 16500** : lorsque le flux de modification est limité, il retourne une page vide.

* **NamespaceNotFound (OperationType Invalidate)**  : si vous exécutez le flux de modification sur une collection qui n’existe pas ou si la collection est supprimée, une erreur `NamespaceNotFound` est retournée. Étant donné que la propriété `operationType` ne peut pas être retournée dans le document de sortie, au lieu de l’erreur `operationType Invalidate`, l’erreur `NamespaceNotFound` est retournée.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser la durée de vie pour faire expirer les données automatiquement dans l’API pour MongoDB d’Azure Cosmos DB](mongodb-time-to-live.md)
* [Indexation dans l’API pour MongoDB d’Azure Cosmos DB](mongodb-indexing.md)
