---
title: Minuteurs dans Fonctions durables - Azure
description: Découvrez comment implémenter des minuteurs durables dans l’extension Fonctions durables pour Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: e81e842e059e09f24627138ba9fbf6510a603efe
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353292"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Minuteurs dans Fonctions durables (Azure Functions)

[Fonctions durables](durable-functions-overview.md) fournit *des minuteurs durables* à utiliser dans les fonctions de l’orchestrateur pour implémenter des retards ou configurer des délais d’expiration sur des actions asynchrones. Les minuteurs durables doivent être utilisés dans les fonctions de l’orchestrateur à la place de `Thread.Sleep` et `Task.Delay` (C#) ou `setTimeout()` et `setInterval()` (JavaScript).

Vous pouvez créer un minuteur durable en appelant la méthode [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) de [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) en .NET, ou la méthode `createTimer` de `DurableOrchestrationContext` en JavaScript. La méthode retourne une tâche qui reprend à une date et une heure spécifiées.

## <a name="timer-limitations"></a>Limitations des minuteurs

Lorsque vous créez un minuteur qui expire à 16:30, l’infrastructure des tâches durables sous-jacent empile un message qui est uniquement visible à 16:30. Durant l’exécution dans le plan de consommation d’Azure Functions, le nouveau message visible du minuteur garantit que l’application de fonction est activée sur une machine virtuelle appropriée.

> [!NOTE]
> * En raison des limitations du stockage Azure, les minuteurs durables ne peuvent pas dépasser 7 jours. Nous travaillons actuellement sur une [demande de fonctionnalité pour étendre les minuteurs à plus de 7 jours](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Utilisez toujours [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) au lieu de `DateTime.UtcNow` en .NET et `currentUtcDateTime` au lieu de `Date.now` ou `Date.UTC` en JavaScript comme indiqué dans les exemples ci-dessous pour le calcul d’une échéance relative d’un minuteur durable.

## <a name="usage-for-delay"></a>Utilisation des retards

L’exemple suivant montre comment utiliser des minuteurs durables pour retarder une exécution. Dans cet exemple, une notification de facturation est émise tous les jours pendant dix jours.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (fonctions 2.x uniquement)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Evitez les boucles infinies dans les fonctions de l’orchestrateur. Pour plus d’informations sur la façon d’implémenter efficacement et en toute sécurité des scénarios de boucles infinies, consultez [Orchestrations externes](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Utilisation des délais d’expiration

Cet exemple montre comment utiliser des minuteurs durables pour implémenter des délais d’expiration.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (fonctions 2.x uniquement)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Utilisez `CancellationTokenSource` pour annuler un minuteur durable (C#) ou appelez `cancel()` sur le `TimerTask` retourné (JavaScript) si votre code n’attendra pas qu’il se termine. Dans l’infrastructure des tâches durables, une orchestration ne passe à l’état « terminé » que quand toutes les tâches en attente sont terminées ou annulées.

Ce mécanisme ne termine pas réellement l’exécution des fonctions d’activité en cours. Il permet simplement à la fonction de l’orchestrateur d’ignorer le résultat et de continuer. Si votre application de fonction utilise le plan de consommation, vous serez toujours facturé pour le temps consacré et la mémoire consommée par la fonction d’activité abandonnée. Par défaut, les fonctions exécutées dans le plan de consommation ont un délai d’expiration de cinq minutes. Si cette limite est dépassée, l’hôte d’Azure Functions est recyclé pour arrêter toute exécution en cours et éviter toute facturation supplémentaire. Le [délai d’expiration des fonctions est configurable](../functions-host-json.md#functiontimeout).

Pour obtenir un exemple plus détaillé d’implémentation de délais d’expiration dans les fonctions de l’orchestrateur, consultez la procédure pas à pas [Interaction humaine et délais d’expiration - Vérification par téléphone](durable-functions-phone-verification.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déclenchement et gestion d’événements externes](durable-functions-external-events.md)
