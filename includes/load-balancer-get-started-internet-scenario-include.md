---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66122185"
---
Les tâches suivantes seront effectuées dans ce scénario :

* Créer un équilibrage de charge recevant le trafic réseau sur le port 80 et envoyer un trafic d’équilibrage de charge vers les machines virtuelles « web1 » et « web2 »
* Créer des règles NAT pour l’accès Bureau à distance/SSH pour les machines virtuelles situées derrière l’équilibrage de charge
* Créer des sondes d’intégrité

![Scénario d’équilibreur de charge](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
