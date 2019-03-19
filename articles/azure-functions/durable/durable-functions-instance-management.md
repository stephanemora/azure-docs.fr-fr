---
title: Gérer des instances dans Fonctions durables (Azure)
description: Découvrez comment gérer des instances dans l’extension Fonctions durables pour Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547308"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gérer des instances dans Durable Functions dans Azure

Si vous utilisez le [fonctions durables](durable-functions-overview.md) extension pour Azure Functions, ou que vous souhaitiez démarrer ce faisant, assurez-vous que vous obtenez le meilleur parti en tirer. Vous pouvez optimiser vos instances d’orchestration de fonctions durables en savoir plus sur comment les gérer. Cet article explique en détail chaque opération de gestion d’instance.

Vous pouvez démarrer et arrêter les instances, par exemple, et vous pouvez interroger les instances, y compris la possibilité d’interroger toutes les instances et les instances de la requête avec des filtres. En outre, vous pouvez envoyer des événements à des instances, attendre la fin de l’orchestration et récupérer l’URL de webhook de gestion HTTP. Cet article traite des autres opérations de gestion, trop, notamment rembobinage des instances, la purge l’historique des instances et la suppression d’un hub de tâches.

Dans fonctions durables, vous disposez des options pour la façon dont vous souhaitez implémenter chacune de ces opérations de gestion. Cet article fournit des exemples qui utilisent la [Azure Functions Core Tools](../functions-run-local.md) pour les deux .NET (C#) et JavaScript.

## <a name="start-instances"></a>Démarrer des instances

Il est important de pouvoir démarrer une instance d’orchestration. Cette situation est fréquente lorsque vous utilisez une liaison de fonctions durables dans le déclencheur d’une autre fonction.

Le [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) méthode sur le [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou `startNew` sur la `DurableOrchestrationClient` (JavaScript) démarre une nouvelle instance. Acquérir des instances de cette classe à l’aide de la `orchestrationClient` liaison. En interne, cette méthode empile un message dans la file d’attente de contrôle, qui déclenche ensuite une fonction avec le nom spécifié utilisant la liaison de déclenchement `orchestrationTrigger`.

Cette opération asynchrone se termine quand le processus d’orchestration est correctement planifié. Le processus d’orchestration doit démarrer dans les 30 secondes. S’il faut plus de temps, vous verrez un `TimeoutException`.

> [!WARNING]
> Lorsque vous développez localement dans JavaScript, définir la variable d’environnement `WEBSITE_HOSTNAME` à `localhost:<port>` (par exemple, `localhost:7071`) à utiliser des méthodes sur `DurableOrchestrationClient`. Pour plus d’informations sur cette configuration, consultez le [problème GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

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
> Utilisez un identificateur aléatoire pour l’ID d’instance. Cela permet de garantir une distribution égale de la charge lorsque vous mettez à l’échelle les fonctions d’orchestrateur sur plusieurs machines virtuelles. L’heure appropriée à utiliser les ID d’instance non aléatoires est lorsque l’ID doit provenir d’une source externe, ou lorsque vous implémentez le [orchestrateur singleton](durable-functions-singletons.md) modèle.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez également démarrer une instance directement à l’aide du [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` commande. Les paramètres suivants sont pris en compte :

* **`function-name` (obligatoire)**  : Nom de la fonction à démarrer.
* **`input` (facultatif)**  : Entrée à la fonction inline ou d’un fichier JSON. Pour les fichiers, ajoutez un préfixe pour le chemin d’accès au fichier avec `@`, tel que `@path/to/file.json`.
* **`id` (facultatif)**  : ID de l’instance d’orchestration. Si vous ne spécifiez pas ce paramètre, la commande utilise un GUID aléatoire.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches fonctions durables à utiliser. La valeur par défaut est DurableFunctionsHub. Vous pouvez également le définir [host.json](durable-functions-bindings.md#host-json) à l’aide de durableTask:HubName.

> [!NOTE]
> Commandes des outils de base supposent que vous les exécutez à partir du répertoire racine d’une application de fonction. Si vous fournissez explicitement le `connection-string-setting` et `task-hub-name` paramètres, vous pouvez exécuter les commandes à partir de n’importe quel répertoire. Bien que vous pouvez exécuter ces commandes sans un hôte d’application de fonction en cours d’exécution, vous constaterez peut-être que vous ne pouvez pas observer certains effets, sauf si l’hôte est en cours d’exécution. Par exemple, le `start-new` empile un message de démarrage dans le hub de tâches cible, mais l’orchestration ne s’exécute en fait, sauf si un processus hôte d’application de fonction en cours d’exécution qui peut traiter le message de commande.

La commande suivante démarre la fonction nommée HelloWorld et transmet le contenu du fichier `counter-data.json` à celui-ci :

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instances de la requête

Dans le cadre de votre travail pour gérer vos orchestrations, vous devrez très probablement recueillir des informations sur l’état d’une instance d’orchestration (par exemple, si elle a s’est terminée normalement ou échoué).

La méthode [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) sur la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou la méthode `getStatus` sur la classe `DurableOrchestrationClient` (JavaScript) interroge l’état d’une instance d’orchestration.

Elle prend `instanceId` (obligatoire), `showHistory` (facultatif), `showHistoryOutput` (facultatif) et `showInput` (facultatif, .NET uniquement) comme paramètres.

* **`showHistory`** : Si la valeur `true`, la réponse contient l’historique d’exécution.
* **`showHistoryOutput`** : Si la valeur `true`, l’historique d’exécution contient les sorties de l’activité.
* **`showInput`** : Si la valeur `false`, la réponse ne contient pas l’entrée de la fonction. La valeur par défaut est `true`. (.NET uniquement)

La méthode retourne un objet JSON avec les propriétés suivantes :

* **Name** : Nom de la fonction orchestrator.
* **InstanceId** : ID d’instance de l’orchestration (doit être identique à l’entrée `instanceId`).
* **CreatedTime** : Heure à laquelle la fonction orchestrator a commencé à s’exécuter.
* **LastUpdatedTime** : Heure du dernier point de contrôle d’orchestration.
* **Entrée**: Entrée de la fonction sous forme de valeur JSON. Ce champ n’est pas rempli si `showInput` a la valeur false.
* **CustomStatus** : État personnalisé de l’orchestration au format JSON.
* **Sortie**: Sortie de la fonction sous forme de valeur JSON (si cette fonction est terminée). Si la fonction d’orchestrateur a échoué, cette propriété inclut les détails de l’échec. Si la fonction d’orchestrateur s’est arrêtée, cette propriété inclut le motif de l’arrêt (le cas échéant).
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

Il est également possible d’obtenir l’état d’une instance d’orchestration directement, à l’aide de la [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` commande. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration.
* **`show-input` (facultatif)**  : Si la valeur `true`, la réponse contient l’entrée de la fonction. La valeur par défaut est `false`.
* **`show-output` (facultatif)**  : Si la valeur `true`, la réponse contient la sortie de la fonction. La valeur par défaut est `false`.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches fonctions durables à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Elle peut également être définie [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

La commande suivante récupère l’état (y compris l’entrée et sortie) d’une instance avec un ID d’instance d’orchestration de 0ab8c55a66644d68a3a8b220b12d209c. Il suppose que vous exécutiez le `func` commande à partir du répertoire racine de l’application de fonction :

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Vous pouvez utiliser la `durable get-history` commande pour récupérer l’historique d’une instance d’orchestration. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches fonctions durables à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Elle peut également être définie dans host.json, à l’aide de durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Toutes les instances de la requête

Au lieu d’une instance de requête sur votre orchestration à la fois, il peut s’avérer plus efficace de toutes les requêtes à la fois.

Vous pouvez utiliser la méthode `GetStatusAsync` (.NET) ou `getStatusAll` (JavaScript) pour interroger les états de toutes les instances de l’orchestration. Dans .NET, vous pouvez passer un `CancellationToken` au cas où vous souhaiteriez annuler l’objet. La méthode renvoie des objets avec les mêmes propriétés que la méthode `GetStatusAsync` avec des paramètres.

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

Il est également possible d’instances de la requête directement, à l’aide de la [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` commande. Les paramètres suivants sont pris en compte :

* **`top` (facultatif)**  : Cette commande prend en charge la pagination. Ce paramètre correspond au nombre d’instances récupérées par demande. La valeur par défaut est de 10.
* **`continuation-token` (facultatif)**  : Jeton servant à indiquer quelle page ou une section d’instances à récupérer. Chaque exécution de `get-instances` retourne un jeton à l’ensemble suivant d’instances.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches fonctions durables à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Elle peut également être définie [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Instances de requête avec des filtres

Que se passe-t-il si ne pas vraiment besoin d’une requête d’instance standard peut fournir toutes les informations ? Par exemple, que se passe-t-il si vous venez recherchez l’heure de création d’orchestration ou de l’état d’exécution d’orchestration ? Vous pouvez affiner votre requête en appliquant des filtres.

Utilisez le `GetStatusAsync` (.NET) ou `getStatusBy` (méthode) (JavaScript) pour obtenir une liste d’instances d’orchestration qui correspondent à un ensemble d’exemples de filtres.

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

Dans Azure Functions Core Tools, vous pouvez également utiliser le `durable get-instances` commande avec des filtres. Outre les susmentionnés `top`, `continuation-token`, `connection-string-setting`, et `task-hub-name` paramètres, vous pouvez utiliser trois paramètres de filtre (`created-after`, `created-before`, et `runtime-status`).

* **`created-after` (facultatif)**  : Récupère les instances créées après cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`created-before` (facultatif)**  : Récupère les instances créées avant cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`runtime-status` (facultatif)**  : Récupérer les instances avec un état particulier (par exemple, en cours d’exécution ou terminé). Peut fournir plusieurs états (séparés par des espaces).
* **`top` (facultatif)**  : Nombre d’instances récupérées par requête. La valeur par défaut est de 10.
* **`continuation-token` (facultatif)**  : Jeton servant à indiquer quelle page ou une section d’instances à récupérer. Chaque exécution de `get-instances` retourne un jeton à l’ensemble suivant d’instances.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches fonctions durables à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Elle peut également être définie [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

Si vous ne fournissez pas tous les filtres (`created-after`, `created-before`, ou `runtime-status`), la commande récupère simplement `top` instances, sans se soucier des temps d’état ou de la création du runtime.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Arrêter les instances

Si vous avez une instance d’orchestration qui prend trop de temps à exécuter, ou vous devez simplement pour l’arrêter avant la fin pour une raison quelconque, vous avez la possibilité pour l’arrêter.

Vous pouvez utiliser la [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) méthode de la [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe (.NET), ou le `terminate` méthode de la `DurableOrchestrationClient` classe (JavaScript). Les deux paramètres sont un `instanceId` et un `reason` chaîne, qui sont écrits dans les journaux et à l’état de l’instance. Une instance interrompue s’arrête dès qu’il atteint la prochaine `await` (.NET) ou `yield` (JavaScript) point, ou il se termine immédiatement si elle l’est déjà un `await` ou `yield`.

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
> Arrêt de l’instance ne propage pas actuellement. Orchestrations secondaires et les fonctions d’activité s’exécutent jusqu'à la fin, indépendamment de si vous avez arrêté l’instance d’orchestration qui les a appelées.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez également mettre fin à une instance d’orchestration directement, à l’aide de la [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` commande. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration à arrêter.
* **`reason` (facultatif)**  : Raison de l’arrêt.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches fonctions durables à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Elle peut également être définie [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

La commande suivante met fin à une instance d’orchestration avec un ID de 0ab8c55a66644d68a3a8b220b12d209c :

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Envoyer des événements à des instances

Dans certains scénarios, il est important pour vos fonctions d’orchestrator être en mesure d’attendre et écouter des événements externes. Cela inclut les [surveiller les fonctions](durable-functions-concepts.md#monitoring) et des fonctions qui sont en attente de [interaction humaine](durable-functions-concepts.md#human).

Envoyer des notifications d’événements à l’exécution d’instances à l’aide de la [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) méthode de la [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe (.NET) ou le `raiseEvent` méthode de la `DurableOrchestrationClient` classe ( JavaScript). Les instances pouvant gérer ces événements sont celles en attente d’un appel à[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) ou `waitForExternalEvent` (JavaScript).

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
> S’il n’existe aucune instance d’orchestration avec l’ID d’instance spécifié, ou si l’instance n’est pas en attente sur le nom de l’événement spécifié, le message d’événement est ignoré. Pour plus d’informations sur ce comportement, consultez le [problème GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez également déclencher un événement à une instance d’orchestration directement, à l’aide de la [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` commande. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration.
* **`event-name` (facultatif)**  : Nom de l’événement à déclencher. Par défaut, il s’agit de `$"Event_{RandomGUID}"`.
* **`event-data` (facultatif)**  : Données à envoyer à l’instance d’orchestration. Cela peut être le chemin d’accès à un fichier JSON, ou vous pouvez fournir les données directement sur la ligne de commande.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches fonctions durables à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Elle peut également être définie [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Attendre la fin de l’orchestration

Dans les orchestrations à long terme, vous souhaiterez attendre et obtenir les résultats d’une orchestration. Dans ces cas, il est également utile de pouvoir définir une période de délai d’attente sur l’orchestration. Si le délai d’attente est dépassé, l’état de l’orchestration doit être retournée au lieu des résultats.

Le [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe expose un [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API dans .NET. Vous pouvez utiliser cette API pour obtenir la sortie réelle à partir d’une instance d’orchestration de façon synchrone. En JavaScript, la classe `DurableOrchestrationClient` expose une API `waitForCompletionOrCreateCheckStatusResponse` dans le même but. Lorsqu’ils ne sont pas définis, les méthodes utilisent une valeur par défaut de 10 secondes pour `timeout`et 1 seconde pour `retryInterval`.  

Voici un exemple de fonction de déclencheur HTTP qui montre comment utiliser cette API :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Appeler la fonction avec la ligne suivante. Utilisez les 2 secondes pour le délai d’attente et de 0,5 seconde pour l’intervalle avant nouvelle tentative :

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Selon le temps nécessaire pour obtenir la réponse de l’instance d’orchestration, il existe deux cas de figure :

* Les instances d’orchestration terminées dans le délai imparti (dans ce cas 2 secondes), et la réponse est la sortie d’instance d’orchestration réelle remise de manière synchrone :

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

* Impossible de terminer les instances d’orchestration dans le délai imparti, et la réponse est la valeur par défaut une décrites dans [découverte de l’URL de l’API HTTP](durable-functions-http-api.md):

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
> Le format des URL webhook peut-être différer selon la version de l’hôte Azure Functions vous sont en cours d’exécution. L’exemple précédent concerne l’hôte Azure Functions 2.x.

## <a name="retrieve-http-management-webhook-urls"></a>Récupérer l’URL de webhook de gestion HTTP

Vous pouvez utiliser un système externe pour contrôler ou déclencher des événements à une orchestration. Les systèmes externes peuvent communiquer avec les fonctions durables via les URL de webhook qui font partie de la réponse par défaut décrite dans [découverte de l’URL de l’API HTTP](durable-functions-http-api.md). Toutefois, les URL de webhook également accessibles par programme dans le client d’orchestration ou dans une fonction d’activité. Cela à l’aide de la [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) méthode de la [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe (.NET), ou le `createHttpManagementPayload` méthode de la `DurableOrchestrationClient` classe (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) et `createHttpManagementPayload` ont un seul caractère :

* **instanceId** : ID unique de l’instance.

Les méthodes retournent une instance de [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) ou un objet (JavaScript), avec les propriétés de chaîne suivantes :

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

## <a name="rewind-instances-preview"></a>Rewind instances (version préliminaire)

Si vous avez un échec de l’orchestration pour une raison inattendue, vous pouvez *rewind* l’instance à un état sain précédemment à l’aide d’une API intégrée à cet effet.

> [!NOTE]
> Cette API n’est pas destinée à se substituer à des stratégies appropriées de nouvelles tentatives et de gestion des erreurs. Son utilisation est plutôt uniquement réservée dans les cas où les instances d’orchestration échouent pour des raisons inattendues. Pour plus d’informations sur les stratégies de nouvelle tentative et de gestion des erreurs, consultez le [la gestion des erreurs](durable-functions-error-handling.md) rubrique.

Utilisez le [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) ou `rewindAsync` API (JavaScript) pour placer l’orchestration de nouveau la *en cours d’exécution* état. Réexécuter les échecs d’exécution activité ou suborchestration ayant provoqué l’échec de l’orchestration.

Par exemple, supposons que vous avez un flux de travail impliquant une série de [approbations humaines](durable-functions-concepts.md#human). Supposons qu’il existe une série de fonctions d’activité qui informent une personne qui leur approbation est nécessaire et attendre la réponse en temps réel. Après tout, des activités d’approbation ont reçu des réponses ou a expiré, supposons qu’une autre activité échoue en raison d’une mauvaise configuration des applications, comme une chaîne de connexion de base de données non valide. Il en résulte un échec de l’orchestration, survenu en profondeur dans le workflow. Avec le `RewindAsync` (.NET) ou `rewindAsync` (JavaScript), API, une application administrateur permettre corriger l’erreur de configuration et rembobiner l’orchestration ayant échouée à l’état immédiatement avant la défaillance. Aucune des étapes à l’interaction homme doit être Réapprouvé et l’orchestration peut désormais s’effectuer correctement.

> [!NOTE]
> Le *rewind* fonctionnalité ne prend pas en charge les instances d’orchestration rembobinage qui utilisent des minuteurs durables.

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

Vous pouvez également rembobiner une instance d’orchestration directement à l’aide du [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` commande. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration.
* **`reason` (facultatif)**  : Motif de rembobinage de l’instance d’orchestration.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches fonctions durables à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Elle peut également être définie [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Purger l’historique d’instance

Pour supprimer toutes les données associées à une orchestration, vous pouvez vider l’historique de l’instance. Par exemple, vous souhaiterez peut-être se débarrasser des lignes de Table Azure et les objets BLOB de message volumineux, s’ils existent. Pour ce faire, utilisez le [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API.

> [!NOTE]
> L’API `PurgeInstanceHistoryAsync` est actuellement disponible uniquement pour C#.

 La méthode a deux surcharges. La première condition purge l’historique par l’ID de l’instance d’orchestration :

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

Le deuxième exemple montre une fonction déclenchée par un minuteur qui purge l’historique de toutes les instances d’orchestration terminées après l’intervalle de temps spécifié. Dans ce cas, il supprime les données pour toutes les instances terminées de 30 jours ou plus il y a. Il est planifié pour s’exécuter une fois par jour, à 12 : 00 :

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
> Pour le processus de la fonction déclenchée par l’heure réussisse, l’état d’exécution doit être **terminé**, **Terminated**, ou **échec**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Vous pouvez vider l’historique d’une instance d’orchestration à l’aide de la [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` commande. Similaire à la seconde C# exemple dans la section précédente, il le supprime définitivement l’historique de toutes les instances d’orchestration créées pendant un intervalle de temps spécifié. Vous pouvez filtrer davantage les instances purgés par état d’exécution. La commande a plusieurs paramètres :

* **`created-after` (facultatif)**  : Purge l’historique des instances créées après cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`created-before` (facultatif)**  : Purge l’historique des instances créées avant cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`runtime-status` (facultatif)**  : Vider l’historique des instances avec un état particulier (par exemple, en cours d’exécution ou terminé). Peut fournir plusieurs états (séparés par des espaces).
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches fonctions durables à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Elle peut également être définie [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

La commande suivante supprime l’historique de toutes les instances ayant échoués créées avant le 14 novembre 2018 à 7 h 35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Supprimer un hub de tâches

À l’aide de la [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` commande, vous pouvez supprimer tous les artefacts de stockage associés à un hub de tâches spécifique. Cela inclut les objets blob, les files d’attente et les tables de stockage Azure. La commande a deux paramètres :

* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. Par défaut, il s’agit de `AzureWebJobsStorage`.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches fonctions durables à utiliser. Par défaut, il s’agit de `DurableFunctionsHub`. Elle peut également être définie [host.json](durable-functions-bindings.md#host-json), à l’aide de durableTask:HubName.

La commande suivante supprime toutes les données de stockage Azure associées à le `UserTest` hub de tâches.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment utiliser les API HTTP pour la gestion des instances](durable-functions-http-api.md)
