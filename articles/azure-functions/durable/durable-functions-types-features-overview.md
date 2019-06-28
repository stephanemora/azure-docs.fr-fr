---
title: Types de fonctions et fonctionnalités de l’extension Durable Functions d’Azure Functions
description: Découvrez les types de fonctions et les rôles qui permettent la communication de fonction à fonction dans le cadre d’une orchestration Durable Functions dans Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731111"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Types et fonctionnalités Durable Functions (Azure Functions)

Durable Functions est une extension d’[Azure Functions](../functions-overview.md). Vous pouvez utiliser Durable Functions pour effectuer une orchestration avec état d’une exécution de fonction. Une fonction durable est une solution qui est composée de différentes fonctions Azure. Les fonctions peuvent jouer différents rôles dans le cadre d’une orchestration de fonction durable. 

Cet article vous présente les différents types de fonctions que vous pouvez utiliser dans une orchestration Durable Functions. L’article comprend quelques modèles courants que vous pouvez utiliser pour connecter les fonctions. Découvrez comment Durable Functions peut vous aider à résoudre les difficultés que vous rencontrez lors du développement d’applications.

![Image qui illustre les types de fonctions durables][1]  

## <a name="types-of-durable-functions"></a>Types de fonctions durables

Vous pouvez utiliser trois types de fonctions durables dans Azure Functions : les fonctions d’activité, les fonctions d’orchestrateur et les fonctions clientes.

### <a name="activity-functions"></a>Fonctions d’activité

Les fonctions d’activité sont à la base de l’orchestration de fonction durable. Les fonctions d’activité correspondent aux fonctions et aux tâches qui sont orchestrées dans le processus. Par exemple, vous pouvez créer une fonction durable pour traiter une commande. Ces tâches impliquent la vérification de l’inventaire, la facturation du client et l’organisation de l’expédition. Chacune de ces tâches correspond à une fonction d’activité. 

Vous pouvez effectuer tous types de travaux dans les fonctions d’activité. Vous pouvez écrire une fonction d’activité dans n’importe quel [langage pris en charge par Durable Functions](durable-functions-overview.md#language-support). L’infrastructure des tâches durables garantit que chaque fonction d’activité appelée est exécutée au moins une fois au cours d’une orchestration.

Utilisez un [déclencheur d’activité](durable-functions-bindings.md#activity-triggers) pour déclencher une fonction d’activité. Les fonctions .NET reçoivent un [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) comme paramètre. Vous pouvez également lier le déclencheur à UN autre objet à passer dans les entrées de la fonction. En JavaScript, vous pouvez accéder à l’entrée via la propriété `<activity trigger binding name>` de l’[objet `context.bindings`](../functions-reference-node.md#bindings).

Votre fonction d’activité peut également retourner des valeurs à l’orchestrateur. Si vous envoyez ou retournez un grand nombre de valeurs à partir d’une fonction d’activité, vous pouvez utiliser [des tuples ou des tableaux](durable-functions-bindings.md#passing-multiple-parameters). Vous ne pouvez déclencher une fonction d’activité qu’à partir d’une instance de l’orchestration. Même si une fonction d’activité peut partager du code avec une autre fonction (par exemple, une fonction déclenchée via HTTP), chaque fonction ne peut avoir qu’un seul déclencheur.

Pour plus d’informations et pour obtenir des exemples, consultez [Fonctions d’activité](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Fonctions d’orchestrateur

Les fonctions d’orchestrateur décrivent la façon dont les actions sont exécutées et l’ordre dans lequel elles sont exécutées. Les fonctions d’orchestrateur décrivent l’orchestration dans le code (C# ou JavaScript), comme indiqué dans [Concepts techniques et modèles Durable Functions](durable-functions-concepts.md). Une orchestration peut avoir de nombreux types d’actions différents, comme des [fonctions d’activité](#activity-functions), des [sous-orchestrations](#sub-orchestrations), l’[attente d’événements externes](#external-events) et des [ minuteurs](#durable-timers). 

Une fonction d’orchestrateur doit être déclenchée par un [déclencheur d’orchestration](durable-functions-bindings.md#orchestration-triggers).

L’orchestrateur est démarré par un [client d’orchestrateur](#client-functions). Vous pouvez déclencher l’orchestrateur à partir de n’importe quelle source (HTTP, file d’attente, flux d’événements). Chaque instance d’une orchestration a son propre identificateur d’instance. L’identificateur d’instance peut être généré automatiquement (méthode recommandée) ou être généré par l’utilisateur. Vous pouvez utiliser l’identificateur d’instance pour [gérer des instances](durable-functions-instance-management.md) de l’orchestration.

Pour plus d’informations et pour obtenir des exemples, consultez [Déclencheurs d’orchestration](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Fonctions clientes

Les fonctions clientes sont des fonctions déclenchées qui créent de nouvelles instances d’une orchestration. Les fonctions clientes sont le point d’entrée pour la création d’une instance d’orchestration Durable Functions. Vous pouvez déclencher une fonction cliente à partir de n’importe quelle source (HTTP, file d’attente, flux d’événements). Pour écrire une fonction cliente, vous pouvez utiliser n’importe quel langage pris en charge par l’application. 

Les fonctions clientes ont également une liaison de [client d’orchestration](durable-functions-bindings.md#orchestration-client). Une fonction cliente peut utiliser la liaison de client d’orchestration pour créer et gérer des orchestrations durables. 

L’exemple de base d’une fonction cliente est une fonction HTTP déclenchée qui démarre une fonction d’orchestrateur et retourne une réponse de vérification de l’état. Pour obtenir un exemple, consultez [Découverte de l’URL de l’API HTTP](durable-functions-http-api.md#http-api-url-discovery).

Pour plus d’informations et pour obtenir des exemples, consultez [Client d’orchestration](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Fonctionnalités et modèles

Les sections suivantes décrivent les fonctionnalités et les modèles des différents types Durable Functions.

### <a name="sub-orchestrations"></a>Orchestrations secondaires

Les fonctions d’orchestrateur peuvent appeler des fonctions d’activité, mais aussi d’autres fonctions d’orchestrateur. Par exemple, vous pouvez créer une orchestration plus grande à partir d’une bibliothèque de fonctions d’orchestrateur. Vous pouvez aussi exécuter en parallèle plusieurs instances d’une fonction d’orchestrateur.

Pour plus d’informations et pour obtenir des exemples, consultez [Sous-orchestrations](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Minuteurs durables

[Durable Functions](durable-functions-overview.md) fournit des *minuteurs durables* à utiliser dans les fonctions d’orchestrateur dans le but d’implémenter des délais ou de configurer des délais d’expiration pour des actions asynchrones. Utilisez les minuteurs durables dans les fonctions de l’orchestrateur à la place de `Thread.Sleep` et `Task.Delay` (C#) ou de `setTimeout()` et `setInterval()` (JavaScript).

Pour plus d’informations et pour obtenir des exemples, consultez [Minuteurs durables](durable-functions-timers.md).

### <a name="external-events"></a>Événements externes

Les fonctions d’orchestrateur peuvent attendre des événements externes pour mettre à jour une instance d’orchestration. Cette fonctionnalité Durable Functions est souvent utile pour gérer l’interaction humaine ou d’autres rappels externes.

Pour plus d’informations et pour obtenir des exemples, consultez [Événements externes](durable-functions-external-events.md).

### <a name="error-handling"></a>Gestion des erreurs

Utilisez du code pour implémenter des orchestrations Durable Functions. Vous pouvez utiliser les fonctionnalités de gestion des erreurs du langage de programmation. Les modèles tels que `try`/`catch` fonctionnent dans votre orchestration. 

Durable Functions comprend également des stratégies intégrées de nouvelle tentative. Une action peut retarder et retenter des activités automatiquement lors de la survenue d’exceptions. Les nouvelles tentatives permettent de gérer les exceptions temporaires sans avoir à abandonner l’orchestration.

Pour plus d’informations et pour obtenir des exemples, consultez [Gestion des erreurs](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Communication entre plusieurs applications de fonction

Même si une orchestration durable s’exécute dans le contexte d’une seule application de fonction, vous pouvez utiliser des modèles pour coordonner les orchestrations de nombreuses applications de fonction. Même si des communications entre applications sont possibles via HTTP, l’utilisation d’un framework durable pour chaque activité signifie que vous pouvez conserver un processus durable entre deux applications.

Les exemples suivants montrent l’orchestration d’application interfonctions en C# et en JavaScript. Dans chaque exemple, une activité démarre l’orchestration externe. Une autre activité récupère et retourne l’état. L’orchestrateur attend que l’état soit `Complete` avant de continuer.

Voici quelques exemples d’orchestration d’application interfonctions :

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
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
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
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
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

Pour bien démarrer, créez votre première fonction durable en [C#](durable-functions-create-first-csharp.md) ou en [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [En savoir plus sur Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
