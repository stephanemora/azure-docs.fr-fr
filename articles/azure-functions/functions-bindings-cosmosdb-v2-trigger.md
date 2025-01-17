---
title: Déclencheur Azure Cosmos DB pour Azure Functions 2.x et supérieur
description: Apprenez à utiliser le déclencheur Azure Cosmos DB dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f9e8790a05181f095ff28b154a5a264cb38295e4
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994320"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Déclencheur Azure Cosmos DB pour Azure Functions 2.x et supérieur

Le déclencheur Azure Cosmos DB utilise le [flux de modification Azure Cosmos DB](../cosmos-db/change-feed.md) pour écouter les insertions et mises à jour sur plusieurs partitions. Le flux de modification publie les insertions et mises à jour, pas les suppressions.

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

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

Les applications utilisant la [version d’extension 4.x](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) ou une version ultérieure de Cosmos DB auront des propriétés d’attribut différentes (présentées ci-dessous). Cet exemple fait référence à un type simple `ToDoItem`.

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

```cs
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "databaseName",
            containerName: "containerName",
            Connection = "CosmosDBConnectionSetting",
            LeaseContainerName = "leases",
            CreateLeaseContainerIfNotExists = true)]IReadOnlyList<ToDoItem> input, ILogger log)
        {
            if (input != null && input.Count > 0)
            {
                log.LogInformation("Documents modified " + input.Count);
                log.LogInformation("First document Id " + input[0].Id);
            }
        }
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

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

# <a name="java"></a>[Java](#tab/java)

La fonction est appelée quand des insertions ou des mises à jour figurent dans la base de données et la collection spécifiées.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

L’exemple suivant montre comment exécuter une fonction lorsque des données sont modifiées dans Cosmos DB.

```json
{
  "type": "cosmosDBTrigger",
  "name": "Documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```

Dans le fichier _run.ps1_, vous avez accès au document qui déclenche la fonction avec le paramètre `$Documents`.

```powershell
param($Documents, $TriggerMetadata) 

Write-Host "First document Id modified : $($Documents[0].id)" 
```

# <a name="python"></a>[Python](#tab/python)

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

---

## <a name="attributes-and-annotations"></a>Attributs et annotations

# <a name="c"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs).

Le constructeur de l’attribut accepte le nom de la base de données et le nom de la collection. Pour plus d’informations sur ces paramètres et d’autres propriétés que vous pouvez configurer, consultez [Déclencheur - configuration](#configuration). Voici un exemple d’attribut `CosmosDBTrigger` dans une signature de méthode :

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Dans la [version d’extension 4.x](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher), certains paramètres et certaines propriétés ont été supprimés ou renommés. Pour obtenir des informations détaillées sur ces changements, consultez [Déclencheur - configuration](#configuration). Voici un exemple d’attribut `CosmosDBTrigger` dans une signature de méthode. Il fait référence à un type simple `ToDoItem` :

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

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "container", Connection = "CosmosDBConnectionSetting")]
        IReadOnlyList<ToDoItem> documents,  
        ILogger log)
    {
        ...
    }
```

Pour obtenir un exemple complet de l’une des versions d’extension, consultez [Déclencheur](#example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="java"></a>[Java](#tab/java)

À partir de la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@CosmosDBInput` sur les paramètres qui lisent des données de Cosmos DB.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Les attributs ne sont pas pris en charge par PowerShell.

# <a name="python"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

---

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `CosmosDBTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `cosmosDBTrigger`. |
|**direction** | n/a | Cette propriété doit être définie sur `in`. Ce paramètre est défini automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de variable utilisé dans le code de fonction, qui représente la liste des documents modifiés. |
|**connectionStringSetting** <br> ou <br> **connection**|**ConnectionStringSetting** <br> ou <br> **Connection**| Nom d’un paramètre d’application ou d’une collection de paramètres qui spécifie la façon de se connecter au compte Azure Cosmos DB qui est surveillé. Voir [Connexions](#connections) <br><br> Dans la [version 4.x de l’extension], cette propriété est appelée `connection`. |
|**databaseName**|**DatabaseName**  | Nom de la base de données Azure Cosmos DB contenant la collection surveillée. |
|**collectionName** <br> ou <br> **containerName** |**CollectionName** <br> ou <br> **ContainerName** | Nom de la collection surveillée. <br><br> Dans la [version 4.x de l’extension], cette propriété est appelée `ContainerName`. |
|**leaseConnectionStringSetting** <br> ou <br> **leaseConnection** | **LeaseConnectionStringSetting** <br> ou <br> **LeaseConnection** | (Facultatif) Nom d’un paramètre d’application ou d’une collection de paramètres qui spécifie la façon de se connecter au compte Azure Cosmos DB qui détient la collection de baux. Voir [Connexions](#connections) <br><br> S’il n’est pas défini, la valeur `connectionStringSetting` est utilisée. Ce paramètre est automatiquement défini lorsque la liaison est créée dans le portail. La chaîne de connexion de la collection de baux doit avoir des autorisations en écriture. <br><br> Dans la [version 4.x de l’extension], cette propriété est appelée `leaseConnection`. Si elle n’est pas définie, la valeur `connection` sera utilisée. |
|**leaseDatabaseName** |**LeaseDatabaseName** | (Facultatif) Nom de la base de données contenant la collection utilisée pour stocker des baux. S’il n’est pas défini, la valeur du paramètre `databaseName` est utilisée. Ce paramètre est automatiquement défini lorsque la liaison est créée dans le portail. |
|**leaseCollectionName** <br> ou <br> **leaseContainerName** | **LeaseCollectionName** <br> ou <br> **LeaseContainerName** | (Facultatif) Nom de la collection utilisée pour stocker des baux. S’il n’est pas défini, la valeur `leases` est utilisée. <br><br> Dans la [version 4.x de l’extension], cette propriété est appelée `LeaseContainerName`. |
|**createLeaseCollectionIfNotExists** <br> ou <br> **createLeaseContainerIfNotExists** | **CreateLeaseCollectionIfNotExists** <br> ou <br> **CreateLeaseContainerIfNotExists** | (Facultatif) Lorsque la valeur est définie sur `true`, la collection de baux est créée automatiquement si elle n’existe pas. La valeur par défaut est `false`. <br><br> Dans la [version 4.x de l’extension], cette propriété est appelée `CreateLeaseContainerIfNotExists`. |
|**leasesCollectionThroughput** <br> ou <br> **leasesContainerThroughput**| **LeasesCollectionThroughput** <br> ou <br> **LeasesContainerThroughput**| (Facultatif) Définit le nombre d’unités de requête à attribuer lors de la création de la collection de baux. Ce paramètre est utilisé uniquement quand `createLeaseCollectionIfNotExists` est défini sur `true`. Ce paramètre est automatiquement défini lors de la création de la liaison à l’aide du portail. <br><br> Dans la [version 4.x de l’extension], cette propriété est appelée `LeasesContainerThroughput`. |
|**leaseCollectionPrefix** <br> ou <br> **leaseContainerPrefix**| **LeaseCollectionPrefix** <br> ou <br> **leaseContainerPrefix** | (Facultatif) Lorsque cette valeur est définie, elle est ajoutée en tant que préfixe aux baux créés dans la collection de baux pour cette fonction. L’utilisation d’un préfixe permet à deux Azure Functions distinctes de partager la même collection de baux en utilisant des préfixes différents. <br><br> Dans la [version 4.x de l’extension], cette propriété est appelée `LeaseContainerPrefix`. |
|**feedPollDelay**| **FeedPollDelay**| (Facultatif) Durée (en millisecondes) du délai s’écoulant entre le moment où toutes les modifications d’une partition sont purgées du flux et le moment où la partition est interrogée afin d’identifier de nouvelles modifications. La valeur par défaut est 5 000 millisecondes, soit 5 secondes.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle pour déclencher une tâche afin de calculer si les partitions sont réparties uniformément parmi les instances d’hôte connues. La valeur par défaut est 13 000 (13 secondes).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle selon lequel le bail est pris sur un bail représentant une partition. Si le bail n’est pas renouvelé dans cet intervalle, il expire et une autre instance devient propriétaire de la partition. La valeur par défaut est 60 000 (60 secondes).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle de renouvellement de tous les baux pour les partitions actuellement détenues par une instance. La valeur par défaut est 17 000 (17 secondes).
|**checkpointInterval**| **CheckpointInterval**| (Facultatif) Quand ce paramètre est défini, il spécifie, en millisecondes, l’intervalle entre les points de contrôle du bail. La valeur par défaut est toujours après chaque appel de fonction. <br><br> Cette propriété n’est pas disponible dans la [version 4.x de l’extension]. |
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Facultatif) Quand cette propriété est définie, elle détermine le nombrer maximal d’éléments reçus par appel de fonction. Si des opérations de la collection supervisée sont effectuées par le biais de procédures stockées, l’[étendue de transaction](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) est conservée lors de la lecture d’éléments à partir du flux de changement. Par conséquent, le nombre d’éléments reçus pourrait être supérieur à la valeur spécifiée de sorte que les éléments modifiés par la même transaction soient retournés dans le cadre d’un même lot atomique.
|**startFromBeginning**| **StartFromBeginning**| (Facultatif) Cette option indique au déclencheur de lire les modifications à partir du début de l’historique de changement de la collection au lieu de commencer à l’heure actuelle. La lecture depuis le début fonctionne uniquement au premier démarrage du déclencheur car, lors des exécutions suivantes, les points de contrôle sont déjà stockés. La définition de cette option sur `true` quand des baux ont déjà été créés est sans effet. |
|**preferredLocations**| **PreferredLocations**| (Facultatif) Définit les endroits par défaut (régions) des comptes de base de données géorépliqués dans le service Azure Cosmos DB. Les valeurs doivent être séparées par des virgules. Par exemple, « USA Est, USA Centre Sud, Europe Nord ». |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-cosmosdb-connections](../../includes/functions-cosmosdb-connections.md)]

## <a name="usage"></a>Usage

Le déclencheur nécessite une deuxième collection qu’il utilise pour stocker des _baux_ sur les partitions. La collection surveillée et la collection contenant les baux doivent être disponibles pour que le déclencheur fonctionne.

>[!IMPORTANT]
> Si plusieurs fonctions sont configurées pour utiliser un déclencheur Cosmos DB pour la même collection, chacune de ces fonctions doit utiliser une collection de bail dédiée ou spécifier un `LeaseCollectionPrefix` différent pour chaque fonction. Sinon, une seule des fonctions est déclenchée. Pour plus d’informations sur le préfixe, consultez la [section Configuration](#configuration).

Le déclencheur n’indique pas si un document a été mis à jour ou inséré, il fournit simplement le document lui-même. Si vous avez besoin de gérer les mises à jour et insertions différemment, vous pouvez le faire en implémentant des champs d’horodatage pour l’insertion ou la mise à jour.

## <a name="next-steps"></a>Étapes suivantes

- [Lire un document Azure Cosmos DB (liaison d’entrée)](./functions-bindings-cosmosdb-v2-input.md)
- [Enregistrer les modifications apportées à un document Azure Cosmos DB (liaison de sortie)](./functions-bindings-cosmosdb-v2-output.md)

[Version 4.x de l’extension]: ./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher