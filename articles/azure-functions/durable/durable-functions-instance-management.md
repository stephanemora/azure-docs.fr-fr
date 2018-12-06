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
ms.date: 11/27/2018
ms.author: azfuncdf
ms.openlocfilehash: 9b85ce6bdb7f72c5e4f1cf0d47cc324f5f75ec20
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637264"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Gérer des instances dans Fonctions durables (Azure Functions)

Les instances d’orchestration de [Fonctions durables](durable-functions-overview.md) peuvent être des événements de notification démarrés, arrêtés, interrogés et envoyés. La gestion de toutes les instances est effectuée à l’aide de la [liaison du client d’orchestration](durable-functions-bindings.md). Cet article explique en détail chaque opération de gestion d’instance.

## <a name="starting-instances"></a>Démarrage des instances

La méthode [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) du [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) démarre la nouvelle instance d’une fonction d’orchestrateur. Les instances de cette classe peuvent être obtenues à l’aide de la liaison `orchestrationClient`. En interne, cette méthode empile un message dans la file d’attente de contrôle, qui déclenche ensuite une fonction avec le nom spécifié utilisant la liaison de déclenchement `orchestrationTrigger`. 

La tâche est terminée lorsque le processus d’orchestration a démarré. Le processus d’orchestration doit démarrer dans les 30 secondes. Si cela dure plus longtemps, une `TimeoutException` est levée. 

Les paramètres de [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) sont les suivants :

* **Nom** : nom de la fonction de l’orchestrateur à planifier.
* **Entrée** : toutes les données JSON sérialisables devant être transmises comme entrée à la fonction de l’orchestrateur.
* **InstanceId** : (facultatif) ID unique de l’instance. S’il n’est pas spécifié, un ID d’instance aléatoire est généré.

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

Pour les langages autres que .NET, la liaison de sortie de la fonction peut être utilisée pour démarrer également de nouvelles instances. Dans ce cas, tout objet sérialisable JSON ayant les trois paramètres ci-dessus comme champs peut être utilisé. Considérez par exemple la fonction JavaScript suivante :

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

Le code ci-dessus suppose que, dans le fichier function.json, vous avez défini une liaison de sortie nommée `starter` avec le type `orchestrationClient`. Si la liaison n’est pas définie, l’instance de fonction durable n’est pas créée.

Pour appeler la fonction durable, le fichier function.json doit être modifié de sorte à avoir une liaison au client d’orchestration, comme décrit ci-dessous

```js
{
    "bindings": [{
        "name":"starter",
        "type":"orchestrationClient",
        "direction":"out"
    }]
}
```

> [!NOTE]
> Utilisez un identificateur aléatoire pour l’ID d’instance. Cela permet de garantir une distribution égale de la charge lors de la mise à l’échelle des fonctions de l’orchestrateur sur plusieurs machines virtuelles. Le moment qui convient pour l’utilisation des ID d’instance non aléatoires correspond au moment où l’ID provient d’une source externe ou lors de l’implémentation du modèle [orchestrateur singleton](durable-functions-singletons.md).

### <a name="using-core-tools"></a>Utilisation de Core Tools

Vous pouvez aussi démarrer une instance directement via la commande [Azure Functions Core Tools](../functions-run-local.md) `durable start-new`. Les paramètres suivants sont pris en compte :

* **`function-name` (obligatoire)**  : Nom de la fonction à démarrer
* **`input` (facultatif)**  : Entrée de la fonction, en ligne ou via un fichier JSON. Pour les fichiers, préfixez le chemin du fichier avec `@`, par exemple, `@path/to/file.json`.
* **`id` (facultatif)**  : ID de l’instance d’orchestration. S’il n’est pas fourni, un GUID aléatoire est généré.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches durable à utiliser. La valeur par défaut est DurableFunctionsHub. Elle peut également être définie dans [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName. 

> [!NOTE]
> Les commandes Core Tools supposent qu’elles sont exécutées à partir du répertoire racine d’une application de fonction. Si `connection-string-setting` et `task-hub-name` sont explicitement fournis, les commandes peuvent être exécutées à partir de n’importe quel répertoire. Alors que ces commandes peuvent être exécutées sans l’exécution d’un hôte d’application de fonction, certains effets peuvent être observés seulement quand l’hôte est exécuté. Par exemple, la commande `start-new` met en file d’attente un message de démarrage dans le hub de tâches cible, mais l’orchestration ne peut pas réellement s’exécuter à moins d’avoir un processus hôte d’application de fonction en cours d’exécution pouvant traiter le message.

La commande suivante démarre la fonction nommée HelloWorld et lui passe le contenu du fichier « counter-data.json » :
```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>Interrogation des instances

La méthode [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) interroge l’état d’une instance d’orchestration.

Elle prend `instanceId` (obligatoire), `showHistory` (facultatif), `showHistoryOutput` (facultatif) et `showInput` (facultatif) comme paramètres. 
* **`showHistory`**  : Si défini sur `true`, la réponse contient l’historique d’exécution. 
* **`showHistoryOutput`**  : Si défini sur `true`, l’historique d’exécution contient les sorties de l’activité. 
* **`showInput`**  : si défini sur `false`, la réponse ne contient pas l’entrée de la fonction. La valeur par défaut est `true`.

La méthode retourne un objet JSON avec les propriétés suivantes :

* **Nom** : nom de la fonction de l’orchestrateur.
* **InstanceId** : ID d’instance de l’orchestration (doit être identique à l’entrée `instanceId`).
* **CreatedTime** : Heure à laquelle la fonction de l’orchestrateur a commencé à s’exécuter.
* **LastUpdatedTime** : Heure du dernier point de contrôle d’orchestration.
* **Input** : entrée de la fonction sous forme de valeur JSON. Ce champ n’est pas rempli si `showInput` est false.
* **CustomStatus** : état personnalisé de l’orchestration au format JSON. 
* **Output** : sortie de la fonction sous forme de valeur JSON (si cette fonction est terminée). En cas d’échec de la fonction, cette propriété inclut les détails de l’échec. En cas d’interruption de la fonction de l’orchestrateur, cette propriété indique pour quel motif (le cas échéant).
* **RuntimeStatus** : une des valeurs suivantes :
    * **En attente** : l’instance a été planifiée mais n’est pas encore en cours d’exécution.
    * **Running** : l’instance a commencé à s’exécuter.
    * **Completed** : l’instance s’est terminée normalement.
    * **ContinuedAsNew** : l’instance a redémarré automatiquement d’elle-même avec un nouvel historique. Il s’agit d’un état temporaire.
    * **Failed** : échec de l’instance avec une erreur.
    * **Arrêté** : l’instance a été brusquement arrêtée.
* **L’historique** : l’historique d’exécution de l’orchestration. Ce champ est renseigné uniquement si `showHistory` est défini sur `true`.
    
Cette méthode renvoie `null` si l’instance n’existe pas ou n’est pas encore exécutée.

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

### <a name="using-core-tools"></a>Utilisation de Core Tools

Vous pouvez aussi obtenir l’état d’une instance d’orchestration directement via la commande [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status`. Les paramètres suivants sont pris en compte : 

* **`id` (obligatoire)**  : ID de l’instance d’orchestration
* **`show-input` (facultatif)**  : si défini sur `true`, la réponse contient l’entrée de la fonction. La valeur par défaut est `false`.
* **`show-output` (facultatif)**  : si défini sur `true`, la réponse contient la sortie de la fonction. La valeur par défaut est `false`.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches durable à utiliser. La valeur par défaut est DurableFunctionsHub. Elle peut également être définie dans [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

La commande suivante récupère l’état (y compris l’entrée et la sortie) d’une instance avec l’ID d’instance d’orchestration 0ab8c55a66644d68a3a8b220b12d209c. Cela suppose que la commande `func` est exécutée à partir du répertoire racine de l’application de fonction :
```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

La commande `durable get-history` peut être utilisée pour récupérer l’historique d’une instance d’orchestration. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches durable à utiliser. La valeur par défaut est DurableFunctionsHub. Elle peut aussi être définie dans host.json via durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>Interrogation de toutes les instances

Vous pouvez utiliser la méthode `GetStatusAsync` pour interroger les états de toutes les instances de l’orchestration. Elle ne prend aucun paramètre. Si vous souhaitez l’annuler, vous pouvez transmettre un objet `CancellationToken`. La méthode renvoie des objets avec les mêmes propriétés que la méthode `GetStatusAsync` avec des paramètres, sauf qu’elle ne renvoie pas d’historique. 

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="using-core-tools"></a>Utilisation de Core Tools

Vous pouvez aussi interroger des instances directement via la commande [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances`. Les paramètres suivants sont pris en compte :

* **`top` (facultatif)**  : Cette commande prend en charge la pagination. Ce paramètre correspond au nombre d’instances récupérées par demande. La valeur par défaut est de 10.
* **`continuation-token` (facultatif)**  : Jeton qui indique la page/section des instances à récupérer. Chaque exécution de `get-instances` retourne un jeton à l’ensemble suivant d’instances.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches durable à utiliser. La valeur par défaut est DurableFunctionsHub. Elle peut également être définie dans [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>Interrogation d’instances avec des filtres

Vous pouvez également utiliser la méthode `GetStatusAsync` pour obtenir une liste des instances d’orchestration qui correspondent à un ensemble de filtres prédéfinis. Les options de filtre possibles incluent l’heure de création de l’orchestration et l’état de l’exécution de l’orchestration.

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

### <a name="using-the-functions-core-tools"></a>Utilisation de Functions Core Tools

La commande `durable get-instances` peut également être utilisée avec des filtres. Outre les paramètres `top`, `continuation-token`, `connection-string-setting` et `task-hub-name` mentionnés ci-dessus, trois paramètres de filtre (`created-after`, `created-before` et `runtime-status`) peuvent être utilisés. 

* **`created-after` (facultatif)**  : Récupère les instances créées après cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`created-before` (facultatif)**  : Récupère les instances créées avant cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`runtime-status` (facultatif)**  : Récupère les instances dont l’état correspond à « en cours d’exécution », « effectué », etc. Peut fournir plusieurs états (séparés par des espaces).
* **`top` (facultatif)**  : Nombre d’instances récupérées par demande. La valeur par défaut est de 10.
* **`continuation-token` (facultatif)**  : Jeton qui indique la page/section des instances à récupérer. Chaque exécution de `get-instances` retourne un jeton à l’ensemble suivant d’instances.
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches durable à utiliser. La valeur par défaut est DurableFunctionsHub. Elle peut également être définie dans [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

Si aucun filtre (`created-after`, `created-before` ou `runtime-status`) n’est fourni, les instances `top` sont récupérées sans considération de l’état d’exécution ni de l’heure de création.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>Arrêt des instances

Vous pouvez arrêter une instance d’orchestration en cours d’exécution à l’aide de la méthode [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Les deux paramètres sont une chaîne `instanceId` et `reason`, qui est écrite dans les journaux et dans l’état de l’instance. Une instance interrompue cesse de s’exécuter dès qu’elle atteint le point `await` suivant, ou s’arrête immédiatement si elle se trouve déjà sur un `await`. 

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

> [!NOTE]
> Actuellement, l’arrêt de l’instance ne se propage pas. Les fonctions et sous-orchestrations de l’activité s’exécutent entièrement, même si l’instance d’orchestration qui les a appelées a été arrêtée.

### <a name="using-core-tools"></a>Utilisation de Core Tools

Vous pouvez aussi arrêter une instance d’orchestration directement via la commande [Core Tools](../functions-run-local.md) `durable terminate`. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration à arrêter
* **`reason` (facultatif)**  : Raison de l’arrêt
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches durable à utiliser. La valeur par défaut est DurableFunctionsHub. Elle peut également être définie dans [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

La commande suivante arrête une instance d’orchestration avec l’ID 0ab8c55a66644d68a3a8b220b12d209c :
```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>Envoi d’événements à des instances

Des notifications d’événements peuvent être envoyées aux instances en cours d’exécution à l’aide de la méthode [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Les instances pouvant gérer ces événements sont celles en attente d’un appel à [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

Les paramètres de [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) sont les suivants :

* **InstanceId** : ID unique de l’instance.
* **EventName** : nom de l’événement à envoyer.
* **EventData** : charge utile JSON sérialisable à envoyer à l’instance.

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

> [!WARNING]
> S’il n’existe aucune instance d’orchestration avec l’*ID d’instance* spécifié ou si l’instance n’attend pas le *nom d’événement* spécifié, le message d’événement est ignoré. Pour plus d’informations sur ce comportement, consultez le [problème GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="using-core-tools"></a>Utilisation de Core Tools

Vous pouvez aussi déclencher un événement sur une instance d’orchestration directement via la commande [Core Tools](../functions-run-local.md) `durable raise-event`. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration
* **`event-name` (facultatif)**  : Nom de l’événement à déclencher. La valeur par défaut est `$"Event_{RandomGUID}"`
* **`event-data` (facultatif)**  : Données à envoyer à l’instance d’orchestration. Il peut s’agir d’un chemin de fichier JSON ou les données peuvent être fournies directement sur la ligne de commande
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches durable à utiliser. La valeur par défaut est DurableFunctionsHub. Elle peut également être définie dans [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```
```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Attendre la fin de l’orchestration

La classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) expose une API [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) qui peut être utilisée pour obtenir de façon synchrone le résultat réel à partir d’une instance d’orchestration. La méthode utilise la valeur par défaut de 10 secondes pour `timeout` et d’1 seconde pour `retryInterval` quand elles ne sont pas définies.  

Voici un exemple de fonction de déclencheur HTTP qui montre comment utiliser cette API :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

La fonction peut être appelée avec la ligne suivante à l’aide du délai d’attente de 2 secondes et de l’intervalle de 0,5 seconde avant nouvelle tentative :

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Selon le temps nécessaire pour obtenir la réponse de l’instance d’orchestration, il existe deux cas de figure :

* Les instances d’orchestration sont terminées dans le délai imparti (dans ce cas 2 secondes), la réponse est la sortie d’instance d’orchestration réelle remise de manière synchrone :

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

* Les instances d’orchestration ne peuvent pas être terminées dans le délai imparti (dans ce cas 2 secondes), la réponse est la réponse par défaut décrite dans **Découverte de l’URL de l’API HTTP** :

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
> Le format des URL « webhook » peut différer selon la version de l’hôte Azure Functions que vous exécutez. L’exemple précédent utilise l’hôte Azure Functions 2.0.

## <a name="retrieving-http-management-webhook-urls"></a>Récupération des URL Webhook de gestion HTTP

Les systèmes externes peuvent communiquer avec Fonctions durables via les URL Webhook qui font partie de la réponse par défaut décrite dans [Découverte de l’URL de l’API HTTP](durable-functions-http-api.md). Toutefois, les URL Webhook sont également accessibles par programme dans le client d’orchestration ou dans une fonction d’activité via la méthode [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) a un seul paramètre :

* **instanceId** : ID unique de l’instance.

La méthode renvoie une instance de [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) avec les propriétés de chaîne suivantes :

* **Id** : ID d’instance de l’orchestration (doit être identique à l’entrée `InstanceId`).
* **StatusQueryGetUri** : URL de l’état de l’instance de l’orchestration.
* **SendEventPostUri** : URL « déclencher un événement » de l’instance de l’orchestration.
* **TerminatePostUri** : URL « d’arrêt » de l’instance de l’orchestration.
* **RewindPostUri** : URL de « rembobinage » de l’instance d’orchestration.

Les fonctions d’activité peuvent envoyer une instance de [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) à des systèmes externes pour surveiller ou déclencher des événements sur une orchestration :

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

## <a name="rewinding-instances-preview"></a>Rembobinage d’instances (préversion)

Une instance d’orchestration ayant échoué peut être *rembobinée* jusqu’à un état sain précédent au moyen de l’API [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_). Le principe consiste à remettre l’orchestration dans l’état *En cours d’exécution* et à réexécuter les échecs d’exécution d’activité et/ou de sous-orchestration ayant provoqué l’échec de l’orchestration.

> [!NOTE]
> Cette API n’est pas destinée à se substituer à des stratégies appropriées de nouvelles tentatives et de gestion des erreurs. Son utilisation est plutôt uniquement réservée dans les cas où les instances d’orchestration échouent pour des raisons inattendues. Pour plus d’informations sur les stratégies de nouvelles tentatives et de gestion des erreurs, consultez la rubrique [Gestion des erreurs](durable-functions-error-handling.md).

Un exemple de cas d’utilisation pour le *rewind* (rembobinage) s’illustre dans un workflow impliquant une série d’[approbations humaines](durable-functions-overview.md#pattern-5-human-interaction). Supposons une série de fonctions d’activité qui informent une personne que son approbation est nécessaire, et qui attendent la réponse en temps réel. Une fois que toutes les activités d’approbation ont reçu des réponses ou ont expiré, une autre activité échoue en raison d’un problème de configuration d’application (par exemple, une chaîne de connexion de base de données non valide). Il en résulte un échec de l’orchestration, survenu en profondeur dans le workflow. Avec l’API `RewindAsync`, un administrateur d’application peut corriger l’erreur de configuration et *rewind* (rembobiner) l’orchestration ayant échoué jusqu’à l’état situé immédiatement avant l’échec. Aucune des étapes nécessitant une interaction humaine n’a besoin d’être réapprouvée et l’orchestration peut désormais s’effectuer correctement.

> [!NOTE]
> La fonctionnalité *rewind* ne prend pas en charge les instances d’orchestration de rembobinage qui utilisent des minuteurs durables.

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

### <a name="using-core-tools"></a>Utilisation de Core Tools

Vous pouvez aussi rembobiner une instance d’orchestration directement via la commande [Core Tools](../functions-run-local.md) `durable rewind`. Les paramètres suivants sont pris en compte :

* **`id` (obligatoire)**  : ID de l’instance d’orchestration
* **`reason` (facultatif)**  : Motif de rembobinage de l’instance d’orchestration
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches durable à utiliser. La valeur par défaut est DurableFunctionsHub. Elle peut également être définie dans [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Purger l’historique d’instance

Vous pouvez purger l’historique d’orchestration à l’aide de [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_). La fonctionnalité supprime toutes les données associées à une orchestration : les lignes de Table Azure et les grands objets blob de message s’ils existent. La méthode a deux surcharges. La première purge l’historique par ID d’instance d’orchestration :

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
    return client.InnerClient.PurgeInstanceHistoryAsync( 
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus> 
                    { 
                        OrchestrationStatus.Completed
                    }); 
}
```

> [!NOTE]
> La surcharge *PurgeInstanceHistory* acceptant l’intervalle de temps comme paramètre traite uniquement les instances d’orchestration dans un état d’exécution Effectué, Arrêté ou Échec.

### <a name="using-core-tools"></a>Utilisation de Core Tools

Vous pouvez purger l’historique d’une instance d’orchestration à l’aide de la commande [Core Tools](../functions-run-local.md) `durable purge-history`. Tout comme dans le deuxième exemple C# ci-dessus, elle supprime définitivement l’historique de toutes les instances d’orchestration créées dans un intervalle de temps spécifié. Les instances purgées peuvent être filtrées aussi par état d’exécution. La commande a plusieurs paramètres :

* **`created-after` (facultatif)**  : Purge l’historique des instances créées après cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`created-before` (facultatif)**  : Purge l’historique des instances créées avant cette date/heure (UTC). Les dates et heures au format ISO 8601 sont acceptées.
* **`runtime-status` (facultatif)**  : Purge les instances dont l’état correspond à « en cours d’exécution », « effectué », etc. Peut fournir plusieurs états (séparés par des espaces).
* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches durable à utiliser. La valeur par défaut est DurableFunctionsHub. Elle peut également être définie dans [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

La commande suivante supprime l’historique de toutes les instances ayant échoué créées avant le 14 novembre 2018 à 19:35 (UTC).
```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>Suppression d’un hub de tâches
À l’aide de la commande [Core Tools](../functions-run-local.md) `durable delete-task-hub`, vous pouvez supprimer tous les artefacts de stockage associés à un hub de tâches spécifique. Cela inclut les objets blob, les files d’attente et les tables de stockage Azure. La commande a deux paramètres : 

* **`connection-string-setting` (facultatif)**  : Nom du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. La valeur par défaut est AzureWebJobsStorage.
* **`task-hub-name` (facultatif)**  : Nom du hub de tâches durable à utiliser. La valeur par défaut est DurableFunctionsHub. Elle peut également être définie dans [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

La commande suivante supprime toutes les données de stockage Azure associées au hub de tâches « UserTest ».
```bash
func durable delete-task-hub --task-hub-name UserTest
```


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment utiliser les API HTTP pour la gestion des instances](durable-functions-http-api.md)
