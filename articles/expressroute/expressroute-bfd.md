---
title: 'Azure ExpressRoute : Configurer BFD'
description: Cet article fournit des instructions sur la configuration de BFD (Bidirectional Forwarding Detection) via un appairage privé d’un circuit ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 378b639e89ffd46f6b32d7004f934104dd4b5407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064839"
---
# <a name="configure-bfd-over-expressroute"></a>Configurer BFD sur ExpressRoute

ExpressRoute prend en charge la détection de transfert bidirectionnel (BFD, Bidirectional Forwarding Detection) sur le peering privé et le peering Microsoft. En activant BFD via ExpressRoute, vous pouvez accélérer la détection des défaillances des liaisons entre les appareils MSEE (Microsoft Enterprise Edge) et les routeurs sur lesquels vous terminez le circuit ExpressRoute (CE/PE). Vous pouvez établir une terminaison d’ExpressRoute sur des appareils de routage de périphérie du client ou des appareils de routage de périphérie de partenaire (si vous avez utilisé le service de connexion de couche 3 managé). Ce document explique pourquoi BFD peut être nécessaire et comment activer BFD sur ExpressRoute.

## <a name="need-for-bfd"></a>Besoin de BFD

Le diagramme suivant présente les avantages de l’activation de BFD sur le circuit ExpressRoute : [![1]][1]

Vous pouvez activer le circuit ExpressRoute par des connexions de couche 2 ou par des connexions de couche 3 managées. Dans les deux cas, s’il existe un ou plusieurs appareils de couche 2 dans le chemin de connexion ExpressRoute, la responsabilité de la détection des échecs des liaisons dans le chemin incombe au protocole BGP superposé.

Sur les appareils MSEE, les durées de conservation et de mise en suspens de BGP sont généralement configurées respectivement sur 60 et 180 secondes. Par conséquent, après un échec de liaison, jusqu’à 3 minutes seraient nécessaires pour détecter cet échec et basculer le trafic vers une autre connexion.

Vous pouvez contrôler les minuteurs BGP en configurant des durées de conservation et de mise en suspens de BGP avec des valeurs inférieures sur l’appareil de peering de périphérie du client. Si les minuteurs BGP ne correspondent pas entre les deux appareils du peering, la session BGP entre les pairs utilise la valeur des minuteurs la plus basse. La durée de conservation de BGP peut être définie avec une valeur minimale de 3 secondes, et la durée de mise en suspens avec une valeur de l’ordre de quelques dizaines de secondes. Cependant, la définition de valeurs agressives pour les minuteurs BGP est moins préférable, car le protocole est basé sur un processus consommant des ressources de façon intensive.

Dans ce scénario, BFD peut être bénéfique. BFD assure la détection de la défaillance des liaisons en consommant peu de ressources et dans un intervalle de temps inférieur à la seconde. 


## <a name="enabling-bfd"></a>Activation de BFD

BFD est configuré par défaut sous toutes les interfaces de peering privé ExpressRoute nouvellement créées sur les appareils MSEE. Ainsi, pour activer BFD, vous devez simplement configurer BFD sur vos routeurs CE/PE (sur vos appareils principaux et secondaires). La configuration de BFD est un processus en deux étapes : vous devez configurer BFD sur l’interface, puis la lier à la session BGP.

Voici un exemple de configuration de CE/PE (avec Cisco IOS XE). 

    interface TenGigabitEthernet2/0/0.150
       description private peering to Azure
       encapsulation dot1Q 15 second-dot1q 150
       ip vrf forwarding 15
       ip address 192.168.15.17 255.255.255.252
       bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
       address-family ipv4 vrf 15
          network 10.1.15.0 mask 255.255.255.128
          neighbor 192.168.15.18 remote-as 12076
          neighbor 192.168.15.18 fall-over bfd
          neighbor 192.168.15.18 activate
          neighbor 192.168.15.18 soft-reconfiguration inbound
       exit-address-family

>[!NOTE]
>Pour activer BFD sous un peering privé déjà existant, vous devez réinitialiser le peering. Consultez [Réinitialiser les peerings ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Négociation des minuteurs BFD

Entre les pairs BFD, le plus lent des deux pairs détermine la vitesse de transmission. Les intervalles de transmission/réception BFD des appareils MSEE sont définis sur 300 millisecondes. Dans certains scénarios, l'intervalle peut être défini sur une valeur supérieure de 750 millisecondes. En configurant des valeurs plus élevées, vous pouvez forcer ces intervalles à être plus longs, mais pas plus courts.

>[!NOTE]
>Si vous avez configuré des circuits ExpressRoute géoredondants ou que vous utilisez une connectivité de VPN IPSec de site à site comme solution de secours, l’activation de BFD aide à accélérer la bascule sur l’alternative de secours après une défaillance de la connectivité ExpressRoute. 
>

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations ou d'aide, consultez les liens suivants :

- [Création et modification d’un circuit ExpressRoute][CreateCircuit]
- [Créer et modifier le routage pour un circuit ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD accélère les temps de détection des défaillances des liaisons"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






