---
title: Orchestrations secondaires pour Fonctions durables - Azure
description: Guide pratique pour appeler des orchestrations à partir d’orchestrations dans l’extension Fonctions durables pour Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: df4cfd8cdf720dd085c3f14ad518c557f270ffa4
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340859"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Orchestrations secondaires dans Fonctions durables (Azure Functions)

En plus d’appeler des fonctions d’activité, les fonctions de l’orchestrateur peuvent appeler d’autres fonctions d’orchestrateur. Par exemple, vous pouvez créer une orchestration plus grande à partir d’une bibliothèque de fonctions d’orchestrateur. Ou vous pouvez exécuter en parallèle plusieurs instances d’une fonction d’orchestrateur.

Une fonction d’orchestrateur peut appeler une autre fonction d’orchestrateur en appelant la méthode [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) ou [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) dans .NET, ou les méthodes `callSubOrchestrator` ou `callSubOrchestratorWithRetry` dans JavaScript. L’article [Gestion des erreurs et compensation](durable-functions-error-handling.md#automatic-retry-on-failure) fournit des informations supplémentaires sur la nouvelle tentative automatique.

Les fonctions d’orchestrateur secondaires se comportent comme des fonctions d’activité du point de vue de l’appelant. Elles peuvent retourner une valeur, lever une exception et être attendues par la fonction d’orchestrateur parente.

## <a name="example"></a>Exemples

L’exemple suivant montre un scénario IoT (« Internet of Things ») dans lequel plusieurs appareils doivent être configurés. Une orchestration spécifique doit se produire pour chacun des appareils, ce qui peut se présenter comme suit :

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

Cette fonction d’orchestrateur peut être utilisée en l’état pour la configuration unique d’un appareil, ou elle peut faire partie d’une orchestration plus importante. Dans ce cas, la fonction d’orchestrateur parente peut planifier des instances de `DeviceProvisioningOrchestration` à l’aide de l’API `CallSubOrchestratorAsync` (C#) or `callSubOrchestrator` (JavaScript).

Voici un exemple montrant comment exécuter en parallèle plusieurs fonctions d’orchestrateur.

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez ce que représentent les hubs de tâches et comment les configurer](durable-functions-task-hubs.md)
