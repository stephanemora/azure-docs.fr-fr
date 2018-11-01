---
title: Singletons pour l’extension Fonctions durables - Azure
description: Explique comment utiliser des singletons dans l’extension Fonctions durables pour Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 58e5b06d613ee3e3311b58af64abd2411c637449
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50084439"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orchestrateurs de singleton dans l’extension Fonctions durables (Azure Functions)

Pour les orchestrations de style acteur ou les travaux en arrière-plan, vous avez souvent besoin de vous assurer qu’une seule instance d’un orchestrateur spécifique soit exécutée. Vous pouvez le vérifier grâce à l’extension [Fonctions durables](durable-functions-overview.md), en affectant un ID d’instance spécifique à un orchestrateur lors de sa création.

## <a name="singleton-example"></a>Exemple de singleton

L’exemple C# suivant illustre une fonction de déclencheur HTTP, qui crée une orchestration singleton de travail en arrière-plan. Le code garantit que seule une instance existe pour un ID d’instance spécifié.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

Par défaut, les ID d’instance sont des identificateurs globaux uniques générés de manière aléatoire. Dans ce cas, cependant, l’ID d’instance est passé dans les données d’itinéraire à partir de l’URL. Le code appelle [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) pour vérifier si une instance ayant l’ID spécifié est déjà en cours d’exécution. Si ce n’est pas le cas, une instance est créée avec cet ID.

> [!NOTE]
> Il existe une condition de concurrence potentielle dans cet exemple. L’exécution simultanée de deux instances de **HttpStartSingle** peut entraîner la création de deux instances de singleton distinctes, l’une remplaçant l’autre. Selon vos besoins, cela peut avoir des effets secondaires indésirables. Pour cette raison, il est important de s’assurer l’impossibilité que deux demandes puissent exécuter cette fonction de déclencheur simultanément.

Les détails liés à l’implémentation de la fonction d’orchestrateur ne sont pas importants. Il peut s’agir d’une fonction d’orchestrateur classique, qui démarre et se termine, ou d’une fonction qui s’exécute sans s’arrêter (une [orchestration externe](durable-functions-eternal-orchestrations.md)). Ce qui importe, c’est qu’une seule instance s’exécute à la fois, systématiquement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’appel d’orchestrations secondaires](durable-functions-sub-orchestrations.md)
