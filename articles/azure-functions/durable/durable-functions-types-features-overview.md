---
title: Types de fonctions et fonctionnalités dans l’extension fonctions durables d’Azure Functions
description: En savoir plus sur les types de fonctions et les rôles qui prennent en charge la communication de la fonction de la fonction dans une orchestration de fonctions durables dans Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 03/19/2019
ms.author: v-junlch
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731111"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Les types de fonctions durables et aux fonctionnalités (Azure Functions)

Fonctions durables est une extension de [Azure Functions](../functions-overview.md). Vous pouvez utiliser les fonctions durables pour l’orchestration avec état d’exécution de la fonction. Une fonction durable est une solution qui se compose de différentes fonctions Azure. Fonctions peuvent jouer différents rôles dans une orchestration de fonction durable. 

Cet article vous donne une vue d’ensemble des types de fonctions que vous pouvez utiliser dans une orchestration de fonctions durables. L’article inclut quelques modèles courants que vous pouvez utiliser pour connecter les fonctions. Découvrez comment les fonctions durables peut vous aider à résoudre vos défis de développement d’application.

![Une image qui illustre les types de fonctions durables][1]  

## <a name="types-of-durable-functions"></a>Types de fonctions durables

Vous pouvez utiliser trois types de fonction durable dans Azure Functions : activité, orchestrator et le client.

### <a name="activity-functions"></a>Fonctions d’activité

Fonctions d’activité sont l’unité élémentaire de travail dans une orchestration de fonction durable. Fonctions d’activité sont les fonctions et les tâches qui sont orchestrées dans le processus. Par exemple, vous pouvez créer une fonction durable pour traiter une commande. Les tâches impliquent la vérification de l’inventaire, la facturation du client et création d’une expédition. Chaque tâche serait une fonction d’activité. 

Le type de travail que vous pouvez faire dans les fonctions d’activité ne sont pas limitées. Vous pouvez écrire une fonction d’activité dans les [langage prenant en charge les fonctions durables](durable-functions-overview.md#language-support). L’infrastructure des tâches durables garantit que chaque fonction d’activité appelée est exécutée au moins une fois au cours d’une orchestration.

Utilisez un [déclencheur d’activité](durable-functions-bindings.md#activity-triggers) pour déclencher une fonction d’activité. Les fonctions .NET reçoivent un [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) en tant que paramètre. Vous pouvez également lier le déclencheur à UN autre objet à passer dans les entrées de la fonction. Dans JavaScript, vous pouvez accéder à une entrée via la `<activity trigger binding name>` propriété sur le [ `context.bindings` objet](../functions-reference-node.md#bindings).

Votre fonction d’activité peut également retourner des valeurs pour l’orchestrateur. Si vous envoyez ou retournez un grand nombre de valeurs à partir d’une fonction d’activité, vous pouvez utiliser [tuples ou tableaux](durable-functions-bindings.md#passing-multiple-parameters). Vous pouvez déclencher une fonction d’activité uniquement à partir d’une instance d’orchestration. Bien qu’une fonction d’activité et une autre fonction (par exemple, une fonction déclenchée via HTTP) susceptibles de partager du code, chaque fonction peut avoir qu’un seul déclencheur.

Pour plus d’informations et pour obtenir des exemples, consultez [fonctions d’activité](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Fonctions d’orchestrateur

Les fonctions d’orchestrateur décrivent la façon dont les actions sont exécutées et l’ordre dans lequel les actions sont exécutées. L’orchestration dans le code décrivent les fonctions d’orchestrateur (C# ou JavaScript) comme indiqué dans [modèles de fonctions durables et concepts techniques](durable-functions-concepts.md). Une orchestration peut avoir de nombreux différents types d’actions, y compris [fonctions d’activité](#activity-functions), [sous-orchestrations](#sub-orchestrations), [en attente des événements externes](#external-events)et [minuteries](#durable-timers). 

Une fonction d’orchestrateur doit être déclenchée par un [déclencheur d’orchestration](durable-functions-bindings.md#orchestration-triggers).

Un orchestrateur est démarré par un [client d’orchestrator](#client-functions). Vous pouvez déclencher l’orchestrateur à partir de n’importe quelle source (HTTP, file d’attente, flux d’événements). Chaque instance d’une orchestration possède un identificateur d’instance. L’identificateur d’instance peut être généré automatiquement (recommandé) ou généré par l’utilisateur. Vous pouvez utiliser l’identificateur d’instance à [gérer des instances](durable-functions-instance-management.md) de l’orchestration.

Pour plus d’informations et pour obtenir des exemples, consultez [déclencheurs d’Orchestration](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Fonctions clientes

Les fonctions de clients sont les fonctions déclenchées qui créent des instances d’une orchestration. Les fonctions de clients sont le point d’entrée pour la création d’une instance d’une orchestration de fonctions durables. Vous pouvez déclencher une fonction de client à partir de n’importe quelle source (HTTP, file d’attente, flux d’événements). Vous pouvez écrire une fonction cliente dans n’importe quel langage qui prend en charge de l’application. 

Les fonctions de clients ont également un [client d’orchestration](durable-functions-bindings.md#orchestration-client) liaison. Une fonction de client peut utiliser la liaison pour créer et gérer des orchestrations durables du client d’orchestration. 

L’exemple de base d’une fonction de client est une fonction déclenchée par HTTP qui démarre une fonction d’orchestrateur, puis retourne une réponse d’état de vérification. Pour obtenir un exemple, consultez [découverte de l’URL de l’API HTTP](durable-functions-http-api.md#http-api-url-discovery).

Pour plus d’informations et pour obtenir des exemples, consultez [client d’Orchestration](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Fonctionnalités et modèles

Les sections suivantes décrivent les fonctionnalités et les modèles de types de fonctions durables.

### <a name="sub-orchestrations"></a>Orchestrations secondaires

Les fonctions d’orchestrateur peuvent appeler des fonctions d’activité, mais elles peuvent également appeler d’autres fonctions d’orchestrateur. Par exemple, vous pouvez créer une orchestration plus grande à partir d’une bibliothèque de fonctions d’orchestrateur. Ou bien, vous pouvez exécuter plusieurs instances d’une fonction d’orchestrateur en parallèle.

Pour plus d’informations et pour obtenir des exemples, consultez [sous-orchestrations](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Minuteurs durables

[Fonctions durables](durable-functions-overview.md) fournit *minuteurs durables* que vous pouvez utiliser dans les fonctions d’orchestrateur pour implémenter des retards ou pour définir des délais d’expiration sur les actions asynchrones. Utiliser des minuteurs durables dans les fonctions d’orchestrateur à la place de `Thread.Sleep` et `Task.Delay` (C#) ou `setTimeout()` et `setInterval()` (JavaScript).

Pour plus d’informations et pour obtenir des exemples, consultez [minuteurs durables](durable-functions-timers.md).

### <a name="external-events"></a>Événements externes

Les fonctions d’orchestrateur peuvent attendre des événements externes pour mettre à jour une instance d’orchestration. Cette fonctionnalité de fonctions durables est souvent utile pour la gestion des autres rappels externes ou une interaction humaine.

Pour plus d’informations et pour obtenir des exemples, consultez [événements externes](durable-functions-external-events.md).

### <a name="error-handling"></a>Gestion des erreurs

Utilisez le code pour implémenter des orchestrations de fonctions durables. Vous pouvez utiliser les fonctionnalités de gestion des erreurs du langage de programmation. Modèles comme `try` / `catch` fonctionnent dans votre orchestration. 

Fonctions durables, livrée avec des stratégies de nouvelle tentative intégrées. Une action peut retarder et recommencez les activités automatiquement lorsqu’une exception se produit. Vous pouvez utiliser les nouvelles tentatives pour gérer les exceptions temporaires sans abandon de l’orchestration.

Pour plus d’informations et pour obtenir des exemples, consultez [la gestion des erreurs](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Communication entre plusieurs applications de fonction

Bien qu’une orchestration durable s’exécute dans le contexte d’une application de fonction unique, vous pouvez utiliser des modèles pour coordonner des orchestrations entre plusieurs applications de fonction. Les communications entre applications peuvent se produire sur HTTP, mais à l’aide de l’infrastructure durable pour chaque activité signifie que vous pouvez tout en conservant un processus durable entre deux applications.

Les exemples suivants montrent l’orchestration d’application de fonction croisée dans C# et JavaScript. Dans chaque exemple, une seule activité démarre l’orchestration externe. Une autre activité récupère et retourne l’état. L’orchestrateur attend que l’état soit `Complete` avant de continuer.

Voici quelques exemples d’orchestration de l’application de fonction croisée :

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.chinacloudsites.cn/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.chinacloudsites.cn/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, créez votre première fonction durable dans [ C# ](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [En savoir plus sur les fonctions durables](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png

<!-- Update_Description: wording update -->