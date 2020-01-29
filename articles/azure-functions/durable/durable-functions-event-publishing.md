---
title: Publication de Fonctions durables sur Azure Event Grid (version préliminaire)
description: Découvrez comment configurer la publication automatique de l’extension Fonctions durables sur Azure Event Grid.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 768af2e89d6523f50bd9fcc3d13cc84b711cc6f0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547470"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publication de Fonctions durables sur Azure Event Grid (version préliminaire)

Cet article explique comment configurer Durable Functions de façon à publier les événements de cycle de vie d’orchestration (création, achèvement, échec, etc.) sur une [Rubrique Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) personnalisée.

Voici certains scénarios pour lesquels cette fonctionnalité est utile :

* **Scénarios DevOps, comme les déploiements bleus/verts** : vous avez intérêt à savoir si des tâches sont en cours d’exécution avant d’implémenter la [stratégie de déploiement côte à côte](durable-functions-versioning.md#side-by-side-deployments).

* **Prise en charge du monitoring et des diagnostics avancés** : vous pouvez effectuer le suivi des données sur l’état d’orchestration dans un magasin externe optimisé pour les requêtes, comme SQL Database ou CosmosDB.

* **Activité longue en arrière-plan** : si vous utilisez Durable Functions pour une activité longue en arrière-plan, cette fonctionnalité vous donne des indications sur l’état actuel.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

## <a name="prerequisites"></a>Conditions préalables requises

* Installez [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) dans votre projet Durable Functions.
* Installez l’[émulateur de stockage Azure](../../storage/common/storage-use-emulator.md).
* Installez [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou utilisez [Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-a-custom-event-grid-topic"></a>Créer une rubrique Event Grid personnalisée

Créez une rubrique Event Grid afin d’envoyer des événements à partir de l’extension Durable Functions. Les instructions suivantes vous expliquent comment créer une rubrique à l’aide de l’interface Azure CLI. Pour plus d’informations sur la procédure à suivre avec PowerShell ou le portail Azure, consultez les articles suivants :

* [Guides de démarrage rapide EventGrid : Créer un événement personnalisé – Azure PowerShell](../../event-grid/custom-event-quickstart-powershell.md)
* [Guides de démarrage rapide EventGrid : Créer un événement personnalisé – Portail Azure](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande `az group create`. Azure Event Grid ne prend pas en charge toutes les régions. Pour plus d’informations sur les régions prises en charge, consultez [Vue d’ensemble d’Azure Event Grid](../../event-grid/overview.md).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Créer une rubrique personnalisée

Une rubrique Event Grid fournit un point de terminaison défini par l’utilisateur vers lequel vous envoyez vos événements. Remplacez `<topic_name>` par un nom unique pour votre rubrique. Le nom de la rubrique doit être unique, car il devient une entrée DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Obtenir le point de terminaison et la clé

Obtenez le point de terminaison de la rubrique. Remplacez `<topic_name>` par le nom de votre choix.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Obtenez la clé de la rubrique. Remplacez `<topic_name>` par le nom de votre choix.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Vous pouvez maintenant envoyer des événements à la rubrique.

## <a name="configure-azure-event-grid-publishing"></a>Configurer la publication Azure Event Grid

Dans votre projet Fonctions durables, recherchez le fichier `host.json`.

Ajoutez `eventGridTopicEndpoint` et `eventGridKeySettingName` dans une propriété `durableTask`.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Les propriétés de configuration d’Azure Event Grid disponibles se trouvent dans la [documentation host.json](../functions-host-json.md#durabletask). Après avoir configuré le fichier `host.json`, votre application de fonction envoie des événements de cycle de vie à la rubrique Event Grid. L’exécution de votre application de fonction fonctionne aussi bien localement que dans Azure.

Définissez le paramètre d’application pour la clé de rubrique dans l’application Function App et `local.settings.json`. Le fichier JSON suivant est un exemple de l’instance `local.settings.json` pour le débogage local. Remplacez `<topic_key>` par la clé de rubrique.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Assurez-vous que l’[émulateur de stockage](../../storage/common/storage-use-emulator.md) fonctionne. Il est judicieux d’exécuter la commande `AzureStorageEmulator.exe clear all` avant l’exécution.

## <a name="create-functions-that-listen-for-events"></a>Créer des fonctions qui écoutent les événements

Créez une application de fonction. Il est préférable de la placer dans la même région que la rubrique Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Créer une fonction de déclenchement Event Grid

Créer une fonction dédiée à la réception des événements de cycle de vie. Sélectionnez **Fonction personnalisée**.

![Sélectionnez l’option de création d’une fonction personnalisée.](./media/durable-functions-event-publishing/functions-portal.png)

Choisissez le déclencheur Event Grid, puis sélectionnez `C#`.

![Sélectionnez la fonction de déclenchement Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Saisissez le nom de la fonction, puis sélectionnez `Create`.

![Créez la fonction de déclenchement Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Une fonction présentant le code suivant est créée :

#### <a name="precompiled-c"></a>C# précompilé
```csharp
public static void Run([HttpTrigger] JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script"></a>Script C#

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

Sélectionnez `Add Event Grid Subscription`. Cette opération ajoute un abonnement Event Grid à la rubrique Event Grid créée. Pour plus d’informations, consultez la section [Concepts utilisés dans Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts).

![Sélectionnez le lien de la fonction de déclenchement Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Sélectionnez `Event Grid Topics` pour le **Type de rubrique**. Sélectionnez le groupe de ressources créé pour la rubrique Event Grid. Ensuite, sélectionnez l’instance de la rubrique Event Grid. Appuyez sur `Create`.

![Créer un abonnement Event Grid.](./media/durable-functions-event-publishing/eventsubscription.png)

Vous êtes maintenant prêt à recevoir les événements du cycle de vie.

## <a name="create-durable-functions-to-send-the-events"></a>Créer Durable Functions pour envoyer les événements

Dans votre projet Fonctions durables, commencez le débogage sur votre machine locale.  Le code suivant correspond au code du modèle de l’extension Fonctions durables. Vous avez déjà configuré `host.json` et `local.settings.json` sur votre machine locale.

### <a name="precompiled-c"></a>C# précompilé

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] IDurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
            [DurableClient] IDurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

> [!NOTE]
> Le code précédent correspond à Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`, l’attribut `OrchestrationClient` au lieu de l’attribut `DurableClient` et le type de paramètre `DurableOrchestrationClient` au lieu de `IDurableOrchestrationClient`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

Si vous appelez `Sample_HttpStart` avec Postman ou votre navigateur, Fonctions durables commence à envoyer les événements du cycle de vie. Le point de terminaison est généralement `http://localhost:7071/api/Sample_HttpStart` pour le débogage local.

Consultez les journaux d’activité associés à la fonction créée dans le portail Azure.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schéma d’événement

La liste suivante explique le schéma des événements du cycle de vie :

* **`id`**  : identificateur unique de l’événement Event Grid.
* **`subject`**  : chemin d’accès de l’objet de l’événement. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` sera `Running`, `Completed`, `Failed` et `Terminated`.  
* **`data`**  : paramètres propres à Durable Functions.
  * **`hubName`**  : nom [TaskHub](durable-functions-task-hubs.md).
  * **`functionName`**  : nom de la fonction d’orchestrateur.
  * **`instanceId`**  : identificateur de l’instance Durable Functions.
  * **`reason`**  : données supplémentaires associées à l’événement de suivi. Pour en savoir plus, consultez la section [Diagnostics dans Fonctions durables (Azure Functions)](durable-functions-diagnostics.md).
  * **`runtimeStatus`**  : état du runtime d’orchestration. Running, Completed, Failed, Canceled. (En cours d’exécution, Terminé, En échec ou Annulé)
* **`eventType`**  : "orchestratorEvent"
* **`eventTime`**  : heure de l’événement (UTC).
* **`dataVersion`**  : version du schéma d’événement du cycle de vie.
* **`metadataVersion`**  :  version des métadonnées.
* **`topic`**  : ressource de rubrique Event Grid.

## <a name="how-to-test-locally"></a>Procédure de test local

Pour tester localement, consultez [Débogage local des déclencheurs Event Grid des fonctions Azure](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur la gestion des instances dans Fonctions durables](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [En savoir plus sur le contrôle de version dans Fonctions durables](durable-functions-versioning.md)
