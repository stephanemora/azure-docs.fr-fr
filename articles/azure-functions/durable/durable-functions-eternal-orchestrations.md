---
title: Orchestrations externes dans Fonctions durables - Azure
description: Découvrez comment implémenter des orchestrations externes à l’aide de l’extension Fonctions durables pour Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 572fec4d6e47efd734bc84a40dc974c79bd619fb
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262977"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orchestrations externes dans Fonctions durables (Azure Functions)

Les *orchestrations externes* sont des fonctions d’orchestrateur qui ne se terminent jamais. Elles sont utiles si vous souhaitez utiliser [Fonctions Durable](durable-functions-overview.md) pour des agrégateurs et tout scénario qui nécessite une boucle infinie.

## <a name="orchestration-history"></a>Historique d’orchestration

Comme expliqué dans la rubrique [Historique d’orchestration](durable-functions-orchestrations.md#orchestration-history), l’infrastructure Durable Task Framework effectue le suivi de l’historique de chaque orchestration de fonction. Cet historique augmente sans cesse tant que la fonction d’orchestrateur continue de planifier une nouvelle tâche. Si la fonction d’orchestrateur entre dans une boucle infinie et planifie sans cesse des tâches, cet historique risque de devenir très volumineux et de provoquer d’importants problèmes de performances. Le concept *d’orchestration externe* a été conçu pour limiter ces types de problèmes dans les applications nécessitant des boucles infinies.

## <a name="resetting-and-restarting"></a>Réinitialisation et redémarrage

Au lieu d’utiliser des boucles infinies, les fonctions d’orchestrateur réinitialisent leur état en appelant la méthode `ContinueAsNew` (.NET) ou `continueAsNew` (JavaScript) de la [liaison du déclencheur d’orchestration](durable-functions-bindings.md#orchestration-trigger). Cette méthode n’utilise qu’un seul paramètre JSON sérialisable, qui devient la nouvelle entrée de la prochaine génération de la fonction d’orchestrateur.

Lorsque la méthode `ContinueAsNew` est appelée, l’instance garde un message dans sa propre file d’attente avant de se fermer. Le message redémarre l’instance avec la nouvelle valeur d’entrée. Le même ID d’instance est conservé, mais l’historique de la fonction d’orchestrateur est tronqué.

> [!NOTE]
> L’infrastructure des tâches durables conserve le même ID d’instance, mais crée en interne un nouvel *ID d’exécution* pour la fonction d’orchestrateur réinitialisée par `ContinueAsNew`. Cet ID d’exécution n’est généralement pas exposé en externe, mais il peut être utile d’en tenir compte lors du débogage de l’exécution d’orchestration.

## <a name="periodic-work-example"></a>Exemple de travail périodique

Voici un cas d’utilisation des orchestrations externes : un code a besoin d’effectuer un travail périodique indéfiniment.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> L’exemple C# précédent porte sur Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

---

La différence entre cet exemple et une fonction déclenchée par un minuteur est que les heures de déclenchement du nettoyage ne sont pas ici basées sur une planification. Par exemple, une planification CRON qui exécute une fonction toutes les heures se produira à 1 h 00, 2 h 00, 3 h 00 etc., et risque d’entraîner des problèmes de chevauchement. Mais dans cet exemple, si le nettoyage prend 30 minutes, il sera alors planifié à 1 h 00, 2 h 30, 4 h 00, etc., et il n’existe aucun risque de chevauchement.

## <a name="starting-an-eternal-orchestration"></a>Démarrage d’une orchestration externe

Utilisez la méthode `StartNewAsync` (.NET) ou `startNew` (JavaScript) pour démarrer une orchestration externe, comme vous le feriez pour toute autre fonction d’orchestration.  

> [!NOTE]
> Si vous devez vous assurer qu’une orchestration externe singleton est en cours d’exécution, il est important de conserver le même `id` d’instance lors du démarrage de l’orchestration. Pour plus d’informations, consultez [Gestion d’instance](durable-functions-instance-management.md).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Le code précédent correspond à Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser l’attribut `OrchestrationClient` au lieu de l’attribut `DurableClient`, et vous devez utiliser le type de paramètre `DurableOrchestrationClient` au lieu de `IDurableOrchestrationClient`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>Fermeture d’une orchestration externe

Si une fonction d’orchestrateur doit se terminer, il vous suffit de ne *pas* appeler `ContinueAsNew` et de laisser la fonction se terminer.

Si une fonction d’orchestrateur est dans une boucle infinie et doit être arrêtée, utilisez la méthode `TerminateAsync` (.NET) ou `terminate` (JavaScript) de la [liaison du client d’orchestration](durable-functions-bindings.md#orchestration-client) pour l’arrêter. Pour plus d’informations, consultez [Gestion d’instance](durable-functions-instance-management.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Apprendre à implémenter des orchestrations de singleton](durable-functions-singletons.md)
