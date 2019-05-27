---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66122102"
---
Un équilibrage de charge Azure est de type Couche 4 (TCP, UDP). L’équilibrage de charge offre une disponibilité élevée en distribuant le trafic entrant parmi les instances de service saines dans les services cloud ou les machines virtuelles dans un jeu d’équilibrage de la charge. Azure Load Balancer peut également présenter ces services sur plusieurs ports, plusieurs adresses IP ou les deux.

Vous pouvez configurer un équilibrage de charge pour :

* équilibrer la charge du trafic Internet entrant sur les machines virtuelles. L’équilibrage de charge auquel nous faisons référence dans ce scénario est un [équilibrage de charge accessible sur Internet](../articles/load-balancer/load-balancer-internet-overview.md).
* Il équilibre le trafic entre des machines virtuelles dans un réseau virtuel (VNet), entre des machines virtuelles dans les services cloud ou entre des ordinateurs locaux et des machines virtuelles dans un réseau virtuel entre différents locaux. L’équilibreur de charge auquel nous faisons référence dans ce scénario est un [Équilibreur de charge interne (ILB)](../articles/load-balancer/load-balancer-internal-overview.md);
* transférer du trafic externe vers une instance spécifique de machine virtuelle.
