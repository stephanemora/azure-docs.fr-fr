---
title: Publication de Fonctions durables sur Azure Event Grid (version préliminaire)
description: Découvrez comment configurer la publication automatique de l’extension Fonctions durables sur Azure Event Grid.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/20/2018
ms.author: tdykstra
ms.openlocfilehash: 50e517e5719fb102fd91072abe59d3908176278e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762460"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publication de Fonctions durables sur Azure Event Grid (version préliminaire)

Cet article vous explique comment définir l’extension Fonctions durables pour publier les événements de cycle de vie d’orchestration (création, achèvement, échec) sur une [rubrique Azure Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/overview) personnalisée. 

Voici certains scénarios pour lesquels cette fonctionnalité est utile :

* **Scénarios DevOps comme les déploiements bleu/vert** : vous avez intérêt à identifier les tâches en exécution avant d’implémenter la [stratégie de déploiement côté à côté](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-versioning#side-by-side-deployments).

* **Prise en charge avancée de la surveillance et des diagnostics** : vous pouvez effectuer le suivi des données sur l’état d’orchestration dans un magasin externe optimisé pour les requêtes, comme SQL Database ou CosmosDB.

* **Activité d’arrière-plan de longue durée** : si vous utilisez l’extension Fonctions durables pour une activité d’arrière-plan de longue durée, cette fonctionnalité vous donne des indications sur l’état actuel.

## <a name="prerequisites"></a>Prérequis


* Installez [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc ou une version ultérieure dans votre projet Fonctions durables.
* Installez l’[émulateur de stockage Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator).
* Installez [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) ou utilisez [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

## <a name="create-a-custom-event-grid-topic"></a>Créer une rubrique Event Grid personnalisée

Créez une rubrique Event Grid afin d’envoyer des événements à partir de l’extension Fonctions durables. Les instructions suivantes vous expliquent comment créer une rubrique à l’aide de l’interface Azure CLI. Pour plus d’informations sur la procédure à suivre avec PowerShell ou le portail Azure, consultez les articles suivants :

* [Créer et acheminer des événements personnalisés avec Azure PowerShell et Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-powershell)
* [Créer et acheminer des événements personnalisés avec le portail Azure et Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande `az group create`. Actuellement, Event Grid ne prend pas en charge l’ensemble des régions. Pour plus d’informations sur les régions prises en charge, consultez la [Vue d’ensemble d’Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

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

Ajoutez `EventGridTopicEndpoint` et `EventGridKeySettingName` dans une propriété `durableTask`.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

* **EventGridTopicEndpoint** - Le point de terminaison de la rubrique Event Grid.
* **EventGridKeySettingName** - La clé du paramètre d’application sur votre fonction Azure. L’extension Fonctions durables récupère la clé de la rubrique Event Grid à partir de la valeur.

Une fois que vous avez configuré le fichier `host.json`, votre projet Fonctions durables commence à envoyer des événements de cycle de vie à la rubrique Event Grid. Cela fonctionne durant l’exécution dans l’application Function App et en local.

Définissez le paramètre d’application pour la clé de rubrique dans l’application Function App et `local.setting.json`. Le fichier JSON suivant est un exemple de l’instance `local.settings.json` pour le débogage local. Remplacez `<topic_key>` par la clé de rubrique.  

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

Assurez-vous que l’[émulateur de stockage](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) fonctionne. Il est judicieux d’exécuter la commande `AzureStorageEmulator.exe clear all` avant l’exécution.

## <a name="create-functions-that-listen-for-events"></a>Créer des fonctions qui écoutent les événements

Créez une application de fonction. Il est préférable de la placer dans la région de la rubrique Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Créer une fonction de déclenchement Event Grid

Créer une fonction dédiée à la réception des événements de cycle de vie. Sélectionnez **Fonction personnalisée**. 

![Sélectionnez l’option de création d’une fonction personnalisée.](media/durable-functions-event-publishing/functions-portal.png)

Choisissez le déclencheur Event Grid, puis sélectionnez `C#`.

![Sélectionnez la fonction de déclenchement Event Grid.](media/durable-functions-event-publishing/eventgrid-trigger.png)

Saisissez le nom de la fonction, puis sélectionnez `Create`.

![Créez la fonction de déclenchement Event Grid.](media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Une fonction présentant le code suivant est créée : 

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

Sélectionnez `Add Event Grid Subscription`. Cette opération ajoute un abonnement Event Grid à la rubrique Event Grid créée. Pour plus d’informations, consultez la section [Concepts utilisés dans Azure Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/concepts).

![Sélectionnez le lien de la fonction de déclenchement Event Grid.](media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Sélectionnez `Event Grid Topics` pour le **Type de rubrique**. Sélectionnez le groupe de ressources créé pour la rubrique Event Grid. Ensuite, sélectionnez l’instance de la rubrique Event Grid. Appuyez sur `Create`.

![Créer un abonnement Event Grid.](media/durable-functions-event-publishing/eventsubscription.png)

Vous êtes maintenant prêt à recevoir les événements du cycle de vie. 

## <a name="create-durable-functions-to-send-the-events"></a>Créez l’extension Fonctions durables afin d’envoyer les événements.

Dans votre projet Fonctions durables, commencez le débogage sur votre machine locale.  Le code suivant correspond au code du modèle de l’extension Fonctions durables. Vous avez déjà configuré `host.json` et `local.settings.json` sur votre machine locale. 

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
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
        public static string SayHello([ActivityTrigger] string name, TraceWriter log)
        {
            log.Info($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            TraceWriter log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.Info($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Si vous appelez `Sample_HttpStart` avec Postman ou votre navigateur, Fonctions durables commence à envoyer les événements du cycle de vie. Le point de terminaison est généralement `http://localhost:7071/api/Sample_HttpStart` pour le débogage local.

Consultez les journaux associés à la fonction créée dans le portail Azure.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
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
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
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
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schéma d’événement

La liste suivante explique le schéma des événements du cycle de vie :

* **id** : identifiant unique de l’événement Event Grid
* **subject** : chemin de l’objet de l’événement `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` sera `Running`, `Completed`, `Failed` et `Terminated`.  
* **data** : paramètres spécifiques à Fonctions durables.
    * **hubName** : nom [TaskHub](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-task-hubs).
    * **functionName** : nom de la fonction Orchestrator.
    * **instanceId** : identifiant de l’instance Fonctions durables.
    * **reason**: données supplémentaires associées à l’événement de suivi. Pour en savoir plus, consultez la section [Diagnostics dans Fonctions durables (Azure Functions)](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-diagnostics).
    * **runtimeStatus** : état du runtime de l’orchestration. Running, Completed, Failed, Canceled. (En cours d’exécution, Terminé, En échec ou Annulé) 
* **eventType** : orchestratorEvent
* **eventTime** : horodatage de l’événement (UTC).
* **dataVersion** : version du schéma d’événement de cycle de vie.
* **metadataVersion** : version des métadonnées.
* **topic** : ressource de la rubrique Event Grid.

## <a name="how-to-test-locally"></a>Procédure de test local

Pour tester localement, utilisez [ngrok](functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur la gestion des instances dans Fonctions durables](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [En savoir plus sur le contrôle de version dans Fonctions durables](durable-functions-versioning.md)
