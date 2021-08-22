---
title: Fichier Include
description: inclure fichier
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: f4305152e9aec28b7a6b6307815de616abd4dfbd
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113131931"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Durée du délai d’expiration du conteneur de fonctions 

La durée d’expiration d’une application de fonction est définie par la propriété `functionTimeout` dans le fichier projet [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout). Le tableau suivant présente les valeurs par défaut et les valeurs maximales en minutes pour les deux plans et dans les différentes versions du runtime :

| Planifier | Version du runtime | Valeur par défaut | Maximale |
|------|---------|---------|---------|
| Consommation | 1.x | 5 | 10 |
| Consommation | 2.x | 5 | 10 |
| Consommation | 3.x | 5 | 10 |
| Premium | 1.x | Illimité | Illimité |
| Premium | 2.x | 30 | illimitées |
| Premium | 3.x | 30 | illimitées |
| App Service | 1.x | Illimité | Illimité |
| App Service | 2.x | 30 | illimitées |
| App Service | 3.x | 30 | illimitées |

> [!NOTE] 
> Quel que soit le paramètre de délai d’expiration du conteneur de fonctions, 230 secondes est le temps maximum que peut prendre une fonction déclenchée via HTTP pour répondre à une demande. Cela est dû au [délai d’inactivité par défaut d’Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.yml#why-does-my-request-time-out-after-230-seconds-). Pour des temps de traitement plus longs, pensez à utiliser un [modèle asynchrone Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md#async-http) ou [différez le travail actuel et renvoyez une réponse immédiate](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
