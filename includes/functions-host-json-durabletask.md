---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a3af711503445000d9613feb2eec7967442fe538
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736041"
---
Paramètres de configuration de [Fonctions durables](../articles/azure-functions/durable-functions-overview.md).

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

Les noms de hubs de tâches doivent commencer par une lettre et contenir uniquement des lettres et des chiffres. S’il n’est pas spécifié, le nom du hub de tâches par défaut d’une application de fonction est **DurableFunctionsHub**. Pour en savoir plus, consultez la section relative aux [hubs de tâches](../articles/azure-functions/durable-functions-task-hubs.md).

|Propriété  |Default | Description |
|---------|---------|---------|
|HubName|DurableFunctionsHub|D'autres noms de [hub de tâches](../articles/azure-functions/durable-functions-task-hubs.md) peuvent être utilisés pour isoler plusieurs applications Durable Functions les unes des autres, même si elles s'appuient sur le même principal de stockage.|
|ControlQueueBatchSize|32|Nombre de messages à extraire de la file d’attente de contrôle en une seule fois.|
|PartitionCount |4|Nombre de partitions pour la file d’attente de contrôle. Doit être un entier positif compris entre 1 et 16.|
|ControlQueueVisibilityTimeout |5 minutes|Délai d’expiration de la visibilité des messages supprimés de la file d’attente de contrôle.|
|WorkItemQueueVisibilityTimeout |5 minutes|Délai d’expiration de la visibilité des messages supprimés de la file d’attente des éléments de travail.|
|MaxConcurrentActivityFunctions |10 fois le nombre de processeurs sur l’ordinateur actuel|Nombre maximal de fonctions d’activité pouvant être traitées simultanément sur une seule instance d’hôte.|
|MaxConcurrentOrchestratorFunctions |10 fois le nombre de processeurs sur l’ordinateur actuel|Nombre maximal de fonctions d’orchestrateur pouvant être traitées simultanément sur une seule instance d’hôte.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Nom du paramètre d’application qui contient la chaîne de connexion de stockage Azure utilisée pour gérer les ressources de stockage Azure sous-jacentes.|
|TraceInputsAndOutputs |false|Valeur indiquant s’il faut tracer les entrées et sorties des appels de fonction. Lors du traçage d’événements d’exécution de fonctions, le nombre d’octets est inclus par défaut dans les entrées et les sorties sérialisées pour les appels de fonction. Cela fournit un minimum d’informations sur l’apparence des entrées et sorties sans encombrer les journaux ou exposer accidentellement des informations sensibles dans les journaux. La définition de cette propriété sur True amène la journalisation de la fonction par défaut à consigner la totalité du contenu des entrées et sorties de fonction.|
|LogReplayEvents|false|Valeur indiquant s’il faut écrire les événements de relecture d’orchestration à Application Insights.|
|EventGridTopicEndpoint ||URL du point de terminaison de la rubrique personnalisée Azure Event Grid. Lorsque cette propriété est définie, les événements de notification du cycle de vie d’orchestration sont publiés sur ce point de terminaison. Cette propriété prend en charge la résolution de paramètres de l’application.|
|EventGridKeySettingName ||Nom du paramètre d’application contenant la clé utilisée pour l’authentification à l’aide de la rubrique personnalisée Azure Event Grid sur `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Nombre de nouvelles tentatives à effectuer en cas d’échec de la publication de la rubrique Event Grid.|
|EventGridPublishRetryInterval|5 minutes|L’intervalle avant nouvelle tentative de publication Event Grid au format *hh:mm:ss*.|

Beaucoup sont destinés à l’optimisation des performances. Pour plus d’informations, consultez [Performances et mise à l’échelle](../articles/azure-functions/durable-functions-perf-and-scale.md).