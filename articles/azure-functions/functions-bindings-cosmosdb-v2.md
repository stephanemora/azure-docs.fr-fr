---
title: Liaisons Azure Cosmos DB pour Azure Functions 2.x
description: Découvrez comment utiliser des déclencheurs et liaisons Azure Cosmos DB dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: da05dc7136a75d519660412f2ce176f7530eb392
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547436"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Liaisons Azure Cosmos DB pour Azure Functions 2.x

> [!div class="op_single_selector" title1="Sélectionnez la version du runtime Azure Functions que vous utilisez : "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

Cet article explique comment utiliser des liaisons [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) dans Azure Functions 2.x. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour Azure Cosmos DB.

> [!NOTE]
> Cet article concerne [Azure Functions version 2.x](functions-versions.md).  Pour plus d’informations sur l’utilisation de ces liaisons dans Functions 1.x, consultez [Liaisons Azure Cosmos DB pour Azure Functions 1.x](functions-bindings-cosmosdb.md).
>
> Cette liaison était nommée à l’origine DocumentDB. Dans Functions version 2.x, le déclencheur, les liaisons et le package sont tous renommés Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>API prises en charge

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>Packages - Functions 2.x

Les liaisons Azure Cosmos DB pour Functions version 2.x sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB), version 3.x. Le code source des liaisons se trouve dans le référentiel GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Déclencheur

Le déclencheur Azure Cosmos DB utilise le [flux de modification Azure Cosmos DB](../cosmos-db/change-feed.md) pour écouter les insertions et mises à jour sur plusieurs partitions. Le flux de modification publie les insertions et mises à jour, pas les suppressions.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui est invoquée lorsqu’il y a des insertions ou mises à jour dans la base de données et la collection spécifiées.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

L’exemple suivant montre une liaison de déclencheur Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction écrit des messages de journal quand des enregistrements Cosmos DB sont ajoutés ou modifiés.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Voici le code Script C# :

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre une liaison de déclencheur Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction écrit des messages de journal quand des enregistrements Cosmos DB sont ajoutés ou modifiés.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Voici le code JavaScript :

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple suivant montre une liaison de déclencheur Cosmos DB dans un fichier *function.json* et une [fonction Python](functions-reference-python.md) qui utilise la liaison. La fonction écrit des messages de journal quand des enregistrements Cosmos DB sont modifiés.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Voici le code Python :

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="javatabjava"></a>[Java](#tab/java)

L’exemple suivant montre une liaison de déclencheur Cosmos DB dans un fichier *function.json* et une [fonction Java](functions-reference-java.md) qui utilise la liaison. La fonction est appelée lorsqu’il y a des insertions ou des mises à jour dans la base de données et la collection spécifiées.

```json
{
    "type": "cosmosDBTrigger",
    "name": "items",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "AzureCosmosDBConnection",
    "databaseName": "ToDoList",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": false
}
```

Voici le code Java :

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@CosmosDBTrigger` sur les paramètres dont la valeur proviendrait de Cosmos DB.  Vous pouvez utiliser cette annotation avec des types Java natifs, des objets POJO ou des valeurs Null à l’aide de `Optional<T>`.

---

## <a name="trigger---attributes-and-annotations"></a>Déclencheur – Attributs et annotations

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs).

Le constructeur de l’attribut accepte le nom de la base de données et le nom de la collection. Pour plus d’informations sur ces paramètres et d’autres propriétés que vous pouvez configurer, consultez [Déclencheur - configuration](#trigger---configuration). Voici un exemple d’attribut `CosmosDBTrigger` dans une signature de méthode :

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Pour un exemple complet, voir [Déclencheur](#trigger).

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="javatabjava"></a>[Java](#tab/java)

À partir de la [bibliothèque du runtime des fonctions Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime), utilisez l’annotation `@CosmosDBInput` sur les paramètres qui lisent des données de Cosmos DB.

---

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `CosmosDBTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `cosmosDBTrigger`. |
|**direction** | n/a | Cette propriété doit être définie sur `in`. Ce paramètre est défini automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de variable utilisé dans le code de fonction, qui représente la liste des documents modifiés. |
|**connectionStringSetting**|**ConnectionStringSetting** | Nom d’un paramètre d’application contenant la chaîne de connexion utilisée pour se connecter au compte Azure Cosmos DB surveillé. |
|**databaseName**|**DatabaseName**  | Nom de la base de données Azure Cosmos DB contenant la collection surveillée. |
|**collectionName** |**CollectionName** | Nom de la collection surveillée. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Facultatif) Nom d’un paramètre d’application contenant la chaîne de connexion au compte Azure Cosmos DB contenant la collection de baux. S’il n’est pas défini, la valeur `connectionStringSetting` est utilisée. Ce paramètre est automatiquement défini lorsque la liaison est créée dans le portail. La chaîne de connexion de la collection de baux doit avoir des autorisations en écriture.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Facultatif) Nom de la base de données contenant la collection utilisée pour stocker des baux. S’il n’est pas défini, la valeur du paramètre `databaseName` est utilisée. Ce paramètre est automatiquement défini lorsque la liaison est créée dans le portail. |
|**leaseCollectionName** | **LeaseCollectionName** | (Facultatif) Nom de la collection utilisée pour stocker des baux. S’il n’est pas défini, la valeur `leases` est utilisée. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Facultatif) Lorsque la valeur est définie sur `true`, la collection de baux est créée automatiquement si elle n’existe pas. La valeur par défaut est `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Facultatif) Définit le nombre d’unités de requête à attribuer lors de la création de la collection de baux. Ce paramètre est utilisé uniquement quand `createLeaseCollectionIfNotExists` est défini sur `true`. Ce paramètre est automatiquement défini lors de la création de la liaison à l’aide du portail.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Facultatif) Lorsque cette valeur est définie, elle est ajoutée en tant que préfixe aux baux créés dans la collection de baux pour cette fonction. L’utilisation d’un préfixe permet à deux Azure Functions distinctes de partager la même collection de baux en utilisant des préfixes différents.
|**feedPollDelay**| **FeedPollDelay**| (Facultatif) Durée (en millisecondes) du délai s’écoulant entre le moment où toutes les modifications d’une partition sont purgées du flux et le moment où la partition est interrogée afin d’identifier de nouvelles modifications. La valeur par défaut est 5 000 millisecondes, soit 5 secondes.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle pour déclencher une tâche afin de calculer si les partitions sont réparties uniformément parmi les instances d’hôte connues. La valeur par défaut est 13 000 (13 secondes).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle selon lequel le bail est pris sur un bail représentant une partition. Si le bail n’est pas renouvelé dans cet intervalle, il expire et une autre instance devient propriétaire de la partition. La valeur par défaut est 60 000 (60 secondes).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle de renouvellement de tous les baux pour les partitions actuellement détenues par une instance. La valeur par défaut est 17 000 (17 secondes).
|**checkpointFrequency**| **CheckpointFrequency**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle entre les points de contrôle du bail. La valeur par défaut est toujours après chaque appel de fonction.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Facultatif) Quand cette propriété est définie, elle détermine le nombrer maximal d’éléments reçus par appel de fonction. Si des opérations de la collection surveillée sont effectuées via des procédures stockées, l’[étendue de transaction](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) est conservée lors de la lecture d’éléments à partir du flux de modification. Par conséquent, le nombre d’éléments reçus pourrait être supérieur à la valeur spécifiée de sorte que les éléments modifiés par la même transaction soient retournés dans le cadre d’un même lot atomique.
|**startFromBeginning**| **StartFromBeginning**| (Facultatif) Cette option indique au déclencheur de lire les modifications à partir du début de l’historique de changement de la collection au lieu de commencer à l’heure actuelle. La lecture depuis le début fonctionne uniquement au premier démarrage du déclencheur car, lors des exécutions suivantes, les points de contrôle sont déjà stockés. La définition de cette option sur `true` quand des baux ont déjà été créés est sans effet.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Déclencheur - utilisation

Le déclencheur nécessite une deuxième collection qu’il utilise pour stocker des _baux_ sur les partitions. La collection surveillée et la collection contenant les baux doivent être disponibles pour que le déclencheur fonctionne.

>[!IMPORTANT]
> Si plusieurs fonctions sont configurées pour utiliser un déclencheur Cosmos DB pour la même collection, chacune de ces fonctions doit utiliser une collection de bail dédiée ou spécifier un `LeaseCollectionPrefix` différent pour chaque fonction. Sinon, une seule des fonctions est déclenchée. Pour plus d’informations sur le préfixe, consultez la [section Configuration](#trigger---configuration).

Le déclencheur n’indique pas si un document a été mis à jour ou inséré, il fournit simplement le document lui-même. Si vous avez besoin de gérer les mises à jour et insertions différemment, vous pouvez le faire en implémentant des champs d’horodatage pour l’insertion ou la mise à jour.

## <a name="input"></a>Entrée

La liaison d’entrée Azure Cosmos DB utilise l’API SQL pour récupérer un ou plusieurs documents Azure Cosmos DB et les transmet au paramètre d’entrée de la fonction. L’ID du document ou les paramètres de requête peuvent être déterminés en fonction du déclencheur qui appelle la fonction.

> [!NOTE]
> Si la collection est [partitionnée](../cosmos-db/partition-data.md#logical-partitions), les opérations de recherche doivent également spécifier la valeur de clé de partition.
>

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Cette section contient les exemples suivants :

* [Déclencheur de file d’attente, rechercher l’ID à partir de JSON](#queue-trigger-look-up-id-from-json-c)
* [Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête](#http-trigger-look-up-id-from-query-string-c)
* [Déclencheur HTTP, ID de recherche à partir des données de routage](#http-trigger-look-up-id-from-route-data-c)
* [Déclencheur HTTP, ID de recherche à partir des données de routage, utilisation de SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Déclencheur HTTP, obtenir plusieurs documents, utilisation de SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Déclencheur HTTP, obtenir plusieurs documents, utilisation de DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Les exemples font référence à un type `ToDoItem` simple :

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Déclencheur de file d’attente, rechercher l’ID à partir de JSON 

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère un document unique. La fonction est déclenchée par un message de file d’attente qui contient un objet JSON. Le déclencheur de file d’attente analyse le JSON dans un objet de type `ToDoItemLookup` qui contient l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

>[!NOTE]
>Le paramètre de chaîne de requête HTTP respecte la casse.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Déclencheur HTTP, rechercher l’ID à partir des données d’itinéraire

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise des données de routage pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Déclencheur HTTP, rechercher l’ID à partir des données d’itinéraire, utilisation de SqlQuery

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère un document unique. Cette fonction est déclenchée par une requête HTTP qui utilise des données de routage pour spécifier l’ID à rechercher. Cet ID est utilisé pour récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

L’exemple montre comment utiliser une expression de liaison dans le paramètre `SqlQuery`. Vous pouvez valider des données de routage sur le paramètre `SqlQuery` comme indiqué, mais actuellement [vous ne pouvez pas valider des valeurs de chaînes de requête](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Si vous devez interroger uniquement à l’aide de l’ID, il est recommandé d’utiliser une recherche, comme dans les [exemples précédents](#http-trigger-look-up-id-from-query-string-c), car cela consomme moins d’[unités de requête](../cosmos-db/request-units.md). Les opérations de lecture à point (GET) sont [plus efficaces](../cosmos-db/optimize-cost-queries.md) que les requêtes par ID.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Déclencheur HTTP, obtenir plusieurs documents, utilisation de SqlQuery

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère une liste de documents. Cette fonction est déclenchée par une requête HTTP. La requête est spécifiée dans la propriété d’attribut `SqlQuery`.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Déclencheur HTTP, obtenir plusieurs documents, utilisation de DocumentClient

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui récupère une liste de documents. Cette fonction est déclenchée par une requête HTTP. Le code utilise une instance `DocumentClient` fournie par la liaisonAzure Cosmos DB pour lire une liste de documents. L’instance `DocumentClient` peut également être utilisée pour les opérations d’écriture.

> [!NOTE]
> Vous pouvez également utiliser l’interface [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) pour faciliter le test.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Cette section contient les exemples suivants :

* [Déclencheur de file d’attente, rechercher l’ID à partir de la chaîne](#queue-trigger-look-up-id-from-string-c-script)
* [Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête](#http-trigger-look-up-id-from-query-string-c-script)
* [Déclencheur HTTP, ID de recherche à partir des données de routage](#http-trigger-look-up-id-from-route-data-c-script)
* [Déclencheur HTTP, obtenir plusieurs documents, utilisation de SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Déclencheur HTTP, obtenir plusieurs documents, utilisation de DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Les exemples de déclencheur HTTP font référence à un type `ToDoItem` simple :

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Déclencheur de file d’attente, rechercher l’ID à partir de la chaîne

L’exemple suivant montre une liaison d’entrée Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction lit un document unique et met à jour la valeur texte du document.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Script C# :

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery

L’exemple suivant montre une liaison d’entrée Azure Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction récupère plusieurs documents spécifiés par une requête SQL, à l’aide d’un déclencheur de file d’attente pour personnaliser les paramètres de requête.

Le déclencheur de file d’attente fournit un paramètre `departmentId`. Un message de file d’attente de `{ "departmentId" : "Finance" }` retourne tous les enregistrements du service financier.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête

L’exemple suivant illustre une [fonction de script C#](functions-reference-csharp.md) qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Déclencheur HTTP, rechercher l’ID à partir des données d’itinéraire

L’exemple suivant illustre une [fonction de script C#](functions-reference-csharp.md) qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise des données de routage pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Déclencheur HTTP, obtenir plusieurs documents, utilisation de SqlQuery

L’exemple suivant illustre une [fonction de script C#](functions-reference-csharp.md) qui récupère une liste de documents. Cette fonction est déclenchée par une requête HTTP. La requête est spécifiée dans la propriété d’attribut `SqlQuery`.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Déclencheur HTTP, obtenir plusieurs documents, utilisation de DocumentClient

L’exemple suivant illustre une [fonction de script C#](functions-reference-csharp.md) qui récupère une liste de documents. Cette fonction est déclenchée par une requête HTTP. Le code utilise une instance `DocumentClient` fournie par la liaisonAzure Cosmos DB pour lire une liste de documents. L’instance `DocumentClient` peut également être utilisée pour les opérations d’écriture.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Cette section contient les exemples suivants qui lisent un document unique en spécifiant une valeur d’ID à partir de diverses sources :

* [Déclencheur de file d’attente, rechercher l’ID à partir de JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête](#http-trigger-look-up-id-from-query-string-javascript)
* [Déclencheur HTTP, ID de recherche à partir des données de routage](#http-trigger-look-up-id-from-route-data-javascript)
* [Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Déclencheur de file d’attente, rechercher l’ID à partir de JSON

L’exemple suivant montre une liaison d’entrée Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction lit un document unique et met à jour la valeur texte du document.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête

L’exemple suivant illustre une [fonction JavaScript](functions-reference-node.md) qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Déclencheur HTTP, rechercher l’ID à partir des données d’itinéraire

L’exemple suivant illustre une [fonction JavaScript](functions-reference-node.md) qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise des données de routage pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery

L’exemple suivant montre une liaison d’entrée Azure Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction récupère plusieurs documents spécifiés par une requête SQL, à l’aide d’un déclencheur de file d’attente pour personnaliser les paramètres de requête.

Le déclencheur de file d’attente fournit un paramètre `departmentId`. Un message de file d’attente de `{ "departmentId" : "Finance" }` retourne tous les enregistrements du service financier.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Cette section contient les exemples suivants qui lisent un document unique en spécifiant une valeur d’ID à partir de diverses sources :

* [Déclencheur de file d’attente, rechercher l’ID à partir de JSON](#queue-trigger-look-up-id-from-json-python)
* [Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête](#http-trigger-look-up-id-from-query-string-python)
* [Déclencheur HTTP, ID de recherche à partir des données de routage](#http-trigger-look-up-id-from-route-data-python)
* [Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Déclencheur de file d’attente, rechercher l’ID à partir de JSON

L’exemple suivant montre une liaison d’entrée Cosmos DB dans un fichier *function.json* et une [fonction Python](functions-reference-python.md) qui utilise la liaison. La fonction lit un document unique et met à jour la valeur texte du document.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Python :

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Déclencheur HTTP, rechercher l’ID à partir de la chaîne de requête

L’exemple suivant illustre une [fonction Python](functions-reference-python.md) qui récupère un document. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Voici le code Python :

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Déclencheur HTTP, rechercher l’ID à partir des données d’itinéraire

L’exemple suivant illustre une [fonction Python](functions-reference-python.md) qui récupère un document. La fonction est déclenchée par une requête HTTP qui utilise des données de routage pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document `ToDoItem` à partir de la base de données et de la collection spécifiées.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Voici le code Python :

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Déclencheur de file d’attente, obtenir plusieurs documents, utilisation de SqlQuery

L’exemple suivant montre une liaison d’entrée Azure Cosmos DB dans un fichier *function.json* et une [fonction Python](functions-reference-python.md) qui utilise la liaison. La fonction récupère plusieurs documents spécifiés par une requête SQL, à l’aide d’un déclencheur de file d’attente pour personnaliser les paramètres de requête.

Le déclencheur de file d’attente fournit un paramètre `departmentId`. Un message de file d’attente de `{ "departmentId" : "Finance" }` retourne tous les enregistrements du service financier.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Python :

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="javatabjava"></a>[Java](#tab/java)

Cette section contient les exemples suivants :

* [Déclencheur HTTP, rechercher l’ID dans la chaîne de requête - paramètre String](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Déclencheur HTTP, rechercher l’ID dans la chaîne de requête - paramètre POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Déclencheur HTTP, ID de recherche à partir des données de routage](#http-trigger-look-up-id-from-route-data-java)
* [Déclencheur HTTP, ID de recherche à partir des données de routage, utilisation de SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Déclencheur HTTP, obtenir plusieurs documents à partir des données de routage, utilisation de SqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Les exemples font référence à un type `ToDoItem` simple :

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>Déclencheur HTTP, recherche d’ID dans la chaîne de requête : paramètre String

L’exemple suivant illustre une fonction Java qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document à partir de la base de données et de la collection spécifiées sous la forme d’une chaîne.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@CosmosDBInput` sur les paramètres de fonction dont la valeur proviendrait de Cosmos DB.  Vous pouvez utiliser cette annotation avec des types Java natifs, des objets POJO ou des valeurs Null à l’aide de `Optional<T>`.

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Déclencheur HTTP, recherche d’ID dans la chaîne de requête : paramètre POJO

L’exemple suivant illustre une fonction Java qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise une chaîne de requête pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document à partir de la base de données et de la collection spécifiées. Le document est ensuite converti en une instance du POJO ```ToDoItem``` précédemment créé et passé en tant qu’argument à la fonction.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Déclencheur HTTP, rechercher l’ID à partir des données d’itinéraire

L’exemple suivant illustre une fonction Java qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise un paramètre de routage pour spécifier l’ID et la valeur de clé de partition à rechercher. Cet ID et cette valeur de clé de partition permettent de récupérer un document à partir de la base de données et de la collection spécifiées, en le retournant comme ```Optional<String>```.

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Déclencheur HTTP, rechercher l’ID à partir des données d’itinéraire, utilisation de SqlQuery

L’exemple suivant illustre une fonction Java qui récupère un document unique. La fonction est déclenchée par une requête HTTP qui utilise un paramètre de routage afin de spécifier l’ID à rechercher. Cet ID est utilisé pour récupérer un document à partir de la base de données et de la collection spécifiées, en convertissant le jeu de résultats en ```ToDoItem[]```, étant donné que de nombreux documents peuvent être retournés, en fonction des critères de la requête.

> [!NOTE]
> Si vous devez interroger uniquement à l’aide de l’ID, il est recommandé d’utiliser une recherche, comme dans les [exemples précédents](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), car cela consomme moins d’[unités de requête](../cosmos-db/request-units.md). Les opérations de lecture à point (GET) sont [plus efficaces](../cosmos-db/optimize-cost-queries.md) que les requêtes par ID.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>Déclencheur HTTP, obtention de plusieurs documents à partir de données d’itinéraire, utilisation de SqlQuery

L’exemple suivant illustre une fonction Java qui récupère plusieurs documents. La fonction est déclenchée par une requête HTTP qui utilise un paramètre de routage ```desc``` afin de spécifier la chaîne à rechercher dans le champ ```description```. Le terme recherché est utilisé pour récupérer une collection de documents à partir de la base de données et de la collection spécifiées, en convertissant le jeu de résultats en ```ToDoItem[]``` et en le passant en tant qu’argument à la fonction.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="input---attributes-and-annotations"></a>Sortie – Attributs et annotations

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs).

Le constructeur de l’attribut accepte le nom de la base de données et le nom de la collection. Pour plus d’informations sur ces paramètres et d’autres propriétés que vous pouvez configurer, consultez [la section de configuration suivante](#input---configuration).

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Dans la [bibliothèque du runtime des fonctions Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime), utilisez l’annotation `@CosmosDBOutput` sur des paramètres qui écrivent dans Cosmos DB. Le type de paramètre d’annotation doit être `OutputBinding<T>`, où `T` désigne un type Java natif ou un POJO.

---

## <a name="input---configuration"></a>Entrée - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `CosmosDB`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**     | n/a | Cette propriété doit être définie sur `cosmosDB`.        |
|**direction**     | n/a | Cette propriété doit être définie sur `in`.         |
|**name**     | n/a | Nom du paramètre de liaison qui représente le document dans la fonction.  |
|**databaseName** |**DatabaseName** |Base de données contenant le document.        |
|**collectionName** |**CollectionName** | Nom de la collection qui contient le document. |
|**id**    | **Id** | ID du document à récupérer. Cette propriété prend en charge les [expressions de liaison](./functions-bindings-expressions-patterns.md). Ne définissez pas à la fois les propriétés `id` et **sqlQuery**. Si vous ne définissez aucune des deux, l’ensemble de la collection est récupéré. |
|**sqlQuery**  |**SqlQuery**  | Requête SQL Azure Cosmos DB utilisée pour récupérer plusieurs documents. La propriété prend en charge les liaisons d’exécution, comme dans cet exemple : `SELECT * FROM c where c.departmentId = {departmentId}`. Ne définissez pas à la fois les propriétés `id` et `sqlQuery`. Si vous ne définissez aucune des deux, l’ensemble de la collection est récupéré.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Nom du paramètre d’application contenant votre chaîne de connexion Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Spécifie la valeur de la clé de partition pour la recherche. Peut inclure des paramètres de liaison. Requis pour les recherches dans les collections [partitionnées](../cosmos-db/partition-data.md#logical-partitions).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrée - utilisation

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Lorsque la fonction se termine correctement, toutes les modifications apportées au document d’entrée par le biais des paramètres d’entrée nommés sont automatiquement conservées.

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Lorsque la fonction se termine correctement, toutes les modifications apportées au document d’entrée par le biais des paramètres d’entrée nommés sont automatiquement conservées.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Les mises à jour ne sont pas effectuées automatiquement une fois la fonction terminée. Utilisez plutôt `context.bindings.<documentName>In` et `context.bindings.<documentName>Out` pour effectuer les mises à jour. Consultez l’exemple JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les données sont mises à la disposition de la fonction via un paramètre `DocumentList`. Les modifications apportées au document ne sont pas automatiquement conservées.

# <a name="javatabjava"></a>[Java](#tab/java)

À partir de la [bibliothèque du runtime des fonctions Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime), l’annotation [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) expose les données de Cosmos DB à la fonction. Vous pouvez utiliser cette annotation avec des types Java natifs, des objets POJO ou des valeurs Null à l’aide de `Optional<T>`.

---

## <a name="output"></a>Output

La liaison de sortie Azure Cosmos DB vous permet d’écrire un nouveau document dans une base de données Azure Cosmos DB en utilisant l’API SQL.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Cette section contient les exemples suivants :

* [Déclencheur de file d’attente, écrire un document](#queue-trigger-write-one-doc-c)
* [Déclencheur de file d’attente, écrire des documents à l’aide d’IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Les exemples font référence à un type `ToDoItem` simple :

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Déclencheur de la file d’attente, écriture d’un document

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui ajoute un document à une base de données, à l’aide des données fournies dans le message de Stockage File d’attente.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Déclencheur de la file d’attente, écriture de documents à l’aide d’IAsyncCollector

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui ajoute une collection de documents à une base de données, à l’aide de données fournies dans un JSON de message de file d’attente.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Cette section contient les exemples suivants :

* [Déclencheur de file d’attente, écrire un document](#queue-trigger-write-one-doc-c-script)
* [Déclencheur de file d’attente, écrire des documents à l’aide d’IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>Déclencheur de la file d’attente, écriture d’un document

L’exemple suivant montre une liaison de sortie Azure Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction utilise une liaison d’entrée de file d’attente pour une file d’attente qui reçoit le code JSON au format suivant :

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La fonction crée des documents Azure Cosmos DB au format suivant pour chaque enregistrement :

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code Script C# :

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Déclencheur de la file d’attente, écriture de documents à l’aide d’IAsyncCollector

Pour créer plusieurs documents, vous pouvez vous lier à `ICollector<T>` ou `IAsyncCollector<T>` où `T` est un des types pris en charge.

Cet exemple fait référence à un type simple `ToDoItem` :

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Voici le fichier function.json :

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre une liaison de sortie Azure Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction utilise une liaison d’entrée de file d’attente pour une file d’attente qui reçoit le code JSON au format suivant :

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La fonction crée des documents Azure Cosmos DB au format suivant pour chaque enregistrement :

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple suivant montre comment écrire un document dans une base de données Azure CosmosDB en tant que sortie d’une fonction.

Une définition de liaison est définie dans *function.json* , où *Type* possède la valeur `cosmosDB`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Pour écrire dans la base de données, transmettez un objet de document à la méthode `set` du paramètre Base de données.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

* [Déclencheur de file d’attente, enregistrer le message dans la base de données via la valeur de retour](#queue-trigger-save-message-to-database-via-return-value-java)
* [Déclencheur HTTP, enregistrer un document dans la base de données via la valeur de retour](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Déclencheur HTTP, enregistrer un document dans la base de données via OutputBinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Déclencheur HTTP, enregistrer plusieurs documents dans la base de données via OutputBinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Déclencheur de file d’attente, enregistrer un message dans un base de données via une valeur de retour

L’exemple suivant montre une fonction Java qui ajoute un document à une base de données, à l’aide des données fournies dans un message de Stockage File d’attente.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Déclencheur HTTP, enregistrer un document dans une base de données via une valeur de retour

L’exemple suivant illustre une fonction Java dont la signature est annotée avec ```@CosmosDBOutput``` et a une valeur de retour de type ```String```. Le document JSON retourné par la fonction sera automatiquement écrit dans la collection CosmosDB correspondante.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Déclencheur HTTP, enregistrer un document dans une base de données via OutputBinding

L’exemple suivant illustre une fonction Java qui écrit un document dans CosmosDB via un paramètre de sortie ```OutputBinding<T>```. Dans cet exemple, le paramètre ```outputItem``` doit être annoté avec ```@CosmosDBOutput```, non la signature de la fonction. L’utilisation de ```OutputBinding<T>``` permet à votre fonction de tirer parti de la liaison pour écrire le document dans CosmosDB tout en permettant aussi de retourner une valeur différente à l’appelant de la fonction, telle qu’un document JSON ou XML.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Déclencheur HTTP, enregistrer plusieurs documents dans une base de données via OutputBinding

L’exemple suivant illustre une fonction Java qui écrit plusieurs documents dans CosmosDB via un paramètre de sortie ```OutputBinding<T>```. Dans cet exemple, le paramètre ```outputItem``` est annoté avec ```@CosmosDBOutput```, non la signature de la fonction. Le paramètre de sortie, ```outputItem```, possède une liste d’objets ```ToDoItem``` comme type de paramètre de modèle. L’utilisation de ```OutputBinding<T>``` permet à votre fonction de tirer parti de la liaison pour écrire les documents dans CosmosDB tout en permettant aussi de retourner une valeur différente à l’appelant de la fonction, telle qu’un document JSON ou XML.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@CosmosDBOutput` sur les paramètres qui seraient écrits sur Cosmos DB.  Le type de paramètre d’annotation doit être ```OutputBinding<T>```, où T désigne un type Java natif ou un POJO.

---

## <a name="output---attributes-and-annotations"></a>Sortie – Attributs et annotations

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs).

Le constructeur de l’attribut accepte le nom de la base de données et le nom de la collection. Pour plus d’informations sur ces paramètres et d’autres propriétés que vous pouvez configurer, consultez [Sortie - configuration](#output---configuration). Voici un exemple d’attribut `CosmosDB` dans une signature de méthode :

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="javatabjava"></a>[Java](#tab/java)

L’annotation `CosmosDBOutput` est disponible pour écrire des données dans Cosmos DB. Vous pouvez appliquer l’annotation à la fonction ou à un paramètre de fonction. En cas d’utilisation sur la méthode de fonction, la valeur de retour de la fonction est ce qui est écrit dans Cosmos DB. Si vous utilisez l’annotation avec un paramètre, le type du paramètre doit être déclaré en tant que `OutputBinding<T>` où `T` un type Java natif ou un POJO.

---

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `CosmosDB`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**     | n/a | Cette propriété doit être définie sur `cosmosDB`.        |
|**direction**     | n/a | Cette propriété doit être définie sur `out`.         |
|**name**     | n/a | Nom du paramètre de liaison qui représente le document dans la fonction.  |
|**databaseName** | **DatabaseName**|Base de données contenant la collection dans laquelle le document est créé.     |
|**collectionName** |**CollectionName**  | Nom de la collection dans laquelle le document est créé. |
|**createIfNotExists**  |**CreateIfNotExists**    | Valeur booléenne indiquant si la collection doit être créée si elle n’existe pas déjà. La valeur par défaut est *false* car les nouvelles collections sont créées avec un débit réservé, ce qui a des conséquences sur la tarification. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Lorsque `CreateIfNotExists` a la valeur true, définit le chemin de la clé de partition pour la collection créée.|
|**collectionThroughput**|**CollectionThroughput**| Lorsque `CreateIfNotExists` a la valeur true, définit le [débit](../cosmos-db/set-throughput.md) de la collection créée.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Nom du paramètre d’application contenant votre chaîne de connexion Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

Par défaut, lorsque vous écrivez dans le paramètre de sortie de votre fonction, un document est créé dans votre base de données. Ce document comporte un GUID généré automatiquement en tant qu’ID du document. Vous pouvez spécifier l’ID du document de sortie en spécifiant la propriété `id` dans l’objet JSON qui est passé au paramètre de sortie.

> [!Note]
> Lorsque vous spécifier l’ID d’un document existant, il est remplacé par le nouveau document de sortie.

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| CosmosDB | [Codes d’erreur CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Paramètres host.json

Cette section décrit les paramètres de configuration globale disponibles pour cette liaison dans la version 2.x. Pour plus d’informations sur les paramètres de configuration globale dans la version 2.x, voir [Informations de référence sur le fichier host.json pour Azure Functions version 2.x](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------|
|GatewayMode|Passerelle|Le mode de connexion utilisé par la fonction lors de la connexion au service Azure Cosmos DB. Les options sont `Direct` et `Gateway`.|
|Protocol|Https|Le protocole de connexion utilisé par la fonction lors de la connexion au service Azure Cosmos DB.  Voir [l’explication des deux modes](../cosmos-db/performance-tips.md#networking).|
|leasePrefix|n/a|Préfixe de bail à utiliser dans toutes les fonctions d’une application.|

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le traitement de base de données serverless avec Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
