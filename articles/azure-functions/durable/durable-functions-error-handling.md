---
title: 'Gestion des erreurs dans Fonctions durables : Azure'
description: Découvrez comment gérer des erreurs dans l’extension Fonctions durables pour Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 905b71ab1e9a054eaeb6087489d14565933c8a46
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261634"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Gestion des erreurs dans Fonctions durables (Azure Functions)

Les orchestrations de fonctions durables sont implémentées dans du code et peuvent utiliser les fonctionnalités de gestion des erreurs intégrées dans le langage de programmation. Vous n’avez pas vraiment besoin d’apprendre de nouveaux concepts pour ajouter la gestion des erreurs et les compensations dans vos orchestrations. Toutefois, vous devez tenir compte de certains comportements.

## <a name="errors-in-activity-functions"></a>Erreurs liées aux fonctions d’activité

Toute exception levée dans une fonction d’activité est marshalée en retour vers la fonction d’orchestrateur et levée en tant qu’élément `FunctionFailedException`. Vous pouvez écrire du code de gestion des erreurs et de compensation qui correspond le mieux à vos besoins dans la fonction d’orchestrateur.

Par exemple, considérez la fonction d’orchestrateur suivante, qui transfère des fonds d’un compte à un autre :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> Les exemples C# précédents portent sur Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

---

Si le premier appel à la fonction **CreditAccount** échoue, la fonction d’orchestrateur compense en recréditant les fonds sur le compte source.

## <a name="automatic-retry-on-failure"></a>Nouvelle tentative automatique en cas d’échec

Lorsque vous appelez les fonctions d’activité ou les fonctions de sous-orchestration, vous pouvez spécifier une stratégie de nouvelle tentative automatique. L’exemple suivant tente d’appeler une fonction jusqu’à trois fois, et attend 5 secondes avant chaque nouvelle tentative :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Les exemples C# précédents portent sur Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

L'appel de fonction de l'activité de l'exemple précédent utilise un paramètre pour configurer une stratégie de nouvelles tentatives automatiques. Il existe plusieurs options de personnalisation de la stratégie de nouvelle tentative automatique :

* **Nombre maximal de tentatives** : Nombre maximal de nouvelles tentatives.
* **Intervalle avant première nouvelle tentative** : Temps d’attente avant la première nouvelle tentative.
* **Coefficient d’interruption** : Coefficient permettant de déterminer le taux d’augmentation de l’interruption. La valeur par défaut est de 1.
* **Intervalle maximal entre deux nouvelles tentatives** : Durée maximale de l’attente entre deux nouvelles tentatives.
* **Délai de nouvelle tentative** : Temps que passe le système à effectuer de nouvelles tentatives. Par défaut, le système effectue ces nouvelles tentatives indéfiniment.
* **Descripteur** : Un rappel défini par l’utilisateur peut être spécifié pour déterminer s’il convient de réessayer une fonction.

## <a name="function-timeouts"></a>Délais d’expiration des fonctions

Vous souhaiterez peut-être abandonner un appel de fonction au sein d’une fonction d’orchestrateur s’il prend trop de temps. Actuellement, la méthode adéquate pour cela consiste à créer un [minuteur durable](durable-functions-timers.md) à l’aide de `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript) conjointement avec `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript), comme dans l’exemple suivant :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> Les exemples C# précédents portent sur Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

---

> [!NOTE]
> Ce mécanisme ne termine pas réellement l’exécution des fonctions d’activité en cours. Il permet simplement à la fonction de l’orchestrateur d’ignorer le résultat et de continuer. Pour plus d’informations, voir la documentation [Minuteurs](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Exceptions non prises en charge

Si une fonction d’orchestration échoue avec une exception non prise en charge, les détails de cette dernière sont enregistrés et l’instance s’exécute avec un état `Failed`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les orchestrations éternelles](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Comment diagnostiquer des problèmes](durable-functions-diagnostics.md)
