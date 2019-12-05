---
title: Informations de référence sur le fichier host.json pour Azure Functions 2.x
description: Documentation de référence pour le fichier host.json d’Azure Functions avec le runtime v2.
ms.topic: conceptual
ms.date: 09/08/2018
ms.openlocfilehash: bb10f15db1d152ff1d8fd8d38ba22e312a2031b7
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323073"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Informations de référence sur le fichier host.json pour Azure Functions 2.x  

> [!div class="op_single_selector" title1="Sélectionnez la version du runtime Azure Functions que vous utilisez : "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

Le fichier de métadonnées *host.json* contient les options de configuration globale qui affectent l’ensemble des fonctions d’une application de fonction. Cet article répertorie les paramètres disponibles pour le runtime v2.  

> [!NOTE]
> Cet article concerne Azure Functions 2.x.  Pour obtenir des informations de référence sur le fichier host.json dans Functions 1.x, consultez [Informations de référence sur le fichier host.json pour Azure Functions 1.x](functions-host-json-v1.md).

Les autres options de configuration d’application de fonction sont managées dans vos [paramètres d’application](functions-app-settings.md).

Certains paramètres host.json sont uniquement utilisés lors de l’exécution locale dans le fichier [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>Exemple de fichier host.json

L’exemple de fichier *host.json* suivant contient toutes les options possibles spécifiées.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20
            }
        }
    },
    "managedDependency": {
        "enabled": true
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

Les sections suivantes de cet article expliquent chaque propriété de niveau supérieur. Toutes les autres propriétés sont facultatives sauf indication contraire.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Ce paramètre est un enfant de la [journalisation](#logging).

Contrôle la [fonctionnalité d’échantillonnage dans Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> L’échantillonnage de journal peut faire que certaines exécutions n’apparaissent pas dans le panneau de surveillance Application Insights.

|Propriété  |Default | Description |
|---------|---------|---------| 
|isEnabled|true|Active ou désactive l’échantillonnage.| 
|maxTelemetryItemsPerSecond|20|Seuil à partir duquel l’échantillonnage débute.| 
|EnableLiveMetrics |true|Active la collecte des métriques temps réel.|
|EnableDependencyTracking|true|Active le suivi des dépendances.|
|EnablePerformanceCountersCollection|true|Active la collecte des compteurs de performances Kudu.|

## <a name="cosmosdb"></a>cosmosDb

Le paramètre de configuration se trouve dans les [déclencheurs et liaisons Cosmos DB](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

Le paramètre de configuration se trouve dans les [liaisons de Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Les paramètres de configuration se trouvent dans les [déclencheurs et liaisons Event Hub](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>extensions

Propriété qui retourne un objet qui contient tous les paramètres spécifiques d’une liaison, tel que [http](#http) et [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Les offres groupées d’extensions vous permettent d’ajouter un jeu d’extensions de liaison Functions compatible avec votre application de fonction. Pour plus d’informations, voir [Offres groupées d’extension pour développement local](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

Liste des fonctions que l’hôte de travail exécute. Un tableau vide désigne l’exécution de toutes les fonctions. Utilisée uniquement pour une [exécution locale](functions-run-local.md). Dans les applications de fonction dans Azure, vous devez plutôt suivre la procédure décrite dans [Guide pratique pour désactiver des fonctions dans Azure Functions](disable-function.md) pour désactiver des fonctions spécifiques au lieu d’utiliser ce paramètre.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indique la durée avant expiration du délai de toutes les fonctions. Il suit le format de chaîne TimeSpan. Dans les plans de consommation serverless, la plage valide est comprise entre 1 seconde et 10 minutes, et la valeur par défaut est de 5 minutes.  

Dans le plan Premium, la plage valide est comprise entre 1 seconde et 60 minutes, et la valeur par défaut est de 30 minutes.

Dans un plan App Service dédié, il n’existe aucune limite globale, et la valeur par défaut est de 30 minutes. La valeur `-1` indique une exécution illimitée, mais il est recommandé de conserver une limite supérieure fixe.

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
|enabled|true|Indique si la fonctionnalité est activée. | 
|healthCheckInterval|10 secondes|L’intervalle de temps entre les contrôles d’intégrité périodiques en arrière-plan. | 
|healthCheckWindow|2 minutes|Une fenêtre de temps coulissante utilisée conjointement au paramètre `healthCheckThreshold`.| 
|healthCheckThreshold|6|Nombre maximal de fois où le contrôle d’intégrité peut échouer avant le lancement d’un recyclage de l’hôte.| 
|counterThreshold|0.80|Le seuil auquel un compteur de performance est considéré comme non intègre.| 

## <a name="http"></a>http

Les paramètres de configuration se trouvent dans les [déclencheurs et liaisons HTTP](functions-bindings-http-webhook.md#hostjson-settings).

## <a name="logging"></a>journalisation

Contrôle les comportements de journalisation de l’application de fonction, y compris Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Définit le niveau de journalisation de fichiers activé.  Options : `never`, `always`, `debugOnly`. |
|logLevel|n/a|Objet qui définit le filtrage par catégorie du journal pour les fonctions de l’application. La version 2.x suit la disposition d’ASP.NET Core pour le filtrage de catégorie de journal. Cela vous permet de filtrer la journalisation pour des fonctions spécifiques. Pour plus d’informations, consultez [Filtrage de journal](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) dans la documentation ASP.NET Core. |
|console|n/a| Le paramètre de journalisation de la [console](#console). |
|applicationInsights|n/a| Le paramètre [applicationInsights](#applicationinsights). |

## <a name="console"></a>console

Ce paramètre est un enfant de la [journalisation](#logging). Il contrôle la journalisation de la console lorsque le mode débogage n’est pas activé.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|isEnabled|false|Active ou désactive la journalisation de la console.| 

## <a name="manageddependency"></a>managedDependency

La dépendance managée est une fonctionnalité qui est actuellement prise en charge uniquement par les fonctions basées sur PowerShell. Elle permet au service de gérer automatiquement les dépendances. Lorsque la propriété `enabled` est définie sur `true`, le fichier `requirements.psd1` est traité. Les dépendances sont mises à jour lorsque des versions mineures sont publiées. Pour plus d’informations, consultez la section [Dépendance managée](functions-reference-powershell.md#dependency-management) de l’article PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

Les paramètres de configuration se trouvent dans les [déclencheurs et liaisons de la file d'attente de stockage](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Le paramètre de configuration se trouve dans les [déclencheurs et liaisons SendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Le paramètre de configuration se trouve dans les [déclencheurs et liaisons Service Bus](functions-bindings-service-bus.md#host-json).

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

## <a name="version"></a>version

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
