---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131677"
---
Paramètres de configuration de [Fonctions durables](../articles/azure-functions/durable-functions-overview.md).

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

Les noms de hubs de tâches doivent commencer par une lettre et contenir uniquement des lettres et des chiffres. S’il n’est pas spécifié, le nom du hub de tâches par défaut d’une application de fonction est **DurableFunctionsHub**. Pour en savoir plus, consultez la section relative aux [hubs de tâches](../articles/azure-functions/durable-functions-task-hubs.md).

|Propriété  |Default | Description  |
|---------|---------|---------|
|hubName|DurableFunctionsHub|D'autres noms de [hub de tâches](../articles/azure-functions/durable-functions-task-hubs.md) peuvent être utilisés pour isoler plusieurs applications Durable Functions les unes des autres, même si elles s'appuient sur le même principal de stockage.|
|controlQueueBatchSize|32|Nombre de messages à extraire de la file d’attente de contrôle en une seule fois.|
|partitionCount |4|Nombre de partitions pour la file d’attente de contrôle. Doit être un entier positif compris entre 1 et 16.|
|controlQueueVisibilityTimeout |5 minutes|Délai d’expiration de la visibilité des messages supprimés de la file d’attente de contrôle.|
|workItemQueueVisibilityTimeout |5 minutes|Délai d’expiration de la visibilité des messages supprimés de la file d’attente des éléments de travail.|
|maxConcurrentActivityFunctions |10 fois le nombre de processeurs sur l’ordinateur actuel|Nombre maximal de fonctions d’activité pouvant être traitées simultanément sur une seule instance d’hôte.|
|maxConcurrentOrchestratorFunctions |10 fois le nombre de processeurs sur l’ordinateur actuel|Nombre maximal de fonctions d’orchestrateur pouvant être traitées simultanément sur une seule instance d’hôte.|
|maxQueuePollingInterval|30 secondes|Le contrôle maximal et l’intervalle d’interrogation de file d’attente de l’élément de travail dans le *hh : mm :* format. Latences de traitement des messages plus élevé peut entraîner la hausse. Des valeurs plus faibles peuvent entraîner des coûts de stockage plus élevés en raison de transactions de stockage.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Nom du paramètre d’application qui contient la chaîne de connexion de stockage Azure utilisée pour gérer les ressources de stockage Azure sous-jacentes.|
|trackingStoreConnectionStringName||Le nom d’une chaîne de connexion à utiliser pour les tables d’historique et les Instances. Si non spécifié, la `azureStorageConnectionStringName` connexion est utilisée.|
|trackingStoreNamePrefix||Le préfixe à utiliser pour l’historique et les Instances des tables quand `trackingStoreConnectionStringName` est spécifié. Si non définie, la valeur de préfixe par défaut seront `DurableTask`. Si `trackingStoreConnectionStringName` n’est pas spécifié, les tables d’historique et les Instances utilisent la `hubName` valeur, ainsi que leur préfixe tout paramètre pour `trackingStoreNamePrefix` sera ignoré.|
|traceInputsAndOutputs |false|Valeur indiquant s’il faut tracer les entrées et sorties des appels de fonction. Lors du traçage d’événements d’exécution de fonctions, le nombre d’octets est inclus par défaut dans les entrées et les sorties sérialisées pour les appels de fonction. Ce comportement fournit des informations minimales sur les entrées et sorties aspect sans encombrer les journaux ou exposer accidentellement des informations sensibles. La définition de cette propriété sur True amène la journalisation de la fonction par défaut à consigner la totalité du contenu des entrées et sorties de fonction.|
|logReplayEvents|false|Valeur indiquant s’il faut écrire les événements de relecture d’orchestration à Application Insights.|
|eventGridTopicEndpoint ||URL du point de terminaison de la rubrique personnalisée Azure Event Grid. Lorsque cette propriété est définie, les événements de notification de cycle de vie d’orchestration sont publiées dans ce point de terminaison. Cette propriété prend en charge la résolution de paramètres de l’application.|
|eventGridKeySettingName ||Nom du paramètre d’application contenant la clé utilisée pour l’authentification à l’aide de la rubrique personnalisée Azure Event Grid sur `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Nombre de nouvelles tentatives à effectuer en cas d’échec de la publication de la rubrique Event Grid.|
|eventGridPublishRetryInterval|5 minutes|L’intervalle avant nouvelle tentative de publication Event Grid au format *hh:mm:ss*.|
|eventGridPublishEventTypes||Une liste de types d’événements pour publier dans Event Grid. Si non spécifié, tous les types d’événements seront publiées. Autorisées incluent des valeurs `Started`, `Completed`, `Failed`, `Terminated`.|

La plupart de ces paramètres sont pour optimiser les performances. Pour plus d’informations, consultez [Performances et mise à l’échelle](../articles/azure-functions/durable-functions-perf-and-scale.md).