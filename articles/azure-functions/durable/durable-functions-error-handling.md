---
title: 'Gestion des erreurs dans Fonctions durables : Azure'
description: Découvrez comment gérer des erreurs dans l’extension Fonctions durables pour Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 7a55e28f34f36cd02b67e56c6262b9e1f06dde8f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338190"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Gestion des erreurs dans Fonctions durables (Azure Functions)

Les orchestrations Fonctions durables sont implémentées dans du code et peuvent utiliser les fonctionnalités de gestion des erreurs du langage de programmation. Cela dit, lorsque vous incorporez la gestion des erreurs et les compensations dans vos orchestrations, vous n’avez pas besoin d’apprendre de nouveaux concepts. Toutefois, vous devez tenir compte de certains comportements.

## <a name="errors-in-activity-functions"></a>Erreurs liées aux fonctions d’activité

Toute exception levée dans une fonction d’activité est marshalée vers la fonction d’orchestrateur et levée en tant qu’élément `FunctionFailedException`. Vous pouvez écrire du code de gestion des erreurs et de compensation qui correspond le mieux à vos besoins dans la fonction d’orchestrateur.

Par exemple, considérez la fonction d’orchestrateur suivante, qui transfère des fonds d’un compte à un autre :

### <a name="c"></a>C#

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

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

Si l’appel à la fonction **CreditAccount** échoue pour le compte de destination, la fonction d’orchestrateur compense cela replaçant les fonds sur le compte source.

## <a name="automatic-retry-on-failure"></a>Nouvelle tentative automatique en cas d’échec

Lorsque vous appelez les fonctions d’activité ou les fonctions de sous-orchestration, vous pouvez spécifier une stratégie de nouvelle tentative automatique. L’exemple suivant tente d’appeler une fonction jusqu’à trois fois, et attend 5 secondes avant chaque nouvelle tentative :

### <a name="c"></a>C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

L’API `CallActivityWithRetryAsync` (.NET) ou `callActivityWithRetry` (JavaScript) prend un paramètre `RetryOptions`. Les appels de sous-orchestration utilisant l’API `CallSubOrchestratorWithRetryAsync` (.NET) ou `callSubOrchestratorWithRetry` (JavaScript) peuvent utiliser les mêmes stratégies de nouvelle tentative.

Il existe plusieurs options de personnalisation de la stratégie de nouvelle tentative automatique. Les voici :

* **Nombre maximal de tentatives** : Nombre maximal de nouvelles tentatives.
* **Intervalle avant première nouvelle tentative** : Temps d’attente avant la première nouvelle tentative.
* **Coefficient d’interruption** : Coefficient permettant de déterminer le taux d’augmentation de l’interruption. La valeur par défaut est de 1.
* **Intervalle maximal entre deux nouvelles tentatives** : Durée maximale de l’attente entre deux nouvelles tentatives.
* **Délai de nouvelle tentative** : Temps que passe le système à effectuer de nouvelles tentatives. Par défaut, le système effectue ces nouvelles tentatives indéfiniment.
* **Descripteur** : Un rappel défini par l’utilisateur peut être spécifié, qui détermine si un appel de fonction doit être effectué à nouveau.

## <a name="function-timeouts"></a>Délais d’expiration des fonctions

Vous souhaiterez peut-être abandonner un appel de fonction dans une fonction d’orchestration si elle prend trop de temps. Actuellement, la méthode adéquate pour cela consiste à créer un [minuteur durable](durable-functions-timers.md) à l’aide de `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript) conjointement avec `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript), comme dans l’exemple suivant :

### <a name="c"></a>C#

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

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

> [!NOTE]
> Ce mécanisme ne termine pas réellement l’exécution des fonctions d’activité en cours. Il permet simplement à la fonction de l’orchestrateur d’ignorer le résultat et de continuer. Pour plus d’informations, voir la documentation [Minuteurs](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Exceptions non prises en charge

Si une fonction d’orchestration échoue avec une exception non prise en charge, les détails de cette dernière sont enregistrés et l’instance s’exécute avec un état `Failed`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Comment diagnostiquer des problèmes](durable-functions-diagnostics.md)
