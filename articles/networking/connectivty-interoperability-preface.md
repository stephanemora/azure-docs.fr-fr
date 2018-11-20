---
title: 'Interopérabilité des fonctionnalités de connectivité de back-end Azure : configuration de test | Microsoft Docs'
description: Cet article décrit une configuration de test que vous pouvez utiliser pour analyser l’interopérabilité entre ExpressRoute, un réseau virtuel de site à site et l’homologation de réseau virtuel dans Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: bded5dbf0084d230997be178c1f9a7b8a184ac07
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613210"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Interopérabilité des fonctionnalités de connectivité de back-end Azure : configuration de test

Cet article décrit une configuration de test que vous pouvez utiliser pour analyser l’interopérabilité des services de mise en réseau au niveau du plan de contrôle et du plan de données. Nous allons examiner brièvement les composants de la mise en réseau Azure :

-   **Azure ExpressRoute** : utilisez l’homologation privée d’Azure ExpressRoute pour connecter directement des espaces IP privés de votre réseau local à vos déploiements de réseau virtuel Azure. ExpressRoute vous permet d’obtenir une bande passante supérieure et une connexion privée. De nombreux partenaires de l’écosystème ExpressRoute proposent une connectivité ExpressRoute avec des contrats SLA. Pour en savoir plus sur ExpressRoute et sa configuration, consultez [Présentation d’ExpressRoute][ExpressRoute].
-   **VPN de site à site** : vous pouvez utiliser la passerelle VPN Azure comme un VPN de site à site pour connecter en toute sécurité un réseau local à Azure via Internet ou ExpressRoute. Pour savoir comment configurer un VPN de site à site pour se connecter à Azure, consultez [Configurer une passerelle VPN][VPN].
-   **Homologation de réseaux virtuels** : utilisez l’homologation de réseaux virtuels (VNet) pour établir la connectivité entre les réseaux virtuels d’un réseau virtuel Azure. Pour en savoir plus sur l’homologation de réseaux virtuels, consultez le [tutoriel sur l’appairage de réseaux virtuels][VNet].

## <a name="test-setup"></a>Configuration des tests

L’image suivante illustre la configuration de test :

[![1]][1]

Le cœur de la configuration de test est le réseau virtuel Hub dans la région Azure 1. Le réseau virtuel Hub est connecté à différents réseaux, comme suit :

-   Le réseau virtuel Hub est connecté au réseau virtuel Spoke à l’aide de l’homologation de réseaux virtuels. Le réseau virtuel Spoke a accès à distance aux deux passerelles dans le réseau virtuel Hub.
-   Le réseau virtuel Hub est connecté au réseau virtuel branch à l’aide d’un VPN de site à site. La connectivité utilise eBGP pour échanger des itinéraires.
-   Le réseau virtuel Hub est connecté au réseau local Emplacement 1 en utilisant l’homologation privée ExpressRoute comme chemin d’accès primaire. Il utilise la connectivité VPN de site à site comme chemin de secours. Dans le reste de cet article, nous allons attribuer à ce circuit ExpressRoute le nom ExpressRoute 1. Par défaut, les circuits ExpressRoute offrent une connectivité redondante pour une haute disponibilité. Sur 1 ExpressRoute, la sous-interface du routeur de périphérie du client (CE) secondaire connectée au routeur Microsoft Enterprise Edge (MSEE) secondaire est désactivée. Une ligne rouge sur la flèche double trait dans la figure précédente représente la sous-interface désactivée du routeur CE.
-   Le réseau virtuel Hub est connecté au réseau local Emplacement 2 en utilisant une autre homologation privée ExpressRoute. Dans le reste de cet article, nous allons attribuer à ce second circuit ExpressRoute le nom ExpressRoute 2.
-   Le circuit ExpressRoute1 connecte également le réseau virtuel Hub et le réseau local Emplacement 1 à un réseau virtuel distant dans la région Azure 2.

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Connectivité en tandem d’ExpressRoute et du VPN de site à site

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN de site à site sur ExpressRoute

Vous pouvez configurer un VPN de site à site à l’aide de l’homologation Microsoft ExpressRoute pour échanger des données de façon privée entre votre réseau local et vos réseaux virtuels Azure. Avec cette configuration, vous pouvez échanger des données en toute confidentialité, authenticité et intégrité. L'échange de données est également anti-replay. Pour plus d’informations sur la configuration d’un VPN IPSec de site à site en mode tunnel via l’homologation Microsoft ExpressRoute, consultez l’article [Configurer un réseau VPN de site à site via l’homologation Microsoft ExpressRoute][S2S-Over-ExR]. 

La principale limitation liée à la configuration d’un VPN de site à site qui utilise l’homologation Microsoft est le débit. Le débit sur le tunnel IPSec est limité par la capacité de la passerelle VPN. Le débit d’une passerelle VPN est inférieur au débit ExpressRoute. Dans ce scénario, l’utilisation du tunnel IPsec pour le trafic très sécurisé et celle de l’homologation privée pour les autres catégories de trafic permet d’optimiser l’utilisation de la bande passante ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN de site à site en tant que chemin d’accès de basculement sécurisé pour ExpressRoute

ExpressRoute est utilisé comme une paire de circuits redondants pour garantir une haute disponibilité. Vous pouvez configurer une connectivité ExpressRoute géo-redondante dans différentes régions Azure. En outre, comme nous l’avons démontré dans notre configuration de test, dans une région Azure, vous pouvez utiliser un VPN de site à site afin de créer un chemin d’accès de basculement pour votre connectivité ExpressRoute. Lorsque les mêmes préfixes sont publiés sur ExpressRoute et un VPN de site à site, Azure choisit en priorité ExpressRoute. Pour éviter un routage asymétrique entre ExpressRoute et le VPN de site à site, la configuration réseau locale doit en faire autant, en choisissant la connectivité ExpressRoute avant la connectivité VPN de site à site.

Pour plus d’informations sur la configuration de connexions coexistantes pour ExpressRoute et un VPN de site à site, consultez [Configurer la coexistence de connexions de site à site et ExpressRoutee][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Étendre la connectivité du serveur principal aux emplacements branch et réseaux virtuels spoke

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Connectivité de réseau virtuel spoke via l’homologation de réseaux virtuels

L’architecture de réseau virtuel hub-and-spoke est largement utilisée. Le hub est un réseau virtuel dans Azure qui centralise la connectivité entre vos réseaux virtuels spoke et vers votre réseau local. Les spokes sont des réseaux virtuels appairés avec le hub que vous pouvez utiliser pour isoler les charges de travail. Le trafic circule entre le centre de données local et le hub via une connexion ExpressRoute ou VPN. Pour plus d’informations sur l’architecture, consultez [Implémenter une topologie de réseau Hub et Spoke dans Azure][Hub-n-Spoke].

Dans le cadre de l’homologation de réseaux virtuels dans une région, les réseaux virtuels spoke peuvent utiliser les passerelles de réseau virtuel hub (les passerelles VPN et ExpressRoute) pour communiquer avec les réseaux distants.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Connectivité de réseau virtuel branch à l’aide du VPN de site à site

Vous souhaiterez peut-être que les réseaux virtuels branch, qui se trouvent dans différentes régions, et les réseaux locaux communiquent entre eux via un réseau virtuel Hub. La solution Azure native pour cette configuration est la connectivité VPN de site à site à l’aide d’un VPN. Une alternative consiste à utiliser une appliance virtuelle réseau (NVA) pour le routage dans le hub.

Pour plus d’informations, consultez [Qu’est-ce qu’une passerelle VPN ?] [ VPN] et [Déployer une appliance virtuelle réseau hautement disponible][Deploy-NVA].

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [détails de la configuration][Configuration] pour la topologie de test.

En savoir plus sur l’[analyse de plan de contrôle][Control-Analysis] de la configuration de test et les vues de différents réseaux virtuels ou réseaux VLAN dans la topologie.

En savoir plus sur l’[analyse de plan de données][Data-Analysis] de la configuration de test et les affichages des fonctionnalités de supervision de réseau Azure.

Consultez le [Forum Aux Questions sur ExpressRoute][ExR-FAQ] pour :
-   En savoir plus sur le nombre de circuits ExpressRoute que vous pouvez connecter à une passerelle ExpressRoute.
-   En savoir plus sur le nombre de passerelles ExpressRoute que vous pouvez connecter à un circuit ExpressRoute.
-   En savoir plus sur les autres limites de mise à l’échelle d’ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagramme de la topologie de test"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


