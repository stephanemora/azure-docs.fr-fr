---
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104443"
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

|Propriété  |Default | Description |
|---------|---------|---------| 
|maxBatchSize|64|Nombre d’événements maximal reçu par boucle de réception.|
|prefetchCount|n/a|Valeur PrefetchCount par défaut qui est utilisée par l’instance EventProcessorHost sous-jacente.| 
|batchCheckpointFrequency|1|Nombre de lots d’événements à traiter avant de créer un point de contrôle de curseur EventHub.| 

