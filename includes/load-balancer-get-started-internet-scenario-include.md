---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516985"
---
Les tâches suivantes seront effectuées dans ce scénario :

* Créer un équilibrage de charge recevant le trafic réseau sur le port 80 et envoyer un trafic d’équilibrage de charge vers les machines virtuelles « web1 » et « web2 »
* Créer des règles NAT pour l’accès Bureau à distance/SSH pour les machines virtuelles situées derrière l’équilibrage de charge
* Créer des sondes d’intégrité

![Scénario d’équilibreur de charge](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)