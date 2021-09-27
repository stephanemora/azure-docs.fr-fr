---
title: fichier descriptif
description: fichier descriptif
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: ba6929739504dfdf0b699e89393018f68751ce07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637613"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Durée du délai d’expiration du conteneur de fonctions 

La durée d’expiration d’une application de fonction est définie par la propriété `functionTimeout` dans le fichier projet [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout). Le tableau suivant présente les valeurs par défaut et les valeurs maximales en minutes pour les deux plans et dans les différentes versions du runtime :

| Plan | Version du runtime | Default | Maximale |
|------|---------|---------|---------|
| Consommation | 1.x | 5 | 10 |
| Consommation | 2.x | 5 | 10 |
| Consommation | 3.x | 5 | 10 |
| Premium | 1.x | Illimité | Illimité |
| Premium | 2.x | 30 | Illimité |
| Premium | 3.x | 30 | Illimité |
| App Service | 1.x | Illimité | Illimité |
| App Service | 2.x | 30 | Illimité |
| App Service | 3.x | 30 | Illimité |

> [!NOTE] 
> Quel que soit le paramètre de délai d’expiration du conteneur de fonctions, 230 secondes est le temps maximum que peut prendre une fonction déclenchée via HTTP pour répondre à une demande. Cela est dû au [délai d’inactivité par défaut d’Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.yml#why-does-my-request-time-out-after-230-seconds-). Pour des temps de traitement plus longs, pensez à utiliser un [modèle asynchrone Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md#async-http) ou [différez le travail actuel et renvoyez une réponse immédiate](../articles/azure-functions/performance-reliability.md#avoid-long-running-functions).
