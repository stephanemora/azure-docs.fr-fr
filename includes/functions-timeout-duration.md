---
title: Fichier Include
description: Fichier Include
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: 3501ff3f92ae045019df2766bbcf7fc2c3fec5b0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768914"
---
## <a name="timeout"></a>Durée du délai d’expiration du conteneur de fonctions 

La durée d’expiration d’une application de fonction est définie par la propriété `functionTimeout` dans le fichier projet [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout). Le tableau suivant présente les valeurs par défaut et les valeurs maximales en minutes pour les deux plans et dans les différentes versions du runtime :

| Plan | Version du runtime | Default | Maximale |
|------|---------|---------|---------|
| Consommation | 1.x | 5 | 10 |
| Consommation | 2.x | 5 | 10 |
| Consommation | 3.x | 5 | 10 |
| App Service | 1.x | Illimité | Illimité |
| App Service | 2.x | 30 | Illimité |
| App Service | 3.x | 30 | Illimité |

> [!NOTE] 
> Quel que soit le paramètre de délai d’expiration du conteneur de fonctions, 230 secondes est le temps maximum que peut prendre une fonction déclenchée via HTTP pour répondre à une demande. Cela est dû au [délai d’inactivité par défaut d’Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Pour des temps de traitement plus longs, pensez à utiliser un [modèle asynchrone Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md#async-http) ou [différez le travail actuel et renvoyez une réponse immédiate](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
