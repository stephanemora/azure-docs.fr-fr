---
title: 'Interopérabilité d’ExpressRoute, du réseau privé virtuel de site à site et de VNet Peering - Configuration du test : interopérabilité des fonctionnalités de connectivité du serveur principal Azure | Microsoft Docs'
description: Cette page fournit une configuration de test qui permet d’analyser l’interopérabilité des fonctionnalités d’ExpressRoute, du VPN de site à site et de VNet Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: e859a0a3ac35a9d9f2dab579b7609192e599f90f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947006"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-setup"></a>Interopérabilité d’ExpressRoute, du réseau privé virtuel de site à site et de VNet Peering - Configuration du test
Dans cet article, nous allons identifier une configuration de test que nous pouvons utiliser pour analyser l’interopérabilité de différentes fonctionnalités au niveau du plan de contrôle et du plan de données. Avant d’aborder la configuration de test, nous allons examiner brièvement l’objet de ces fonctionnalités de mise en réseau Azure.

ExpressRoute : avec l’homologation privée d’ExpressRoute, vous pouvez connecter directement des espaces IP privés de votre réseau local à vos déploiements de réseau virtuel Azure.  À l’aide d’ExpressRoute vous pouvez obtenir une bande passante supérieure et une connexion privée. Il existe de nombreux partenaires de l’écosystème, qui offrent une connectivité ExpressRoute avec un contrat SLA. Pour en savoir plus sur ExpressRoute et sa configuration, consultez [Présentation d’ExpressRoute][ExpressRoute].

VPN de site à site : pour connecter en toute sécurité un réseau local à Azure via Internet ou via ExpressRoute, l’option de VPN de site à site (S2S) est disponible. Pour en savoir plus sur la configuration d’un réseau privé virtuel de site à site pour se connecter à Azure, consultez [Configuration d’une passerelle VPN][VPN].

VNet Peering : le peering de réseaux virtuels (VNet Peering) permet de connecter plusieurs réseaux virtuels (Vnet). Pour en savoir plus sur le peering de réseaux virtuels, consultez [Tutoriel : Connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels en utilisant le portail Azure][VNet].

##<a name="test-setup"></a>Configuration des tests

Le schéma ci-dessous illustre la configuration de test.

[![1]][1]

Le cœur de la configuration de test est le réseau virtuel Hub dans la région Azure 1. Le réseau virtuel Hub est connecté à différents réseaux comme suit :

1.  Au réseau virtuel Spoke via le peering de réseaux virtuels. Le réseau virtuel Spoke a accès à distance aux deux passerelles dans le réseau virtuel Hub.
2.  Pour le réseau virtuel Branche via le réseau virtuel de site à site. La connectivité utilise eBGP pour échanger des itinéraires.
3.  Au réseau local Location-1 via le peering privé d’ExpressRoute comme chemin d’accès principal et la connectivité VPN de site à site comme chemin d’accès de secours. Dans le reste de ce document, nous allons attribuer à ce circuit ExpressRoute le nom ExpressRoute1. Par défaut, les circuits ExpressRoute offrent une connectivité redondante pour une haute disponibilité. Sur ExpressRoute1, la sous-interface du routeur CE secondaire accessible sur le MSEE secondaire est désactivée. Elle est indiquée par une ligne rouge sur la flèche à double trait sur le schéma ci-dessus.
4.  Au réseau local Emplacement-2 via une autre homologation privée ExpressRoute. Dans le reste de ce document, nous allons attribuer à ce circuit ExpressRoute le nom ExpressRoute2.
5.  Le circuit ExpressRoute1 connecte également le réseau virtuel Hub et l’emplacement local Location-1 à un réseau virtuel distant dans la région Azure 2.

## <a name="further-reading"></a>Pour aller plus loin

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Utilisation en tandem de la connectivité d’ExpressRoute et du VPN de site à site

#### <a name="site-to-site-vpn-over-expressroute"></a>VPN de site à site sur ExpressRoute 

Le VPN de site à site peut être configuré sur l’homologation Microsoft ExpressRoute pour échanger des données de façon privée entre votre réseau local et vos réseaux virtuels Azure en garantissant confidentialité, anti-relecture, authenticité et intégrité. Pour plus d’informations sur la configuration du VPN IPSec de site à site en mode tunnel sur le peering Microsoft ExpressRoute, consultez l’article [Configurer un réseau VPN de site à site via l’appairage Microsoft ExpressRoute][S2S-Over-ExR]. 

La principale restriction à la configuration d’un VPN de site à site sur le peering Microsoft concerne le débit. Le débit via le tunnel IPSec est limité par la capacité de la passerelle VPN. Le débit de la passerelle VPN est inférieur au débit ExpressRoute. Dans des scénarios de ce type, l’utilisation du tunnel IPSec pour le trafic très sécurisé et celle du peering privé pour les autres catégories de trafic permettraient d’optimiser l’utilisation de la bande passante d’ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN de site à site comme chemin d’accès de basculement pour ExpressRoute
ExpressRoute est proposé comme paire de circuits redondants pour garantir une haute disponibilité. Vous pouvez configurer une connectivité ExpressRoute géo-redondante dans différentes régions Azure. En outre, comme nous l’avons fait dans notre configuration de test, dans une région Azure donnée, si vous avez besoin d’un chemin d’accès de basculement pour votre connectivité ExpressRoute, vous pouvez l’obtenir via le VPN de site à site. Lorsque les mêmes préfixes sont publiés sur ExpressRoute et le VPN de site à site, Azure privilégie ExpressRoute au VPN de site à site. Pour éviter un routage asymétrique entre ExpressRoute et le VPN de site à site, la configuration du réseau local doit également en faire autant en préférant ExpressRoute à la connectivité du VPN de site à site.

Pour plus d’informations sur la configuration de connexions ExpressRoute et VPN de site à site coexistantes, consultez [Configurer la coexistence de connexions de site à site et ExpressRoute][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Extension de la connectivité du serveur principal aux emplacements branche et aux réseaux virtuels spoke

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Connectivité de réseau virtuel spoke via le peering de réseaux virtuels

L’architecture de réseau virtuel hub-and-spoke est largement utilisée. Le hub est un réseau virtuel d’Azure qui centralise la connectivité entre vos réseaux virtuels spoke et votre réseau local. Les spokes sont des réseaux virtuels appairés avec le hub et qui peuvent être utilisés pour isoler les charges de travail. Le trafic circule entre le centre de données local et le hub via une connexion ExpressRoute ou VPN. Pour plus d’informations sur l’architecture, consultez [Topologie hub-and-spoke][Hub-n-Spoke].

Le peering de réseaux virtuels dans une région permet aux réseaux virtuels spoke d’utiliser les passerelles de réseau virtuel hub (passerelles VPN et ExpressRoute) pour communiquer avec les réseaux distants.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Connectivité de réseau virtuel branche à l’aide du VPN de site à site

Si vous souhaitez que les réseaux virtuels branche (dans différentes régions) et les réseaux locaux communiquent entre eux via un réseau virtuel hub, la solution Azure native est la connectivité VPN de site à site à l’aide du VPN. L’autre solution consiste à utiliser une appliance virtuelle réseau pour le routage dans le hub.

Pour configurer des passerelles VPN, consultez l’article [Configuration d’une passerelle VPN][VPN]. Pour déployer des appliances virtuelles réseau hautement disponibles, consultez l’article [Déployer des appliances virtuelles réseau hautement disponibles][Deploy-NVA].

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la configuration de la topologie de test, consultez [Détails de configuration][Configuration].

Pour analyser le plan de contrôle de la configuration de test et comprendre les vues des différents réseaux virtuels/réseau virtuel local de la topologie, consultez [Control-Plane Analysis (Analyse de contrôle de plan)][Control-Analysis].

Pour analyser le plan de données de la configuration de test et pour l’affichage des fonctionnalités de supervision de réseau Azure, consultez [Data-Plane Analysis (Analyse du plan de données)][Data-Analysis].

Pour connaître le nombre de circuits ExpressRoute que vous pouvez connecter à une passerelle ExpressRoute ou le nombre de passerelles ExpressRoute que vous pouvez connecter à un circuit ExpressRoute ou pour découvrir d’autres limites de mise à l’échelle d’ExpressRoute, consultez l’article [Forum Aux Questions ExpressRoute][ExR-FAQ].



<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "La topologie de test"

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




