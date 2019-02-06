---
title: Gestion des événements externes dans Fonctions durables - Azure
description: Découvrez comment gérer des événements externes dans l’extension Fonctions durables pour Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 7e4b52f0a3ca5e924d9d41e38e51f0cba8b75690
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885811"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Gestion des événements externes dans Fonctions durables (Azure Functions)

Les fonctions d’orchestrateur ont la capacité d’attendre et d’écouter des événements externes. Cette fonctionnalité de [Fonctions durables](durable-functions-overview.md) est souvent utile pour gérer l’interaction humaine ou d’autres déclencheurs externes.

> [!NOTE]
> Les événements externes sont des opérations asynchrones unidirectionnelles. Ils ne sont pas adaptés aux situations où le client qui envoie l’événement a besoin d’une réponse synchrone de la fonction orchestrator.

## <a name="wait-for-events"></a>Attendre des événements

La méthode [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) permet à une fonction d’orchestrateur d’attendre de manière asynchrone et d’écouter un événement externe. La fonction d’orchestrateur qui écoute déclare le *nom* de l’événement et la *forme des données* qu’il s’attend à recevoir.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

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

Dans l’exemple précédent, la fonction écoute un événement unique spécifique et le traite après sa réception.

Vous pouvez écouter plusieurs événements simultanément, comme dans l’exemple suivant, où la fonction attend l’une des trois notifications d’événement possibles.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

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

Dans l’exemple précédent, la fonction écoute *tous* les événements. Elle peut également attendre *tous* les événements.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) attend indéfiniment une entrée.  L’application de fonction peut être déchargée en toute sécurité durant l’attente. Si et quand un événement arrive pour cette instance d’orchestration, elle est automatiquement réveillée et traite immédiatement l’événement.

> [!NOTE]
> Si votre application de fonction utilise le plan de consommation, aucun frais de facturation n’est encouru quand une fonction orchestrator attend une tâche de `WaitForExternalEvent` (.NET) ou de `waitForExternalEvent` (JavaScript), quelle que soit la durée d’attente.

Dans .NET, si la charge utile de l’événement ne peut pas être convertie vers le type attendu `T`, une exception est levée.

## <a name="send-events"></a>Envoyer des événements

La méthode [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) envoie les événements attendus par `WaitForExternalEvent` (.NET) ou `waitForExternalEvent` (JavaScript).  La méthode `RaiseEventAsync` utilise *eventName* et *eventData* comme paramètres. Les données d’événement doivent être sérialisables au format JSON.

Voici un exemple de fonction déclenchée par une file d’attente qui envoie un événement « Approval » à une instance de la fonction d’orchestrateur. L’ID d’instance de l’orchestration provient du corps du message de file d’attente.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

En interne, `RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript) met en file d’attente un message qui est récupéré par la fonction orchestrator en attente. Si l’instance n’est pas en attente sur le *nom d’événement* spécifié, le message d’événement est ajouté à une file d’attente en mémoire. Si l’instance d’orchestration commence ultérieurement à écouter ce *nom d’événement*, elle vérifiera si la file d’attente contient des messages d’événement.

> [!NOTE]
> S’il n’existe aucune instance d’orchestration avec la valeur d’*ID d’instance* spécifiée, le message d’événement est ignoré. Pour plus d’informations sur ce comportement, consultez le [problème GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29). 

> [!WARNING]
> Quand vous développez localement dans JavaScript, vous devez définir la variable d’environnement `WEBSITE_HOSTNAME` sur `localhost:<port>`, par exemple `localhost:7071` pour utiliser les méthodes sur `DurableOrchestrationClient`. Pour plus d’informations sur cette configuration, consultez le [problème GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer des orchestrations externes](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Exécuter un exemple qui attend des événements externes](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Exécuter un exemple qui attend une interaction humaine](durable-functions-phone-verification.md)