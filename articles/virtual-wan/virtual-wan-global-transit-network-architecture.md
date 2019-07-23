---
title: Architecture du réseau de transit global Azure Virtual WAN | Microsoft Docs
description: En savoir plus sur l’architecture du réseau de transit global pour Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 114d11f98c6181a03f5ce52527b5e2efea468c42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65965970"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Architecture du réseau de transit global Virtual WAN

L’architecture du réseau de transit global est adoptée par les entreprises pour consolider, connecter et contrôler l’empreinte informatique de l’entreprise moderne tournée vers le cloud. Dans une entreprise moderne centrée sur le cloud, le trafic réseau n’a pas besoin d’être renvoyé au siège social. L’architecture du réseau de transit global est basée sur des concepts de mise en réseau familiers et de nouveaux concepts qui sont propres au cloud et aux architectures basées sur le cloud.

![architecture](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Figure 1 : Réseau de transit global avec Virtual WAN**

Les entreprises modernes ont besoin d’une connectivité omniprésente entre les applications hyper-distribuées, les données et les utilisateurs dans le cloud et en local. Azure Virtual WAN permet une architecture du réseau de transit global en fournissant une connectivité omniprésente, universelle, entre des ensembles de VNets, sites, applications et utilisateurs distribués mondialement. Azure Virtual WAN est un service managé par Microsoft. Tous les composants réseau qui composent ce service sont hébergés et managés par Microsoft. Pour plus d’informations sur Azure Virtual WAN, consultez [Vue d’ensemble d’Azure Virtual WAN](virtual-wan-about.md).

Dans l’architecture Virtual WAN d’Azure, les régions d’Azure servent de hubs auxquels vous pouvez choisir de connecter vos branches. Une fois les branches connectées, vous pouvez utiliser la dorsale principale d’Azure pour établir une connectivité de branche à réseau et, éventuellement, de branche à branche.

Vous pouvez établir un WAN virtuel en créant un seul hub Virtual WAN dans la région qui possède le plus grand nombre de rayons (branches, VNets, utilisateurs), puis en connectant au hub les rayons qui sont dans d’autres régions. Si les rayons sont géographiquement distribués, vous pouvez aussi instancier des hubs régionaux et interconnecter les hubs. Les hubs font tous partie du même réseau WAN virtuel, mais ils peuvent être associés à des politiques régionales différentes.

## <a name="hub"></a>Transit de réseau en étoile

L’architecture du réseau de transit global est basée sur un modèle classique de connectivité en étoile où le « hub » du réseau hébergé sur le cloud permet une connectivité transitive entre les terminaux qui peut être répartie sur différents types de « rayons ».
  
Dans ce modèle, un rayon peut être :

* Un réseau virtuel (VPN)
* Site de branche physique
* Utilisateur distant
* Internet

![diagramme de transit global en étoile](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Figure 2 : Réseau en étoile**

La Figure 2 montre la vue logique du réseau global où les utilisateurs géographiquement répartis, les sites physiques et les VNets sont interconnectés via un hub réseau hébergé dans le cloud. Cette architecture permet une connectivité de transit à un seul tronçon logique entre les points de terminaison du réseau. Les rayons sont connectés au hub par différents services réseau Azure comme ExpressRoute ou un VPN de site à site pour les branches physiques, le VNet Peering pour les réseaux virtuels et les VPN de point à site pour les utilisateurs distants.

## <a name="crossregion"></a>Connectivité inter-régions

Pour une entreprise, une empreinte cloud suit généralement une empreinte physique. La plupart des entreprises accèdent au cloud à partir d’une région proche de leur site physique et des utilisateurs. Un des principes clés de l’architecture du réseau global consiste à activer la connectivité inter-régions entre des entités réseau et des points de terminaison. Une empreinte cloud peut s’étendre sur plusieurs régions. Cela signifie que le trafic à partir d’une branche connectée au cloud dans une région peut atteindre une autre branche ou un réseau virtuel dans une autre région à l’aide de la connectivité de hub à hub qui est actuellement en préversion.

## <a name="any"></a>Connexion universelle

L’architecture du réseau de transit global permet une *connectivité universelle* via un hub réseau central. Cette architecture élimine ou réduit le besoin de maillage complet ou maillage partiel des modèles de connectivité qui sont plus complexes à créer et gérer. En outre, comparativement aux réseaux maillés, le contrôle du routage dans le réseau en étoile est plus facile à configurer et gérer.

La connectivité universelle, dans le contexte d’une architecture globale, permet à permet à une entreprise dont les utilisateurs, les branches, les centres de données, les réseaux virtuels et les applications sont répartis partout dans le monde, de les connecter entre eux via son hub de transit. Le hub de transit agit en tant que le système de transit global.

![chemins d’accès du trafic](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Figure 3 : Chemins d’accès du trafic Virtual WAN**

Azure Virtual WAN prend en charge les chemins d’accès de connectivité de transit global suivants : Les lettres entre parenthèses renvoient à la Figure 3.

* Branche à réseau virtuel (a)  
* Branche à branche (b)
* Utilisateur distant à réseau virtuel (c)
* Utilisateur distant à branche (d)
* Réseau virtuel à réseau virtuel à l’aide du VNet Peering (e)
* ExpressRoute Global Reach 

### <a name="branchvnet"></a>Branche à réseau virtuel

Branche à réseau virtuel est le principal chemin d’accès pris en charge par Azure Virtual WAN. Ce chemin d’accès vous permet de connecter des branches aux charges de travail d’entreprise Azure IAAS déployées dans Azure VNets. Les branches peuvent être connectées au WAN virtuel via ExpressRoute ou un VPN site à site. Le trafic transite vers des réseaux virtuels connectés aux hubs Virtual WAN via des connexions de réseau virtuel.

### <a name="branchbranch"></a>Branche à branche

Les branches peuvent être connectées à un hub Azure Virtual WAN à l’aide de circuits ExpressRoute et/ou des connexions VPN de site à site. Vous pouvez connecter les branches au hub Virtual WAN qui se trouve dans la région la plus proche de la branche.

Cette option permet aux entreprises de tirer parti de la dorsale principale d’Azure pour connecter des branches. Toutefois, même si cette fonctionnalité est disponible, vous devez comparer les avantages de la connexion des branches via Azure Virtual WAN à l’utilisation d’un réseau WAN privé.

### <a name="usertovnet"></a>Utilisateur distant à réseau virtuel

Vous pouvez activer l’accès à distance direct et sécurisé à Azure à l’aide de connexions point à site depuis un client utilisateur distant vers un réseau WAN virtuel. Les utilisateurs distants d’entreprise n’ont plus besoin d’accéder au cloud en utilisant un VPN d’entreprise.

### <a name="usertobranch"></a>Utilisateur distant à branche

Le chemin d’accès utilisateur distant à branche permet aux utilisateurs distants qui utilisent une connexion point à site sur Azure d’accéder aux charges de travail locales et aux applications en transit à travers le cloud. Ce chemin d’accès offre aux utilisateurs distants la flexibilité d’accéder aux charges de travail qui sont déployées à la fois dans Azure et en local. Les entreprises peuvent activer un service central d’accès distant sécurisé basé sur le cloud dans Azure Virtual WAN.

### <a name="vnetvnet"></a>Transit de réseau virtuel à réseau virtuel à l’aide du VNet Peering

Pour connecter des réseaux virtuels les uns aux autres afin de prendre en charge les applications multiniveau implémentées sur plusieurs réseaux virtuels, utilisez le VNet Peering. Un scénario de transit de réseau virtuel à réseau virtuel via Azure Virtual WAN n’est actuellement pas pris en charge, mais est intégré à la feuille de route Azure. Si vous devez connecter vos réseaux virtuels les uns aux autres, nous vous recommandons d’opter pour une connexion par VNet Peering. Le [transit par passerelle](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) (dans le contexte du VNet Peering) n’est pas requis pour Virtual WAN, car ce dernier active automatiquement le transit par passerelle.

### <a name="globalreach"></a>ExpressRoute Global Reach

ExpressRoute vous permet de connecter vos réseaux locaux à Microsoft Cloud de manière privée et résiliente. ExpressRoute Global Reach est une fonctionnalité de module complémentaire pour ExpressRoute. Avec Global Reach, vous pouvez associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux locaux. Les branches connectées à Azure Virtual WAN à l’aide d’ExpressRoute nécessitent ExpressRoute Global Reach pour communiquer entre elles.

Dans ce modèle, chaque branche connectée au hub Virtual WAN à l’aide d’ExpressRoute peut se connecter à des réseaux virtuels utilisant le chemin d’accès « Branche à réseau virtuel ». Le trafic de branche à branche ne transite pas par le hub, car ExpressRoute Global Reach offre un meilleur chemin d’accès sur Azure WAN.

## <a name="security"></a>Contrôle de la sécurité et de la stratégie

Le hub du réseau virtuel interconnecte et voit potentiellement tout le trafic de transit. Il peut être l’endroit idéal pour héberger des fonctions et des services réseau centraux tels que le contrôle du routage cloud, de la stratégie et de la sécurité réseau et de l’accès à Internet.

## <a name="next-steps"></a>Étapes suivantes

Créer une connexion à l’aide de Virtual WAN.

* [Connexions de site à site à l’aide de Virtual WAN](virtual-wan-site-to-site-portal.md)
* [Connexions de point à site à l’aide de Virtual WAN](virtual-wan-point-to-site-portal.md)
* [Connexions ExpressRoute à l’aide du Virtual WAN](virtual-wan-expressroute-portal.md)
