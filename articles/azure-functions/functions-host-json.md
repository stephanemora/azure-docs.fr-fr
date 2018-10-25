---
title: Informations de référence sur le fichier host.json pour Azure Functions
description: Documentation de référence pour le fichier host.json d’Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 704a41ec840e2a252a1bbb5c20688f722bd0cdfd
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887034"
---
# <a name="hostjson-reference-for-azure-functions"></a>Informations de référence sur le fichier host.json pour Azure Functions

Le fichier de métadonnées *host.json* contient les options de configuration globale qui affectent l’ensemble des fonctions d’une application de fonction. Cet article répertorie les paramètres qui sont disponibles. Le schéma JSON est sur http://json.schemastore.org/host.

> [!NOTE]
> Il existe des différences importantes dans le fichier *host.json* entre les versions v1 et v2 du runtime Azure Functions. La `"version": "2.0"` est requise pour une application de fonction qui cible le runtime v2.

Les autres options de configuration d’application de fonction sont managées dans vos [paramètres d’application](functions-app-settings.md).

Certains paramètres host.json sont uniquement utilisés lors de l’exécution locale dans le fichier [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>Exemple de fichier host.json

L’exemple de fichier *host.json* suivant contient toutes les options possibles spécifiées.

### <a name="version-2x"></a>Version 2.x

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "eventHubs": {
          "maxBatchSize": 64,
          "prefetchCount": 256,
          "batchCheckpointFrequency": 1
        },
        "http": {
            "routePrefix": "api",
            "maxConcurrentRequests": 100,
            "maxOutstandingRequests": 30
        },
        "queues": {
            "visibilityTimeout": "00:00:10",
            "maxDequeueCount": 3
        },
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        },
        "serviceBus": {
          "maxConcurrentCalls": 16,
          "prefetchCount": 100,
          "autoRenewTimeout": "00:05:00"
        }
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "sampling": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

### <a name="version-1x"></a>Version 1.x

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

Les sections suivantes de cet article expliquent chaque propriété de niveau supérieur. Toutes les autres propriétés sont facultatives sauf indication contraire.

## <a name="aggregator"></a>aggregator

Spécifie le nombre d’appels de fonction agrégés lorsque vous [calculez des métriques pour Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Propriété |Default  | Description |
|---------|---------|---------| 
|batchSize|1 000|Nombre maximal de requêtes à agréger.| 
|flushTimeout|00:00:30|Période maximale d’agrégation.| 

Les appels de fonction sont agrégés lorsque la première des deux limites est atteinte.

## <a name="applicationinsights"></a>applicationInsights

Contrôle la [fonctionnalité d’échantillonnage dans Application Insights](functions-monitoring.md#configure-sampling). Dans la version 2.x, ce paramètre est un enfant de la [journalisation](#log).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|isEnabled|true|Active ou désactive l’échantillonnage.| 
|maxTelemetryItemsPerSecond|5.|Seuil à partir duquel l’échantillonnage débute.| 

## <a name="durabletask"></a>durableTask

Paramètres de configuration de [Fonctions durables](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

Les noms de hubs de tâches doivent commencer par une lettre et contenir uniquement des lettres et des chiffres. S’il n’est pas spécifié, le nom du hub de tâches par défaut d’une application de fonction est **DurableFunctionsHub**. Pour en savoir plus, consultez la section relative aux [hubs de tâches](durable-functions-task-hubs.md).

|Propriété  |Default | Description |
|---------|---------|---------|
|HubName|DurableFunctionsHub|D’autres noms de [hub de tâches](durable-functions-task-hubs.md) peuvent être utilisés pour isoler plusieurs applications Fonctions durables les unes des autres, même si elles s’appuient sur le même serveur principal de stockage.|
|ControlQueueBatchSize|32|Nombre de messages à extraire de la file d’attente de contrôle en une seule fois.|
|PartitionCount |4|Nombre de partitions pour la file d’attente de contrôle. Doit être un entier positif compris entre 1 et 16.|
|ControlQueueVisibilityTimeout |5 minutes|Délai d’expiration de la visibilité des messages supprimés de la file d’attente de contrôle.|
|WorkItemQueueVisibilityTimeout |5 minutes|Délai d’expiration de la visibilité des messages supprimés de la file d’attente des éléments de travail.|
|MaxConcurrentActivityFunctions |10 fois le nombre de processeurs sur l’ordinateur actuel|Nombre maximal de fonctions d’activité pouvant être traitées simultanément sur une seule instance d’hôte.|
|MaxConcurrentOrchestratorFunctions |10 fois le nombre de processeurs sur l’ordinateur actuel|Nombre maximal de fonctions d’activité pouvant être traitées simultanément sur une seule instance d’hôte.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Nom du paramètre d’application qui contient la chaîne de connexion de stockage Azure utilisée pour gérer les ressources de stockage Azure sous-jacentes.|
|TraceInputsAndOutputs |false|Valeur indiquant s’il faut tracer les entrées et sorties des appels de fonction. Lors du traçage d’événements d’exécution de fonctions, le nombre d’octets est inclus par défaut dans les entrées et les sorties sérialisées pour les appels de fonction. Cela fournit un minimum d’informations sur l’apparence des entrées et sorties sans encombrer les journaux ou exposer accidentellement des informations sensibles dans les journaux. La définition de cette propriété sur True amène la journalisation de la fonction par défaut à consigner la totalité du contenu des entrées et sorties de fonction.|
|LogReplayEvents|false|Valeur indiquant s’il faut écrire les événements de relecture d’orchestration à Application Insights.|
|EventGridTopicEndpoint ||URL du point de terminaison de la rubrique personnalisée Azure Event Grid. Lorsque cette propriété est définie, les événements de notification du cycle de vie d’orchestration sont publiés sur ce point de terminaison. Cette propriété prend en charge la résolution de paramètres de l’application.|
|EventGridKeySettingName ||Nom du paramètre d’application contenant la clé utilisée pour l’authentification à l’aide de la rubrique personnalisée Azure Event Grid sur `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Nombre de nouvelles tentatives à effectuer en cas d’échec de la publication de la rubrique Event Grid.|
|EventGridPublishRetryInterval|5 minutes|L’intervalle avant nouvelle tentative de publication Event Grid au format *hh:mm:ss*.|

Beaucoup sont destinés à l’optimisation des performances. Pour plus d’informations, consultez [Performances et mise à l’échelle](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>eventHub

Paramètres de configuration pour les [déclencheurs et liaisons Event Hub](functions-bindings-event-hubs.md). Dans la version 2.x, il s’agit d’un enfant des [extensions](#extensions).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="extensions"></a>extensions

*Version 2.x uniquement.*

Propriété qui retourne un objet qui contient tous les paramètres spécifiques d’une liaison, tel que [http](#http) et [eventHub](#eventhub).

## <a name="functions"></a>functions

Liste des fonctions que l’hôte de travail exécute. Un tableau vide désigne l’exécution de toutes les fonctions. Utilisée uniquement pour une [exécution locale](functions-run-local.md). Dans les applications de fonction dans Azure, vous devez plutôt suivre la procédure décrite dans [Guide pratique pour désactiver des fonctions dans Azure Functions](disable-function.md) pour désactiver des fonctions spécifiques au lieu d’utiliser ce paramètre.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indique la durée avant expiration du délai de toutes les fonctions. Dans les plans de consommation serverless, la plage valide est comprise entre 1 seconde et 10 minutes, et la valeur par défaut est de 5 minutes. Dans un plan App Service, il n’existe aucune limite globale et la valeur par défaut dépend de la version du runtime. Dans la version 2.x, la valeur par défaut pour un plan App Service plan est de 30 minutes. Dans la version 1.x, elle prend la valeur *null*, ce qui signifie l’absence de délai d’expiration.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Paramètres de configuration de l’[analyse d’intégrité d’hôtes](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|Activé|true|Indique si la fonctionnalité est activée. | 
|healthCheckInterval|10 secondes|L’intervalle de temps entre les contrôles d’intégrité périodiques en arrière-plan. | 
|healthCheckWindow|2 minutes|Une fenêtre de temps coulissante utilisée conjointement au paramètre `healthCheckThreshold`.| 
|healthCheckThreshold|6.|Nombre maximal de fois où le contrôle d’intégrité peut échouer avant le lancement d’un recyclage de l’hôte.| 
|counterThreshold|0.80|Le seuil auquel un compteur de performance est considéré comme non intègre.| 

## <a name="http"></a>http

Paramètre de configuration pour les [déclencheurs et liaisons http](functions-bindings-http-webhook.md). Dans la version 2.x, il s’agit d’un enfant des [extensions](#extensions).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Version 1.x uniquement.*

ID unique d’un hôte de travail. Il peut s’agir d’un GUID en minuscules dont les tirets ont été supprimés. Requis lors d’une exécution locale. Lors de l’exécution dans Azure, nous vous recommandons de pas définir de valeur d’ID. Un ID est généré automatiquement dans Azure lorsque `id` est omis. Vous ne pouvez pas définir un ID d’application de fonction personnalisé lorsque vous utilisez la version 2.x du runtime.

Si vous partagez un compte de stockage entre plusieurs applications de fonction, assurez-vous que chaque application de fonction a une `id` différente. Vous pouvez omettre la propriété `id` ou définir manuellement chaque `id` de l’application de fonction sur une autre valeur. Le déclencheur du minuteur utilise un verrou de stockage pour vous assurer qu’il n’y aura qu’une seule instance du minuteur lorsqu’une application de fonction augmentera la taille de plusieurs instances. Si deux applications de fonction partagent le même `id` et que chacune utilise un déclencheur de minuteur, un seul minuteur s’exécutera.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

*Version 1.x uniquement ; pour la version 2.x utilisez la [journalisation](#logging).*

Contrôle le filtrage des journaux écrits par un [objet ILogger](functions-monitoring.md#write-logs-in-c-functions) ou par [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|categoryFilter|n/a|Spécifie un filtrage par catégorie.| 
|defaultLevel|Information|Pour toutes les catégories non spécifiées dans le tableau `categoryLevels`, envoie les journaux de ce niveau et des niveaux supérieurs à Application Insights.| 
|categoryLevels|n/a|Tableau des catégories qui spécifie le niveau de journalisation minimal à envoyer à Application Insights pour chaque catégorie. La catégorie indiquée ici contrôle toutes les catégories qui commencent par la même valeur, et les valeurs plus longues sont prioritaires. Dans l’exemple de fichier *host.json* précédent, toutes les catégories qui commencent par « Host.Aggregator » journalisent au niveau `Information`. Toutes les autres catégories commençant par « Host », comme « Host.Executor », journalisent au niveau `Error`.| 

## <a name="logging"></a>journalisation

*Version 2.x uniquement ; pour la version 1.x utilisez l’[enregistreur d’événements](#logger).*

Contrôle les comportements de journalisation de l’application de fonction, y compris Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "applicationInsights": {
        ...
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------|
|fileLoggingMode|information|Envoie les journaux uniquement à ce niveau et aux niveaux supérieurs vers Application Insights. |
|logLevel|n/a|Objet qui définit le filtrage par catégorie du journal pour les fonctions de l’application. La version 2.x suit la disposition d’ASP.NET Core pour le filtrage de catégorie de journal. Cela vous permet de filtrer la journalisation pour des fonctions spécifiques. Pour plus d’informations, consultez [Filtrage de journal](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) dans la documentation ASP.NET Core. |
|applicationInsights|n/a| Le paramètre [applicationInsights](#applicationinsights). |

## <a name="queues"></a>queues

Paramètre de configuration pour les [déclencheurs et liaisons de file d’attente de stockage](functions-bindings-storage-queue.md). Dans la version 2.x, il s’agit d’un enfant des [extensions](#extensions).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

Paramètre de configuration pour les [déclencheurs et liaisons Service Bus](functions-bindings-service-bus.md). Dans la version 2.x, il s’agit d’un enfant des [extensions](#extensions).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Paramètres de configuration du comportement de verrouillage Singleton. Pour plus d’informations, consultez l’article sur le [problème de GitHub relatif à la prise en charge de singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|lockPeriod|00:00:15|Période pendant laquelle des verrous sont établis au niveau des fonctions. Les verrous se renouvellent automatiquement.| 
|listenerLockPeriod|00:01:00|Période pendant laquelle des verrous sont établis au niveau des écouteurs.| 
|listenerLockRecoveryPollingInterval|00:01:00|Intervalle de temps utilisé pour la récupération des verrous d’écouteurs si un verrou de ce type n’a pas pu être acquis au démarrage.| 
|lockAcquisitionTimeout|00:01:00|Période de temps maximale pendant laquelle le runtime essaie d’acquérir un verrou.| 
|lockAcquisitionPollingInterval|n/a|Intervalle écoulé entre les tentatives d’acquisition de verrou.| 

## <a name="tracing"></a>tracing

*Version 1.x*

Paramètres de configuration des journaux que vous créez à l’aide d’un objet `TraceWriter`. Consultez les sections relatives à la [journalisation en C#](functions-reference-csharp.md#logging) et à la [journalisation Node.js](functions-reference-node.md#writing-trace-output-to-the-console). Dans la version 2.x, le comportement de tous les journaux est contrôlé par la [journalisation](#logging).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|consoleLevel|info|Niveau de suivi pour la journalisation de la console. Options : `off`, `error`, `warning`, `info` et `verbose`.|
|fileLoggingMode|debugOnly|Niveau de suivi pour la journalisation des fichiers. Options : `never`, `always`, `debugOnly`.| 

## <a name="version"></a>version

*Version 2.x*

La chaîne de version `"version": "2.0"` est requise pour une application de fonction qui cible le runtime v2.

## <a name="watchdirectories"></a>watchDirectories

Ensemble de [répertoires de code partagé](functions-reference-csharp.md#watched-directories) dont les modifications doivent être surveillées.  Garantit que lorsque le code contenu dans ces répertoires est modifié, les changements sont récupérés par vos fonctions.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment mettre à jour le fichier host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consultez les paramètres globaux des variables d’environnement](functions-app-settings.md)
