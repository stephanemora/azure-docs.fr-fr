---
title: Architecture de réseau de transit global WAN virtuel Azure | Microsoft Docs
description: En savoir plus sur l’architecture de réseau global de transit pour le WAN virtuel
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/06/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 8cda617ca60a17fceaaa818480ff9bbaef46c3fd
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414066"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Architecture de réseau global de transit et WAN virtuel

Architecture de réseau global de transit est adoptée par les entreprises à consolider, connectez et contrôlez l’entreprise moderne orientée cloud empreinte informatique. Dans une entreprise moderne orientée sur le cloud, le trafic réseau n’a pas besoin être backhauled au siège social. Architecture de réseau global de transit est basé sur les concepts de mise en réseau familiers et de nouveaux concepts qui sont propres au cloud et les architectures basées sur le cloud.

![architecture](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Figure 1 : Réseau de transit global avec WAN virtuel**

Les entreprises modernes nécessitent une connectivité omniprésente entre hyper distributed applications, données et les utilisateurs sur le cloud et locales. WAN virtuel Azure permet une architecture de réseau global de transit en permettant la connectivité omniprésente, à tout entre des jeux distribués dans le monde entier de réseaux virtuels, sites, applications et utilisateurs. WAN virtuel Azure est un service géré par Microsoft. Tous les composants de mise en réseau que ce service est composé de sont hébergées et gérées par Microsoft. Pour plus d’informations sur le WAN virtuel, consultez le [présentation du WAN virtuel](virtual-wan-about.md) article.

Dans l’architecture WAN virtuel Azure, des régions Azure servent de concentrateurs à laquelle vous pouvez choisir de connecter vos branches. Une fois que les branches sont connectés, vous pouvez tirer parti à la dorsale principale d’Azure pour établir une branche à réseau virtuel et, éventuellement, connectivité de succursale à succursale.

Vous pouvez établir un WAN virtuel en créant un seul hub WAN virtuel dans la région qui a le plus grand nombre de rayons (branches, réseaux virtuels, les utilisateurs), puis en connectant les rayons situés dans d’autres régions au hub. Si rayons (spokes) est répartis géographiquement, vous pouvez également instancier des hubs régionaux et les hubs d’interconnexion. Les hubs font tous partie de la même WAN virtuel, mais ils peuvent être associées aux stratégies régionaux différents.

## <a name="hub"></a>Transit de hub-and-spoke

L’architecture de réseau global de transit est basé sur un modèle classique connectivité hub-and-spoke où le réseau de nuage hébergé « hub » permet une connectivité transitive entre les points de terminaison qui peut être répartie sur différents types de « spokes ».
  
Dans ce modèle, un rayon peut être :

* Réseau virtuel (Vnet)
* Site de succursale physique
* Utilisateur distant
* Internet

![diagramme de transit global hub- and -spoke](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Figure 2 : Hub-and-spoke**

Figure 2 montre la vue logique du réseau global dans lequel les utilisateurs géographiquement dispersés, des sites physiques et des réseaux virtuels sont interconnectés via un concentrateur réseau hébergé dans le cloud. Cette architecture permet une connectivité de transit d’un saut logiques entre les points de terminaison de mise en réseau. Les rayons sont connectés au concentrateur par différents services réseau Azure comme ExpressRoute ou site à site-VPN pour les succursales physiques, homologation de réseaux virtuels et des VPN de point-to-site pour les utilisateurs distants.

## <a name="crossregion"></a>Connectivité dans plusieurs régions

Pour une entreprise, un encombrement du cloud suit généralement l’encombrement physique. La plupart des entreprises accéder au cloud à partir d’une région proche de leur site physique et les utilisateurs. Un des principaux de clé de l’architecture de réseau global consiste à activer la connectivité entre les régions entre des entités réseau et des points de terminaison. Un encombrement du cloud peut s’étendre sur plusieurs régions. Cela signifie que le trafic à partir d’une branche qui est connecté au cloud dans une région peut atteindre une autre branche ou un réseau virtuel dans une autre région.

## <a name="any"></a>Connectivité à tout

Architecture de réseau de transit global permet *any-connectivité* via un concentrateur réseau central. Cette architecture élimine ou réduit le besoin de maillage complet ou maillage partiel des modèles de connectivité qui sont plus complexes pour créer et gérer. En outre, le contrôle du routage dans le hub-and-spoke et réseaux de maille est plus facile à configurer et maintenir.

Connectivité à tout, dans le contexte d’une architecture globale, permet à une entreprise avec les utilisateurs dispersés dans le monde entier, des branches, centres de données, des réseaux virtuels et applications de se connecter entre eux via le concentrateur de transit. Le hub de transit agit en tant que le système global de transit.

![chemins de trafic](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Figure 3 : Chemins d’accès le trafic WAN virtuel**

WAN virtuel Azure prend en charge les chemins de connectivité de transit global suivants. Les lettres entre parenthèses mappent à la Figure 3.

* Branche-to-VNet (a)  
* Branche branche (b)
* Utilisateur-à-réseau virtuel distant (c)
* Utilisateur-à-branche distante (d)
* Réseau virtuel-à-réseau virtuel à l’aide de l’homologation de réseau virtuel (e)
* Portée mondiale ExpressRoute 

### <a name="branchvnet"></a>Branch-to-VNet

Branche-à-réseau virtuel est le chemin d’accès primaire pris en charge par Azure WAN virtuel. Ce chemin d’accès vous permet de connecter des branches aux charges de travail IAAS Azure enterprise qui sont déployés dans des réseaux virtuels Azure. Branches peuvent être connectés au réseau virtuel étendu via ExpressRoute ou VPN de site à site. Les transits de trafic à des réseaux virtuels qui sont connectés aux hubs WAN virtuels via des connexions de réseau virtuel.

### <a name="branchbranch"></a>Branch-to-branch

Branches peuvent être connectés à un concentrateur Azure WAN virtuel à l’aide de circuits ExpressRoute et/ou des connexions VPN de site à site. Vous pouvez connecter les branches au hub WAN virtuel qui se trouve dans la région la plus proche de la branche.

Cette option permet aux entreprises de tirer parti de la dorsale principale d’Azure pour vous connecter de branches. Toutefois, même si cette fonctionnalité est disponible, vous devez peser les avantages de connexion des branches via le WAN virtuel Azure par rapport à l’aide d’un réseau privé étendu.

### <a name="usertovnet"></a>Utilisateur-à-réseau virtuel distant

Vous pouvez activer l’accès à distance direct et sécurisé à Azure à l’aide de connexions de point-to-site d’un client de l’utilisateur distant à un réseau virtuel étendu. Les utilisateurs distants d’entreprise n’ont plus à EPINGLE vers le cloud à l’aide d’un VPN d’entreprise.

### <a name="usertobranch"></a>Utilisateur de branche distante

Le chemin d’accès utilisateur à branche distante permet des utilisateurs distants qui utilisent une connexion point-to-site aux charges de travail accès Azure en local et aux applications en transit à travers le cloud. Ce chemin d’accès permet aux utilisateurs distants aux charges de travail d’accès sont déployés dans Azure et locales. Les entreprises peuvent activer service central basé sur le cloud accès à distance sécurisé dans Azure le WAN virtuel.

### <a name="vnetvnet"></a>Transit de réseau virtuel à réseau virtuel à l’aide de l’homologation

Pour connecter des réseaux virtuels entre eux pour prendre en charge des applications à plusieurs niveaux qui sont implémentées sur plusieurs réseaux virtuels, utilisez l’homologation de réseau virtuel. Un scénario de transit réseau virtuel à réseau virtuel via un WAN virtuel Azure n’est actuellement pas pris en charge, mais se trouve sur la feuille de route Azure. Connexion de réseaux virtuels via l’homologation de réseau virtuel est la solution recommandée pour les réseaux virtuels qui doivent être connectés entre eux. Pour plus d’informations sur l’homologation, consultez [vue d’ensemble de l’homologation de réseau virtuel](../virtual-network/virtual-network-peering-overview.md).

### <a name="globalreach"></a>Portée mondiale ExpressRoute

ExpressRoute est privé et résiliente permet de connecter vos réseaux locaux au Cloud Microsoft. Portée mondiale ExpressRoute est une fonctionnalité du module complémentaire pour ExpressRoute. Avec une portée mondiale, vous pouvez lier des circuits ExpressRoute afin de rendre un réseau privé entre vos réseaux locaux. Les branches qui sont connectés à Azure WAN virtuel à l’aide d’ExpressRoute nécessitent la portée mondiale ExpressRoute communiquer entre eux.

Dans ce modèle, chaque branche est connecté au concentrateur WAN virtuel à l’aide d’ExpressRoute peut se connecter à des réseaux virtuels utilisant le chemin d’accès de la branche à réseau virtuel. Le trafic de branche à branche ne transit du hub, car la portée mondiale ExpressRoute permet un chemin d’accès plus optimale sur Azure WAN.

## <a name="security"></a>Sécurité et la stratégie de contrôle

Le hub de réseau virtuel interconnexions et potentiellement voit tous les faire transiter le trafic. Il peut être l’emplacement pour les fonctions de mise en réseau central hôte et de services tels que ces un routage de cloud, stratégie de réseau et la sécurité et contrôle d’accès Internet.

## <a name="next-steps"></a>Étapes suivantes

Créer une connexion à l’aide de WAN virtuel.

* [Connexions de site à site à l’aide de WAN virtuel](virtual-wan-site-to-site-portal.md)
* [Connexions de point à site à l’aide de WAN virtuel](virtual-wan-point-to-site-portal.md)
* [Connexions ExpressRoute à l’aide du WAN virtuel](virtual-wan-expressroute-portal.md)
