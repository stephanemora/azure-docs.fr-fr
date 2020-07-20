---
title: Gestion des événements externes dans Fonctions durables - Azure
description: Découvrez comment gérer des événements externes dans l’extension Fonctions durables pour Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 387b5d920de4a295366cc7e948862a12cea901d3
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165547"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Gestion des événements externes dans Fonctions durables (Azure Functions)

Les fonctions d’orchestrateur ont la capacité d’attendre et d’écouter des événements externes. Cette fonctionnalité de [Fonctions durables](durable-functions-overview.md) est souvent utile pour gérer l’interaction humaine ou d’autres déclencheurs externes.

> [!NOTE]
> Les événements externes sont des opérations asynchrones unidirectionnelles. Ils ne sont pas adaptés aux situations où le client qui envoie l’événement a besoin d’une réponse synchrone de la fonction orchestrator.

## <a name="wait-for-events"></a>Attendre des événements

Les méthodes [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) et `waitForExternalEvent` (JavaScript) de la [liaison de déclencheur d’orchestration](durable-functions-bindings.md#orchestration-trigger) permettent à une fonction d’orchestrateur d’attendre de façon asynchrone et d’écouter un événement externe. La fonction d’orchestrateur qui écoute déclare le *nom* de l’événement et la *forme des données* qu’il s’attend à recevoir.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> Le code C# précédent correspond à Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

---

Dans l’exemple précédent, la fonction écoute un événement unique spécifique et le traite après sa réception.

Vous pouvez écouter plusieurs événements simultanément, comme dans l’exemple suivant, où la fonction attend l’une des trois notifications d’événement possibles.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> Le code C# précédent correspond à Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

---

Dans l’exemple précédent, la fonction écoute *tous* les événements. Elle peut également attendre *tous* les événements.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> Le code précédent correspond à Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

Dans .NET, si la charge utile de l’événement ne peut pas être convertie vers le type attendu `T`, une exception est levée.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

---

`WaitForExternalEvent` attend indéfiniment une entrée.  L’application de fonction peut être déchargée en toute sécurité durant l’attente. Si et quand un événement arrive pour cette instance d’orchestration, elle est automatiquement réveillée et traite immédiatement l’événement.

> [!NOTE]
> Si votre application de fonction utilise le plan de consommation, aucun frais de facturation n’est encouru quand une fonction orchestrator attend une tâche de `WaitForExternalEvent` (.NET) ou de `waitForExternalEvent` (JavaScript), quelle que soit la durée d’attente.

## <a name="send-events"></a>Envoyer des événements

Vous pouvez utiliser les méthodes [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) ou `raiseEventAsync` (JavaScript) pour envoyer un événement externe à une orchestration. Ces méthodes sont exposées par la liaison du [client d’orchestration](durable-functions-bindings.md#orchestration-client). Vous pouvez également utiliser l’[API HTTP de déclenchement d’événement](durable-functions-http-api.md#raise-event) pour envoyer un événement externe à une orchestration.

Un événement déclenché inclut les paramètres *ID d’instance*, *eventName* et *eventData*. Les fonctions d’orchestrateur gèrent ces événements à l’aide des API `WaitForExternalEvent` (.NET) ou `waitForExternalEvent` (JavaScript). Le paramètre *eventName* doit correspondre à la fois aux terminaisons d’envoi et de réception pour que l’événement soit traité. Les données d’événement doivent également être sérialisables au format JSON.

En interne, les mécanismes de « déclenchement d’événement » mettent en file d’attente un message qui est récupéré par la fonction orchestrator en attente. Si l’instance n’est pas en attente sur le *nom d’événement* spécifié, le message d’événement est ajouté à une file d’attente en mémoire. Si l’instance d’orchestration commence ultérieurement à écouter ce *nom d’événement*, elle vérifiera si la file d’attente contient des messages d’événement.

> [!NOTE]
> S’il n’existe aucune instance d’orchestration avec la valeur d’*ID d’instance* spécifiée, le message d’événement est ignoré.

Voici un exemple de fonction déclenchée par une file d’attente qui envoie un événement « Approval » à une instance de la fonction d’orchestrateur. L’ID d’instance de l’orchestration provient du corps du message de file d’attente.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> Le code C# précédent correspond à Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser l’attribut `OrchestrationClient` au lieu de l’attribut `DurableClient`, et vous devez utiliser le type de paramètre `DurableOrchestrationClient` au lieu de `IDurableOrchestrationClient`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

En interne, `RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript) met en file d’attente un message qui est récupéré par la fonction orchestrator en attente. Si l’instance n’est pas en attente sur le *nom d’événement* spécifié, le message d’événement est ajouté à une file d’attente en mémoire. Si l’instance d’orchestration commence ultérieurement à écouter ce *nom d’événement*, elle vérifiera si la file d’attente contient des messages d’événement.

> [!NOTE]
> S’il n’existe aucune instance d’orchestration avec la valeur d’*ID d’instance* spécifiée, le message d’événement est ignoré.

### <a name="http"></a>HTTP

Voici un exemple de requête HTTP qui déclenche un événement « Approbation » pour une instance d’orchestration. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

Dans ce cas, l’ID d’instance est codé en dur sous la forme *MyInstanceId*.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’implémentation de la gestion des erreurs](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Exécuter un exemple qui attend une interaction humaine](durable-functions-phone-verification.md)