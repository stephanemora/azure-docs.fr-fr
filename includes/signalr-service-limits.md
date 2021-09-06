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
ms.openlocfilehash: 328a3d088ea21ec55f5e06cfbfc307fca2b03afa
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2021
ms.locfileid: "113644368"
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
