---
title: Vue d’ensemble des types de fonctions et fonctionnalités pour les fonctions durables - Azure
description: Découvrez les types de fonctions et de rôles permettant la communication de fonction à fonction dans le cadre d’une orchestration de fonction durable.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 359594ab91b903033ecc303eccd270988be19810
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336524"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>Vue d’ensemble des types de fonctions et fonctionnalités pour les fonctions durables (Azure Functions)

Durable Functions fournit une orchestration avec état d’exécution des fonctions. Une fonction durable est une solution composée de différentes fonctions Azure Functions. Chacune de ces fonctions peut jouer différents rôles dans le cadre d’une orchestration. Le document suivant fournit une vue d’ensemble des types de fonctions impliqués dans une orchestration de fonction durable. Il inclut également quelques modèles courants dans l’interconnexion de fonctions.  Pour commencer dès maintenant, créez votre première fonction durable en [C#](durable-functions-create-first-csharp.md) ou en [JavaScript](quickstart-js-vscode.md).

![Types de fonctions durables][1]  

## <a name="types-of-functions"></a>Types de fonctions

### <a name="activity-functions"></a>Fonctions d’activité

Les fonctions d’activité sont l’unité élémentaire dans une orchestration durable.  Les fonctions d’activité sont les fonctions et les tâches orchestrées dans le processus.  Par exemple, vous pouvez créer une fonction durable pour traiter une commande : vérifier le stock, facturer au client et créer un envoi.  Chacune de ces tâches correspond à une fonction d’activité.  Les fonctions d’activité n’ont aucune restriction concernant le type de travail que vous pouvez y effectuer.  Elles peuvent être écrites dans les [langages pris en charge par Durable Functions](durable-functions-overview.md#language-support). L’infrastructure des tâches durables garantit que chaque fonction d’activité appelée est exécutée au moins une fois au cours d’une orchestration.

Une fonction d’activité doit être déclenchée par un [déclencheur d’activité](durable-functions-bindings.md#activity-triggers).  Les fonctions .NET reçoivent un [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) en tant que paramètre. Vous pouvez également lier le déclencheur à UN autre objet à passer dans les entrées de la fonction. Dans JavaScript, l’entrée est accessible via la propriété `<activity trigger binding name>` sur l’[objet `context.bindings`](../functions-reference-node.md#bindings).

Votre fonction d’activité peut également retourner des valeurs à l’orchestrateur.  Si vous envoyez ou retournez de nombreuses valeurs à partir d’une fonction d’activité, vous pouvez [tirer parti de tuples ou tableaux](durable-functions-bindings.md#passing-multiple-parameters).  Les fonctions d’activité ne peuvent être déclenchées qu’à partir d’une instance d’orchestration.  Bien que du code puisse être partagé entre une fonction d’activité et une autre fonction (par exemple, une fonction déclenchée via HTTP), chaque fonction ne peut avoir qu’un seul déclencheur.

Pour plus d’informations et des exemples, consultez l’[article sur la liaison de fonctions durables](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Fonctions d’orchestrateur

Les fonctions d’orchestrateur sont au cœur d’une fonction durable.  Les fonctions d’orchestrateur décrivent le mode et l’ordre d’exécution des actions.  Les fonctions d’orchestrateur décrivent l’orchestration dans le code (C# ou JavaScript), comme indiqué dans la [vue d’ensemble des fonctions durables](durable-functions-overview.md).  Une orchestration peut avoir de nombreux types différents d’actions, comme des [fonctions d’activité](#activity-functions), [des sous-orchestrations](#sub-orchestrations), l’[attente d’événements externes](#external-events) et des [ minuteurs](#durable-timers).  

Une fonction d’orchestrateur doit être déclenchée par un [déclencheur d’orchestration](durable-functions-bindings.md#orchestration-triggers).

Un orchestrateur est démarré par un [client d’orchestrateur](#client-functions) qui peut lui-même être déclenché à partir de n’importe quelle source (HTTP, files d’attente, flux d’événements).  Chaque instance d’une orchestration a un identificateur d’instance, qui peut être généré automatiquement (recommandé) ou généré par l’utilisateur.  Cet identificateur peut être utilisé pour [gérer des instances](durable-functions-instance-management.md) de l’orchestration.

Pour plus d’informations et des exemples, consultez l’[article sur la liaison de fonctions durables](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Fonctions clientes

Les fonctions clientes sont les fonctions déclenchées qui créent de nouvelles instances d’une orchestration.  Elles sont le point d’entrée pour la création d’une instance d’une orchestration durable.  Les fonctions clientes peuvent être déclenchées par un déclencheur (HTTP, files d’attente, les flux d’événements, etc.) et écrites dans un des langages pris en charge par l’application.  Outre le déclencheur, les fonctions clientes ont une liaison de [client d’orchestration](durable-functions-bindings.md#orchestration-client) qui leur permet de créer et de gérer les orchestrations durables.  L’exemple de base d’une fonction cliente est une fonction HTTP déclenchée qui démarre une fonction d’orchestrateur et retourne une réponse d’état de vérification, comme [indiqué dans l’exemple suivant](durable-functions-http-api.md#http-api-url-discovery).

Pour plus d’informations et des exemples, consultez l’[article sur la liaison de fonctions durables](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Fonctionnalités et modèles

### <a name="sub-orchestrations"></a>Orchestrations secondaires

En plus d’appeler des fonctions d’activité, les fonctions d’orchestrateur peuvent appeler d’autres fonctions d’orchestrateur. Par exemple, vous pouvez créer une orchestration plus grande à partir d’une bibliothèque de fonctions d’orchestrateur. Ou vous pouvez exécuter en parallèle plusieurs instances d’une fonction d’orchestrateur.

Pour plus d’informations et des exemples, consultez l’[article sur la sous-orchestration](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Minuteurs durables

[Fonctions durables](durable-functions-overview.md) fournit *des minuteurs durables* à utiliser dans les fonctions de l’orchestrateur pour implémenter des retards ou configurer des délais d’expiration sur des actions asynchrones. Les minuteurs durables doivent être utilisés dans les fonctions de l’orchestrateur à la place de `Thread.Sleep` et `Task.Delay` (C#) ou `setTimeout()` et `setInterval()` (JavaScript).

Pour plus d’informations et des exemples de minuteurs durables, consultez l’[article sur les minuteurs durables](durable-functions-timers.md).

### <a name="external-events"></a>Événements externes

Les fonctions d’orchestrateur peuvent attendre des événements externes pour mettre à jour une instance d’orchestration. Cette fonctionnalité de Fonctions durables est souvent utile pour gérer l’interaction humaine ou d’autres rappels externes.

Pour plus d’informations et des exemples, consultez l’[article sur les événements externes](durable-functions-external-events.md).

### <a name="error-handling"></a>Gestion des erreurs

Les orchestrations Fonctions durables sont implémentées dans du code et peuvent utiliser les fonctionnalités de gestion des erreurs du langage de programmation.  Cela signifie que les modèles comme « try/catch » fonctionnent dans votre orchestration.  Les fonctions durables intègrent également des stratégies de nouvelle tentative.  Une action peut retarder et retenter des activités automatiquement lors de la survenue d’exceptions.  Les nouvelles tentatives permettent de gérer les exceptions temporaires sans avoir à abandonner l’orchestration.

Pour plus d’informations et des exemples, consultez l’[article sur la gestion des erreurs](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Communication entre plusieurs applications de fonction

Alors qu’une orchestration durable réside généralement dans le contexte d’une application de fonction unique, il existe des modèles qui vous permettent de coordonner les orchestrations entre de nombreuses applications de fonction.  Bien que les communications entre applications puissent se produire sur HTTP, utiliser l’infrastructure durable pour chaque activité signifie que vous pouvez conserver un processus durable entre deux applications.

Ci-dessous figurent des exemples d’orchestration entre plusieurs applications de fonction en C# et JavaScript.  Une des activités démarre l’orchestration externe. Une autre activité récupère et retourne ensuite l’état.  L’orchestrateur attend que l’état soit Terminé avant de continuer.

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now go do more work...
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (fonctions 2.x uniquement)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now go do more work...
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

> [!div class="nextstepaction"]
> [Poursuivre la lecture de la documentation Fonctions durables](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
