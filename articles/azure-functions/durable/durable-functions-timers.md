---
title: Minuteurs dans Fonctions durables - Azure
description: Découvrez comment implémenter des minuteurs durables dans l’extension Fonctions durables pour Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261481"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Minuteurs dans Fonctions durables (Azure Functions)

[Fonctions durables](durable-functions-overview.md) fournit *des minuteurs durables* à utiliser dans les fonctions de l’orchestrateur pour implémenter des retards ou configurer des délais d’expiration sur des actions asynchrones. Les minuteurs durables doivent être utilisés dans les fonctions de l’orchestrateur à la place de `Thread.Sleep` et `Task.Delay` (C#) ou `setTimeout()` et `setInterval()` (JavaScript).

Vous créez un minuteur durable en appelant la méthode `CreateTimer` (.NET) ou la méthode `createTimer` (JavaScript) de la [liaison de déclencheur d’orchestration](durable-functions-bindings.md#orchestration-trigger). La méthode retourne une tâche qui se termine à une date et une heure spécifiées.

## <a name="timer-limitations"></a>Limitations des minuteurs

Lorsque vous créez un minuteur qui expire à 16:30, l’infrastructure des tâches durables sous-jacent empile un message qui devient uniquement visible à 16:30. Durant l’exécution dans le plan de consommation d’Azure Functions, le message de minuteur nouvellement visible garantit que l’application de fonction est activée sur une machine virtuelle appropriée.

> [!NOTE]
> * Les minuteurs durables sont actuellement limités à 7 jours. Si des délais plus longs sont nécessaires, ils peuvent être simulés à l’aide des API de minuteur dans une boucle `while`.
> * Utilisez toujours `CurrentUtcDateTime` au lieu de `DateTime.UtcNow` en .NET ou `currentUtcDateTime` au lieu de `Date.now` ou de `Date.UTC` en JavaScript lors du calcul de l’heure de déclenchement pour les minuteurs durables. Pour plus d’informations, consultez l’article [Contraintes du code des fonctions d’orchestrateur](durable-functions-code-constraints.md).

## <a name="usage-for-delay"></a>Utilisation des retards

L’exemple suivant montre comment utiliser des minuteurs durables pour retarder une exécution. Dans cet exemple, une notification de facturation est émise tous les jours pendant 10 jours.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> L’exemple C# précédent cible Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

---

> [!WARNING]
> Evitez les boucles infinies dans les fonctions de l’orchestrateur. Pour plus d’informations sur la façon d’implémenter efficacement et en toute sécurité des scénarios de boucles infinies, consultez [Orchestrations externes](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Utilisation des délais d’expiration

Cet exemple montre comment utiliser des minuteurs durables pour implémenter des délais d’expiration.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> L’exemple C# précédent cible Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

---

> [!WARNING]
> Utilisez un `CancellationTokenSource` (.NET) ou appelez `cancel()` sur le `TimerTask` (JavaScript) retourné pour annuler un minuteur durable si votre code n’attend pas qu’il se termine. Dans le framework des tâches durables, une orchestration ne passe à l’état « terminé » que quand toutes les tâches en attente sont terminées ou annulées.

Ce mécanisme d’annulation ne termine pas la fonction d’activité en cours ni les exécutions de sous-orchestrations. Il permet simplement à la fonction de l’orchestrateur d’ignorer le résultat et de continuer. Si votre application de fonction utilise le plan de consommation, vous serez toujours facturé pour le temps consacré et la mémoire consommée par la fonction d’activité abandonnée. Par défaut, les fonctions exécutées dans le plan de consommation ont un délai d’expiration de cinq minutes. Si cette limite est dépassée, l’hôte d’Azure Functions est recyclé pour arrêter toute exécution en cours et éviter toute facturation supplémentaire. Le [délai d’expiration des fonctions est configurable](../functions-host-json.md#functiontimeout).

Pour obtenir un exemple plus détaillé d’implémentation de délais d’expiration dans les fonctions de l’orchestrateur, consultez l’article [Interaction humaine et délais d’expiration – Vérification par téléphone](durable-functions-phone-verification.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déclenchement et gestion d’événements externes](durable-functions-external-events.md)
