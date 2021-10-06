---
title: Tableau des limites d’Azure SignalR Service
description: Décrit les limites du système pour le service Azure SignalR.
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/11/2020
ms.author: zhshang
ms.openlocfilehash: f5b197448529e953d5d7d188b00a88d60e87e900
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123453774"
---
| Ressource | Limite par défaut | Limite maximale | 
| --- | --- | --- |
| Unités du service Azure SignalR par instance pour le niveau gratuit |1 |1 |
| Unités du service Azure SignalR par instance pour le niveau Standard |100 |100 |
| Unités du service Azure SignalR par abonnement par région pour le niveau gratuit|5 |5 |
| Total des unités de service Azure SignalR par abonnement par région |150 |Illimité |
| Connexions par unité par jour pour le niveau gratuit |20 |20 |
| Connexions par unité par jour pour le niveau Standard |1 000 |1 000|
| Messages inclus par unité par jour pour le niveau gratuit|20 000 |20 000 |
| Messages supplémentaires par unité et par jour pour le niveau Gratuit|0 |0 |
| Messages inclus par unité par jour pour le niveau Standard|1 000 000 |1 000 000 |
| Messages supplémentaires par unité et par jour pour le niveau Standard|Illimité |Illimité |

Pour demander une mise à jour des limites par défaut de votre abonnement, ouvrez un ticket de support.

Pour obtenir davantage d’informations sur la comptabilisation des connexions et des messages, consultez [Messages et connexions dans Azure SignalR Service](../articles/azure-signalr/signalr-concept-messages-and-connections.md).

Si vos besoins dépassent les limites, passez du niveau Gratuit au niveau Standard et ajoutez des unités. Pour plus d’informations, consultez [Comment mettre à l’échelle une instance d’Azure SignalR Service ?](../articles/azure-signalr/signalr-howto-scale-signalr.md). 

Si vos besoins dépassent les limites d’une seule instance, ajoutez des instances. Pour plus d’informations, consultez [Comment mettre à l’échelle SignalR Service avec plusieurs instances ?](../articles/azure-signalr/signalr-howto-scale-multi-instances.md).
