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
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131344"
---
## <a name="timeout"></a>Durée de délai d’attente d’application (fonction) 

La durée d’expiration d’une application de fonction est définie par la propriété functionTimeout dans le [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) fichier projet. Le tableau suivant indique les valeurs par défaut et maximale en minutes pour les plans et dans les deux versions du runtime :

| Planification | Version du runtime | Default | Maximale |
|------|---------|---------|---------|
| Consommation | 1.x | 5. | 10 |
| Consommation | 2.x | 5. | 10 |
| App Service | 1.x | Illimité | Illimité |
| App Service | 2.x | 30 | Illimité |

> [!NOTE] 
> Quel que soit le paramètre de délai d’attente d’application fonction 230 secondes est la quantité maximale pendant laquelle une fonction déclenchée via HTTP peut effectuer pour répondre à une demande. Cela est dû au fait le [par défaut du délai d’inactivité de l’équilibreur de charge Azure](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Pour les temps de traitement, envisagez d’utiliser le [modèle asynchrone de fonctions durables](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) ou [différer le travail réel et de renvoyer une réponse immédiate](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
