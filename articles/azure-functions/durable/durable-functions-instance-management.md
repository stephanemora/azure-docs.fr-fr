---
title: Gérer des instances dans Fonctions durables (Azure)
description: Découvrez comment gérer des instances dans l’extension Fonctions durables pour Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 6548b84f9599116aaa5055324bfa4625ea621ec3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087243"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gérer des instances dans Durable Functions dans Azure

Si vous utilisez l’extension [Durable Functions](durable-functions-overview.md) pour Azure Functions, ou si vous souhaitez le faire, vérifiez que vous en tirez le meilleur parti. Vous pouvez optimiser vos instances d’orchestration Durable Functions en découvrant comment les gérer. Cet article explique en détail chaque opération de gestion d’instance.

Vous pouvez démarrer et arrêter les instances, par exemple. Vous pouvez également interroger les instances, avec la possibilité de les interroger toutes ou en les filtrant. En outre, vous pouvez envoyer des événements à des instances, attendre la fin de l’orchestration et récupérer les URL de webhook de gestion HTTP. Cet article traite également des autres opérations de gestion, notamment du rembobinage des instances, du vidage de l’historique des instances et de la suppression d’un hub de tâches.

Dans Durable Functions, vous pouvez choisir parmi plusieurs options pour implémenter chacune de ces opérations de gestion. Cet article fournit des exemples qui utilisent [Azure Functions Core Tools](../functions-run-local.md) pour .NET (C#) et JavaScript.

## <a name="start-instances"></a>Démarrer des instances

Il est important de pouvoir démarrer une instance d’orchestration. Cela se révèle souvent nécessaire lorsque vous utilisez une liaison Durable Functions dans le déclencheur d’une autre fonction.

La méthode [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) sur [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou `startNew` sur `DurableOrchestrationClient` (JavaScript) démarre une nouvelle instance. Les instances de cette classe peuvent être obtenues à l’aide de la liaison `orchestrationClient`. En interne, cette méthode empile un message dans la file d’attente de contrôle, qui déclenche ensuite une fonction avec le nom spécifié utilisant la liaison de déclenchement `orchestrationTrigger`.

Cette opération asynchrone se termine quand le processus d’orchestration est correctement planifié. Le processus d’orchestration doit démarrer dans les 30 secondes. Si cela dure plus longtemps, vous verrez une `TimeoutException`.

> [!WARNING]
> Lorsque vous développez localement en JavaScript, vous devez définir la variable d’environnement `WEBSITE_HOSTNAME` sur `localhost:<port>` (par exemple, `localhost:7071`) pour utiliser les méthodes de `DurableOrchestrationClient`. Pour plus d’informations sur cette configuration, consultez le [problème GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Les paramètres de [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) sont les suivants :

* **Name** : Nom de la fonction orchestrator à planifier.
* **Entrée**: Toutes les données JSON sérialisables devant être passées comme entrée à la fonction orchestrator.
* **InstanceId** : (Facultatif) ID unique de l’instance. Si vous ne spécifiez pas ce paramètre, la méthode utilise un ID aléatoire.

Voici un exemple simple C# :

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

Les paramètres de `startNew` sont les suivants :

* **Name** : Nom de la fonction orchestrator à planifier.
* **InstanceId** : (Facultatif) ID unique de l’instance. Si vous ne spécifiez pas ce paramètre, la méthode utilise un ID aléatoire.
* **Entrée**: (Facultatif) Toutes les données JSON sérialisables devant être passées comme entrée à la fonction orchestrator.

Voici un exemple JavaScript simple :

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> Utilisez un identificateur aléatoire pour l’ID d’instance. Cela permet de garantir une distribution égale de la charge lors de la mise à l’échelle des fonctions de l’orchestrateur sur plusieurs machines virtuelles. Le moment qui convient pour l’utilisation des ID d’instance non aléatoires correspond au moment où l’ID provient d’une source externe ou lors de l’implémentation du modèle [orchestrateur singleton](durable-functions-singletons.md).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez aussi démarrer une instance directement via la commande [Azure Functions Core Tools](../functions-run-local.md) `durable start-new`. Les paramètres suivants sont pris en compte :

* **`function-name` (obligatoire)**  : Nom de la fonction à démarrer.
* **`input` (facultatif)**  : Entrée de la fonction, en ligne ou par le biais d’un fichier JSON. Pour les fichiers, ajoutez un préfixe au chemin du fichier avec `@`, par exemple, `@path/to/file.json`.
* **`id` (facultatif)**  : ID de l’instance d’orchestration. Si vous ne spécifiez pas ce paramètre, la commande utilise un GUID aléatoire.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches Durable Functions à utiliser. La valeur par défaut est DurableFunctionsHub. Vous pouvez également le définir dans [host.json](durable-functions-bindings.md#host-json) à l’aide de durableTask:HubName.

> [!NOTE]
> Les commandes Core Tools supposent que vous les exécutiez à partir du répertoire racine d’une application de fonction. Si vous fournissez explicitement les paramètres `connection-string-setting` et `task-hub-name`, vous pouvez exécuter les commandes à partir de n’importe quel répertoire. Même si vous pouvez exécuter ces commandes sans exécuter d’hôte d’application de fonction, vous constaterez peut-être que vous ne pouvez pas observer certains effets si l’hôte n’est pas exécuté. Par exemple, la commande `start-new` met en file d’attente un message de démarrage dans le hub de tâches cible, mais l’orchestration ne peut pas réellement s’exécuter à moins d’avoir un processus hôte d’application de fonction en cours d’exécution pouvant traiter le message.

La commande suivante démarre la fonction nommée HelloWorld et lui passe le contenu du fichier `counter-data.json` :

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Interroger des instances

Dans le cadre de votre gestion des orchestrations, vous devrez très probablement recueillir des informations sur l’état d’une instance d’orchestration (par exemple, si elle s’est terminée normalement ou si elle a échoué).

La méthode [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) sur la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou la méthode `getStatus` sur la classe `DurableOrchestrationClient` (JavaScript) interroge l’état d’une instance d’orchestration.

Elle prend `instanceId` (obligatoire), `showHistory` (facultatif), `showHistoryOutput` (facultatif) et `showInput` (facultatif, .NET uniquement) comme paramètres.

* **`showHistory`** : Si la valeur est définie sur `true`, la réponse contient l’historique d’exécution.
* **`showHistoryOutput`** : Si la valeur est définie sur `true`, l’historique d’exécution contient les sorties de l’activité.
* **`showInput`** : Si la valeur est définie sur `false`, la réponse ne contient pas l’entrée de la fonction. La valeur par défaut est `true`. (.NET uniquement)

La méthode retourne un objet JSON avec les propriétés suivantes :

* **Name** : Nom de la fonction orchestrator.
* **InstanceId** : ID d’instance de l’orchestration (doit être identique à l’entrée `instanceId`).
* **CreatedTime** : Heure à laquelle la fonction orchestrator a commencé à s’exécuter.
* **LastUpdatedTime** : Heure du dernier point de contrôle d’orchestration.
* **Entrée**: Entrée de la fonction sous forme de valeur JSON. Ce champ n’est pas rempli si `showInput` est false.
* **CustomStatus** : État personnalisé de l’orchestration au format JSON.
* **Sortie**: Sortie de la fonction sous forme de valeur JSON (si cette fonction est terminée). En cas d’échec de la fonction d’orchestrateur, cette propriété inclut les détails de l’échec. En cas d’interruption de la fonction de l’orchestrateur, cette propriété indique la raison de cette interruption (s’il y en a une).
* **RuntimeStatus** : L’une des valeurs suivantes :
  * **Pending** : L’instance a été planifiée mais n’est pas encore en cours d’exécution.
  * **Running** : L’instance a commencé à s’exécuter.
  * **Completed** : L’instance s’est terminée normalement.
  * **ContinuedAsNew** : L’instance a redémarré automatiquement avec un nouvel historique. Il s’agit d’un état temporaire.
  * **Failed** : Échec de l’instance avec une erreur.
  * **Terminated** : L’instance a été brusquement arrêtée.
* **History** : L’historique d’exécution de l’orchestration. Ce champ est renseigné uniquement si `showHistory` est défini sur `true`.

Cette méthode renvoie `null` si l’instance n’existe pas ou n’est pas encore exécutée.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez aussi obtenir l’état d’une instance d’orchestration directement via la commande [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status`. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration.
* **`show-input` (facultatif)**  : Si la valeur est définie sur `true`, la réponse contient l’entrée de la fonction. La valeur par défaut est `false`.
* **`show-output` (facultatif)**  : Si la valeur est définie sur `true`, la réponse contient la sortie de la fonction. La valeur par défaut est `false`.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches Durable Functions à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Vous pouvez également le définir dans [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

La commande suivante récupère l’état (y compris l’entrée et la sortie) d’une instance avec l’ID d’instance d’orchestration 0ab8c55a66644d68a3a8b220b12d209c. Cela suppose que la commande `func` est exécutée à partir du répertoire racine de l’application de fonction :

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Vous pouvez utiliser la commande `durable get-history` pour récupérer l’historique d’une instance d’orchestration. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches Durable Functions à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Vous pouvez également le définir dans host.json à l’aide de durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Interroger toutes les instances

Au lieu d’interroger chaque instance de votre orchestration une par une, il peut s’avérer plus efficace de toutes les interroger en même temps.

Vous pouvez utiliser la méthode `GetStatusAsync` (.NET) ou `getStatusAll` (JavaScript) pour interroger les états de toutes les instances de l’orchestration. Dans .NET, si vous souhaitez l’annuler, vous pouvez passer un objet `CancellationToken`. La méthode renvoie des objets avec les mêmes propriétés que la méthode `GetStatusAsync` avec des paramètres.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez aussi interroger des instances directement à l’aide de la commande [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances`. Les paramètres suivants sont pris en compte :

* **`top` (facultatif)**  : Cette commande prend en charge la pagination. Ce paramètre correspond au nombre d’instances récupérées par demande. La valeur par défaut est de 10.
* **`continuation-token` (facultatif)**  : Jeton qui indique la page ou la section des instances à récupérer. Chaque exécution de `get-instances` retourne un jeton à l’ensemble suivant d’instances.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches Durable Functions à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Vous pouvez également le définir dans [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Interroger les instances en appliquant des filtres

Il est possible que vous n’ayez pas vraiment besoin de toutes les informations fournies par une requête d’instance standard. Peut-être avez-vous seulement besoin de connaître l’heure de création de l’orchestration ou de l’état de son exécution ? Vous pouvez affiner votre requête en appliquant des filtres.

Utilisez la méthode `GetStatusAsync` (.NET) ou `getStatusBy` (JavaScript) pour obtenir la liste des instances d’orchestration qui correspondent à un ensemble de filtres prédéfinis.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Dans Azure Functions Core Tools, vous pouvez également utiliser la commande `durable get-instances` avec des filtres. Outre les paramètres `top`, `continuation-token`, `connection-string-setting` et `task-hub-name` mentionnés ci-dessus, vous pouvez utiliser trois paramètres de filtre (`created-after`, `created-before` et `runtime-status`).

* **`created-after` (facultatif)**  : Récupère les instances créées après cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`created-before` (facultatif)**  : Récupère les instances créées avant cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`runtime-status` (facultatif)**  : Récupérez les instances avec un état particulier (par exemple, En cours d’exécution ou Terminé). Peut fournir plusieurs états (séparés par des espaces).
* **`top` (facultatif)**  : Nombre d’instances récupérées par requête. La valeur par défaut est de 10.
* **`continuation-token` (facultatif)**  : Jeton qui indique la page ou la section des instances à récupérer. Chaque exécution de `get-instances` retourne un jeton à l’ensemble suivant d’instances.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches Durable Functions à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Vous pouvez également le définir dans [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

Si vous n’appliquez pas de filtres (`created-after`, `created-before` ou `runtime-status`), la commande récupère simplement les instances `top`, sans se soucier de l’état de l’exécution ou de l’heure de création.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Arrêter les instances

Si vous disposez d’une instance d’orchestration dont l’exécution est trop longue, ou si vous devez simplement l’arrêter avant la fin pour une raison quelconque, vous avez la possibilité de l’arrêter.

Vous pouvez utiliser la méthode [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou la méthode `terminate` de la classe `DurableOrchestrationClient` (JavaScript). Les deux paramètres sont une chaîne `instanceId` et `reason`, qui est écrite dans les journaux et dans l’état de l’instance. Une instance arrêtée cesse de s’exécuter dès qu’elle atteint le point `await` (.NET) ou `yield` (JavaScript) suivant, ou elle s’arrête immédiatement si elle se trouve déjà sur un `await` ou un `yield`.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Il n’est pas possible de propager l’arrêt des instances. Les fonctions et sous-orchestrations de l’activité s’exécutent jusqu’à la fin, même si l’instance d’orchestration qui les a appelées a été arrêtée.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez aussi arrêter une instance d’orchestration directement, à l’aide de la commande [Azure Functions Core Tools](../functions-run-local.md) `durable terminate`. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration à arrêter.
* **`reason` (facultatif)**  : Raison de l’arrêt.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches Durable Functions à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Vous pouvez également le définir dans [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

La commande suivante arrête une instance d’orchestration avec l’ID 0ab8c55a66644d68a3a8b220b12d209c :

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Envoyer des événements à des instances

Dans certains scénarios, il est important que vos fonctions d’orchestrateur soient en mesure d’attendre et d’écouter les événements externes. Cela comprend les [fonctions de supervision](durable-functions-concepts.md#monitoring) et les fonctions qui attendent les [interactions humaines](durable-functions-concepts.md#human).

Vous pouvez envoyer des notifications d’événements aux instances en cours d’exécution à l’aide de la méthode [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou de la méthode `raiseEvent` de la classe `DurableOrchestrationClient` (JavaScript). Les instances pouvant gérer ces événements sont celles en attente d’un appel à[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) ou `waitForExternalEvent` (JavaScript).

Les paramètres de [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) et `raiseEvent` (JavaScript) sont les suivants :

* **InstanceId** : ID unique de l’instance.
* **EventName** : Nom de l’événement à envoyer.
* **EventData** : Charge utile JSON sérialisable à envoyer à l’instance.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> S’il n’existe aucune instance d’orchestration avec l’ID d’instance spécifié ou si l’instance n’attend pas le nom d’événement spécifié, le message d’événement est ignoré. Pour plus d’informations sur ce comportement, consultez le [problème GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez aussi déclencher un événement pour une instance d’orchestration directement, à l’aide de la commande [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event`. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration.
* **`event-name` (facultatif)**  : Nom de l’événement à déclencher. Par défaut, il s’agit de `$"Event_{RandomGUID}"`.
* **`event-data` (facultatif)**  : Données à envoyer à l’instance d’orchestration. Il peut s’agir d’un chemin de fichier JSON. Vous pouvez également fournir les données directement sur la ligne de commande.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches Durable Functions à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Vous pouvez également le définir dans [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Attendre la fin de l’orchestration

Dans les orchestrations à exécution longue, vous pouvez souhaiter attendre les résultats de l’orchestration. Dans ce cas, il est également utile de définir un délai d’attente pour l’orchestration. Si le délai d’attente est dépassé, l’état de l’orchestration doit être retourné à la place des résultats.

La classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) expose une API [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) dans .NET. Vous pouvez utiliser cette API pour obtenir la sortie d’une instance d’orchestration de façon synchrone. En JavaScript, la classe `DurableOrchestrationClient` expose une API `waitForCompletionOrCreateCheckStatusResponse` dans le même but. Quand elles ne sont pas définies, les méthodes utilisent la valeur par défaut de 10 secondes pour `timeout` et de 1 seconde pour `retryInterval`.  

Voici un exemple de fonction de déclencheur HTTP qui montre comment utiliser cette API :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Appelez la fonction avec la ligne suivante. Utilisez une valeur de 2 secondes pour le délai d’attente et de 0,5 seconde pour l’intervalle avant nouvelle tentative :

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Selon le temps nécessaire pour obtenir la réponse de l’instance d’orchestration, il existe deux cas de figure :

* Les instances d’orchestration sont terminées dans le délai imparti (dans ce cas, 2 secondes). La réponse est la sortie d’instance d’orchestration qui est remise de manière synchrone :

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Les instances d’orchestration ne peuvent pas être terminées dans le délai imparti. La réponse est celle par défaut qui est décrite dans [Découverte de l’URL de l’API HTTP](durable-functions-http-api.md) :

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Le format des URL webhook peut varier selon la version de l’hôte Azure Functions que vous exécutez. L’exemple précédent concerne l’hôte Azure Functions 2.x.

## <a name="retrieve-http-management-webhook-urls"></a>Récupérer les URL webhook de gestion HTTP

Vous pouvez utiliser un système externe pour superviser ou déclencher des événements dans une orchestration. Les systèmes externes peuvent communiquer avec Durable Functions via les URL webhook qui font partie de la réponse par défaut décrite dans [Découverte de l’URL de l’API HTTP](durable-functions-http-api.md). Toutefois, les URL webhook sont également accessibles par programmation dans le client d’orchestration ou dans une fonction d’activité. Pour cela, vous pouvez utiliser la méthode [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou la méthode `createHttpManagementPayload` de la classe `DurableOrchestrationClient` (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) et `createHttpManagementPayload` ont un seul caractère :

* **instanceId** : ID unique de l’instance.

Les méthodes retournent une instance de [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) ou un objet (JavaScript) avec les propriétés de chaîne suivantes :

* **Id** : ID d’instance de l’orchestration (doit être identique à l’entrée `InstanceId`).
* **StatusQueryGetUri** : L’URL de l’état de l’instance d’orchestration.
* **SendEventPostUri** : L’URL « Raise event » de l’instance d’orchestration.
* **TerminatePostUri** : L’URL « d’arrêt » de l’instance d’orchestration.
* **RewindPostUri** : URL de «rembobinage» de l’instance d’orchestration.

Les fonctions d’activité peuvent envoyer une instance de ces objets à des systèmes externes pour superviser ou déclencher des événements sur une orchestration :

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewind-instances-preview"></a>Rembobiner des instances (préversion)

Si une orchestration échoue pour une raison quelconque, vous pouvez *rembobiner* l’instance vers un état sain précédent à l’aide d’une API intégrée à cet effet.

> [!NOTE]
> Cette API n’est pas destinée à se substituer à des stratégies appropriées de nouvelles tentatives et de gestion des erreurs. Son utilisation est plutôt uniquement réservée dans les cas où les instances d’orchestration échouent pour des raisons inattendues. Pour plus d’informations sur les stratégies de nouvelles tentatives et de gestion des erreurs, consultez la rubrique [Gestion des erreurs](durable-functions-error-handling.md).

Utilisez l’API [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) ou `rewindAsync` (JavaScript) pour que l’orchestration revienne à l’état *En cours d’exécution*. Réexécutez les activités ou les sous-orchestrations qui ont échoué et provoqué l’échec de l’orchestration.

Par exemple, supposons que vous ayez un workflow impliquant une suite d’[approbations humaines](durable-functions-concepts.md#human). Supposons que vous ayez une suite de fonctions d’activité qui informe une personne que son approbation est nécessaire, et qui attend la réponse en temps réel. Une fois que toutes les activités d’approbation ont reçu des réponses ou ont expiré, supposons qu’une autre activité échoue en raison d’un problème de configuration d’application (par exemple, une chaîne de connexion de base de données non valide). Il en résulte un échec de l’orchestration, survenu en profondeur dans le workflow. Avec l’API `RewindAsync` (.NET) ou `rewindAsync` (JavaScript), un administrateur d’application peut corriger l’erreur de configuration et rembobiner (rewind) l’orchestration ayant échoué jusqu’à l’état situé immédiatement avant l’échec. Aucune des étapes nécessitant une interaction humaine n’a besoin d’être réapprouvée et l’orchestration peut désormais s’effectuer correctement.

> [!NOTE]
> La fonctionnalité de *rembobinage* ne prend pas en charge le rembobinage des instances d’orchestration qui utilisent des minuteurs durables.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez aussi rembobiner une instance d’orchestration directement, à l’aide de la commande [Azure Functions Core Tools](../functions-run-local.md) `durable rewind`. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration.
* **`reason` (facultatif)**  : Motif de rembobinage de l’instance d’orchestration.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches Durable Functions à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Vous pouvez également le définir dans [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Vider l’historique des instances

Pour supprimer toutes les données associées à une orchestration, vous pouvez vider l’historique des instances. Par exemple, si vous avez des lignes Table Azure et des objets blob de messages volumineux, vous souhaiterez peut-être vous en débarrasser. Pour ce faire, utilisez l’API [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_).

> [!NOTE]
> L’API `PurgeInstanceHistoryAsync` est actuellement disponible uniquement pour C#.

 La méthode a deux surcharges. La première vide l’historique par ID d’instance d’orchestration :

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Le deuxième exemple montre une fonction déclenchée par un minuteur qui purge l’historique de toutes les instances d’orchestration terminées après l’intervalle de temps spécifié. Dans ce cas, elle supprime les données de toutes les instances terminées depuis 30 jours ou plus. Elle est planifiée pour s’exécuter une fois par jour à 00:00 :

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> Pour que le processus de la fonction déclenchée sur des critères d’heure réussisse, l’état d’exécution doit être **Completed** (Terminé), **Terminated** (Arrêté) ou **Failed** (Échec).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez aussi vider l’historique d’une instance d’orchestration à l’aide de la commande [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history`. Tout comme dans le deuxième exemple C# de la section précédente, elle vide l’historique de toutes les instances d’orchestration créées dans un intervalle de temps spécifié. Vous pouvez filtrer encore davantage les instances vidées, selon leur état d’exécution. La commande a plusieurs paramètres :

* **`created-after` (facultatif)**  : Purge l’historique des instances créées après cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`created-before` (facultatif)**  : Purge l’historique des instances créées avant cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`runtime-status` (facultatif)**  : Videz l’historique des instances avec un état particulier (par exemple, Running ou Completed). Peut fournir plusieurs états (séparés par des espaces).
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches Durable Functions à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Vous pouvez également le définir dans [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

La commande suivante supprime l’historique de toutes les instances qui ont été créées avant le 14 novembre 2018 à 19:35 (UTC) et qui ont échoué.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Supprimer un hub de tâches

À l’aide de la commande [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub`, vous pouvez supprimer tous les artefacts de stockage associés à un hub de tâches spécifique. Cela inclut les objets blob, les files d’attente et les tables de stockage Azure. La commande a deux paramètres :

* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches Durable Functions à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Vous pouvez également le définir dans [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

La commande suivante supprime toutes les données de stockage Azure associées au hub de tâches `UserTest`.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment utiliser les API HTTP pour la gestion des instances](durable-functions-http-api.md)
