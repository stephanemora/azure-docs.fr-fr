---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0a3f739bae3ec758c8e25d581d3e2b9feb4af5b1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134045"
---
```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|maxConcurrentCalls|16|Nombre maximal d’appels simultanés pour le rappel que la pompe de messages doit initier. Par défaut, le runtime Functions traite plusieurs messages simultanément. Pour que le runtime ne traite qu’un message de file d’attente ou de rubrique à la fois, définissez `maxConcurrentCalls` sur 1. | 
|prefetchCount|n/a|Valeur PrefetchCount par défaut qui est utilisée par l’instance MessageReceiver sous-jacente.| 
|autoRenewTimeout|00:05:00|Durée maximale pendant laquelle le verrouillage de message doit être renouvelé automatiquement.| 
