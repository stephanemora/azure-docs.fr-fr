---
title: 'Interopérabilité d’ExpressRoute, du réseau privé virtuel de site à site et de VNet Peering - Analyse du plan de contrôle : interopérabilité des fonctionnalités de connectivité du serveur principal Azure | Microsoft Docs'
description: Cette page fournit l’analyse du plan de contrôle de la configuration de test qui est créée pour analyser l’interopérabilité des fonctionnalités d’ExpressRoute, du réseau privé virtuel de site à site et de VNet Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947010"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>Interopérabilité d’ExpressRoute, du réseau privé virtuel de site à site et de VNet-Peering - Analyse du plan de contrôle

Dans cet article, nous allons passer en revue l’analyse du plan de contrôle de la configuration de test. Pour passer en revue la configuration de test, consultez l’article [Configuration de test][Setup]. Pour en savoir plus sur la configuration de test, consultez [Configuration de test][Configuration].

L’analyse du plan de contrôle examine essentiellement les itinéraires échangés entre des réseaux au sein d’une topologie. L’analyse du plan de contrôle permet à différents réseaux d’afficher la topologie.

##<a name="hub-and-spoke-vnet-perspective"></a>Perspective du réseau virtuel hub-and-spoke

Le schéma suivant illustre le réseau selon la perspective du réseau virtuel Hub et du réseau virtuel Spoke (en bleu). Ce schéma montre également le numéro de système autonome (ASN) de différents réseaux et les itinéraires échangés entre différents réseaux. 

[![1]][1]

Notez que l’ASN de la passerelle ExpressRoute du réseau virtuel est différent de l’ASN des routeurs Microsoft Enterprise Edge (MSEE). La passerelle ExpressRoute utilise un ASN privé (65515) et les MSEE utilisent un ASN public (12076) dans le monde entier. Lorsque vous configurez le peering ExpressRoute, étant donné que le MSEE est l’homologue, vous utilisez l’ASN d’homologue 12076. Du côté Azure, MSEE établit le peering eBGP la passerelle ExpressRoute. Le double peering eBGP que MSEE établit pour chaque peering ExpressRoute est transparent au niveau du plan de contrôle. Par conséquent, lorsqu’une table de routage ExpressRoute est affichée, l’ASN de la passerelle ExpressRoute du réseau virtuel s’affiche pour les préfixes du réseau virtuel. La capture d’écran ci-dessous montre un exemple de table de routage ExpressRoute : 

[![5]][5]

Dans Azure, l’ASN n’est significatif que de la perspective du peering. Par défaut, l’ASN de la passerelle ExpressRoute et de la passerelle du réseau privé virtuel est 65515.

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>Perspective du réseau local Location-1 et du réseau virtuel distant via ExpressRoute-1

Le réseau local Location-1 et le réseau virtuel distant sont tous deux connectés au réseau virtuel Hub via ExpressRoute 1. Ils partagent donc la même perspective de la topologie, comme indiqué dans le schéma ci-dessous.

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>Perspective du réseau local Location-1 et du réseau virtuel branche via le VPN de site à site

Le réseau local Location-1 et le réseau virtuel branche sont tous deux connectés à la passerelle VPN du réseau virtuel Hub via les connexions VPN de site à site. Ils partagent donc la même perspective de la topologie, comme indiqué dans le schéma ci-dessous.

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>Perspective du réseau local Location-2

Le réseau local Location-2 est connecté au réseau local Hub via le peering privé d’ExpressRoute 2. 

[![4]][4]

## <a name="further-reading"></a>Pour aller plus loin

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Utilisation en tandem de la connectivité d’ExpressRoute et du VPN de site à site

####  <a name="site-to-site-vpn-over-expressroute"></a>VPN de site à site sur ExpressRoute

Le VPN de site à site peut être configuré sur le peering Microsoft ExpressRoute pour échanger des données de façon privée entre votre réseau local et vos réseaux virtuels Azure en garantissant confidentialité, anti-relecture, authenticité et intégrité. Pour plus d’informations sur la configuration du VPN IPSec de site à site en mode tunnel sur le peering Microsoft ExpressRoute, consultez l’article [Configurer un réseau VPN de site à site via le peering Microsoft ExpressRoute][S2S-Over-ExR]. 

La principale restriction à la configuration d’un VPN de site à site sur le peering Microsoft concerne le débit. Le débit via le tunnel IPSec est limité par la capacité de la passerelle VPN. Le débit de la passerelle VPN est inférieur au débit ExpressRoute. Dans des scénarios de ce type, l’utilisation du tunnel IPSec pour le trafic très sécurisé et celle du peering privé pour les autres catégories de trafic permettraient d’optimiser l’utilisation de la bande passante d’ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN de site à site comme chemin d’accès de basculement pour ExpressRoute
ExpressRoute est proposé comme une paire de circuits redondants pour garantir une haute disponibilité. Vous pouvez configurer une connectivité ExpressRoute géo-redondante dans différentes régions Azure. En outre, comme nous l’avons fait dans notre configuration de test, dans une région Azure donnée, si vous avez besoin d’un chemin d’accès de basculement pour votre connectivité ExpressRoute, vous pouvez l’obtenir via le VPN de site à site. Lorsque les mêmes préfixes sont publiés sur ExpressRoute et le VPN de site à site, Azure privilégie ExpressRoute au VPN de site à site. Pour éviter un routage asymétrique entre ExpressRoute et le VPN de site à site, la configuration du réseau local doit également en faire autant en préférant ExpressRoute à la connectivité du VPN de site à site.

Pour plus d’informations sur la configuration de connexions ExpressRoute et VPN de site à site coexistantes, consultez [Configurer la coexistence de connexions de site à site et ExpressRoute][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Extension de la connectivité du serveur principal aux emplacements branche et aux réseaux virtuels spoke

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Connectivité de réseau virtuel spoke via le peering de réseau virtuel

L’architecture de réseau virtuel hub-and-spoke est largement utilisée. Le hub est un réseau virtuel d’Azure qui centralise la connectivité entre vos réseaux virtuels spoke et votre réseau local. Les spokes sont des réseaux virtuels appairés avec le hub et qui peuvent être utilisés pour isoler les charges de travail. Le trafic circule entre le centre de données local et le hub via une connexion ExpressRoute ou VPN. Pour plus d’informations sur l’architecture, consultez l’article [Implémenter une topologie de réseau hub-and-spoke dans Azure][Hub-n-Spoke].

Le peering de réseaux virtuels dans une région permet aux réseaux virtuels spoke d’utiliser les passerelles de réseau virtuel hub (passerelles VPN et ExpressRoute) pour communiquer avec les réseaux distants.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Connectivité de réseau virtuel branche à l’aide du VPN de site à site

Si vous souhaitez que les réseaux virtuels branche (dans différentes régions) et les réseaux locaux communiquent entre eux via un réseau virtuel hub, la solution Azure native est la connectivité VPN de site à site à l’aide du VPN. L’autre solution consiste à utiliser une appliance virtuelle réseau pour le routage dans le hub.

Pour configurer des passerelles VPN, consultez l’article [Configuration d’une passerelle VPN][VPN]. Pour déployer des appliances virtuelles réseau hautement disponibles, consultez l’article [Déployer des appliances virtuelles réseau hautement disponibles][Deploy-NVA].

## <a name="next-steps"></a>Étapes suivantes

Pour analyser le plan de données de la configuration de test et pour l’affichage des fonctionnalités de supervision de réseau Azure, consultez [Data-Plane Analysis (Analyse du plan de données)][Data-Analysis].

Pour connaître le nombre de circuits ExpressRoute que vous pouvez connecter à une passerelle ExpressRoute ou le nombre de passerelles ExpressRoute que vous pouvez connecter à un circuit ExpressRoute ou pour découvrir d’autres limites de mise à l’échelle d’ExpressRoute, consultez l’article [Forum Aux Questions ExpressRoute][ExR-FAQ].


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Perspective du réseau virtuel hub-and-spoke de la topologie"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Perspective du réseau local Location-1 et du réseau virtuel distant via ExpressRoute-1 de la topologie"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Perspective du réseau local Location-1 et du réseau virtuel branche via le VPN de site à site de la topologie"
[4]: ./media/backend-interoperability/Loc2View.png "Perspective du réseau local Location-2 de la topologie"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Table de routage ExpressRoute 1"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




