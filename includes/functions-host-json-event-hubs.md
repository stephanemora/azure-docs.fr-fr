---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131699"
---
```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Propriété  |Default | Description  |
|---------|---------|---------| 
|maxBatchSize|64|Nombre d’événements maximal reçu par boucle de réception.|
|prefetchCount|n/a|Valeur PrefetchCount par défaut qui est utilisée par l’instance EventProcessorHost sous-jacente.| 
|batchCheckpointFrequency|1|Nombre de lots d’événements à traiter avant de créer un point de contrôle de curseur EventHub.| 
