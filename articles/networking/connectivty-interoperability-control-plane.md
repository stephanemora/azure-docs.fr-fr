---
title: 'Interopérabilité des fonctionnalités de connectivité de back-end Azure : analyse du plan de contrôle | Microsoft Docs'
description: Cet article fournit l’analyse du plan de contrôle de la configuration de test que vous pouvez utiliser pour analyser l’interopérabilité entre ExpressRoute, un réseau virtuel de site à site et le peering de réseau virtuel dans Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 37f5399426bebd375200bbc18dae7ed83f4fde3f
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614672"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Interopérabilité des fonctionnalités de connectivité de back-end Azure : analyse du plan de contrôle

Cet article décrit l’analyse du plan de contrôle de la [configuration de test][Setup]. Vous pouvez également consulter la [configuration][Configuration] et l’[analyse du plan de données][Data-Analysis] de l’initialisation (tearDown) de test.

L’analyse du plan de contrôle examine essentiellement les itinéraires qui sont échangés entre des réseaux au sein d’une topologie. L’analyse du plan de contrôle vous permet de comprendre comment des réseaux différents affichent la topologie.

## <a name="hub-and-spoke-vnet-perspective"></a>Perspective du réseau virtuel hub and spoke

La figure suivante illustre le réseau du point de vue d’un réseau virtuel hub (VNet) et d’un réseau virtuel spoke (en bleu). Cette figure montre également le numéro de système autonome (ASN) de différents réseaux et les itinéraires qui sont échangés entre réseaux différents : 

[![1]][1]

L’ASN de la passerelle Azure ExpressRoute du réseau virtuel est différent de l’ASN des routeurs Microsoft Enterprise Edge (MSEE). Une passerelle ExpressRoute utilise un ASN privé (valeur de **65515**) et les MSEE utilisent un ASN public (valeur de **12076**) dans le monde entier. Lorsque vous configurez le peering ExpressRoute, étant donné que le MSEE est l’homologue, vous utilisez l’ASN d’homologue **12076**. Du côté Azure, MSEE établit le peering eBGP la passerelle ExpressRoute. Le double peering eBGP que MSEE établit pour chaque peering ExpressRoute est transparent au niveau du plan de contrôle. Par conséquent, lorsque vous visualisez une table de routage ExpressRoute, l’ASN de la passerelle ExpressRoute du réseau virtuel s’affiche pour les préfixes du réseau virtuel. 

La figure suivante montre un exemple de table de routage ExpressRoute : 

[![5]][5]

Dans Azure, l’ASN n’est significatif que de la perspective du peering. Par défaut, l’ASN de la passerelle ExpressRoute et de la passerelle du réseau privé virtuel est **65515** dans la passerelle VPN Azure.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Perspective du réseau local Location 1 et du réseau virtuel distant via ExpressRoute 1

Le réseau local Location 1 et du réseau virtuel distant sont connectés au réseau virtuel du hub via ExpressRoute 1. Ils partagent la même perspective de la topologie, comme le montre le schéma suivant :

[![2]][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Perspective du réseau local Location 1 et du réseau virtuel branche via le VPN de site à site

Le réseau local Location 1 et le réseau virtuel branche sont tous deux connectés à la passerelle VPN du réseau virtuel Hub via les connexions VPN de site à site. Ils partagent la même perspective de la topologie, comme le montre le schéma suivant :

[![3]][3]

## <a name="on-premises-location-2-perspective"></a>Perspective du réseau local Location 2

Le réseau local Location 2 est connecté au réseau local hub via le peering privé d’ExpressRoute 2 : 

[![4]][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Connectivité en tandem d’ExpressRoute et du VPN de site à site

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN de site à site sur ExpressRoute

Vous pouvez configurer un VPN de site à site à l’aide de l’homologation Microsoft ExpressRoute pour échanger des données de façon privée entre votre réseau local et vos réseaux virtuels Azure. Avec cette configuration, vous pouvez échanger des données en toute confidentialité, authenticité et intégrité. L’échange de données est également anti-replay. Pour plus d’informations sur la configuration d’un VPN IPSec de site à site en mode tunnel via l’homologation Microsoft ExpressRoute, consultez l’article [Configurer un réseau VPN de site à site via l’homologation Microsoft ExpressRoute][S2S-Over-ExR]. 

La principale limitation liée à la configuration d’un VPN de site à site qui utilise l’homologation Microsoft est le débit. Le débit sur le tunnel IPSec est limité par la capacité de la passerelle VPN. Le débit d’une passerelle VPN est inférieur au débit ExpressRoute. Dans ce scénario, l’utilisation du tunnel IPsec pour le trafic très sécurisé et celle du peering privé pour les autres catégories de trafic permet d’optimiser l’utilisation de la bande passante ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN de site à site en tant que chemin d’accès de basculement sécurisé pour ExpressRoute

ExpressRoute est utilisé comme une paire de circuits redondants pour garantir une haute disponibilité. Vous pouvez configurer une connectivité ExpressRoute géo-redondante dans différentes régions Azure. En outre, comme nous l’avons démontré dans notre configuration de test, dans une région Azure, vous pouvez utiliser un VPN de site à site afin de créer un chemin d’accès de basculement pour votre connectivité ExpressRoute. Lorsque les mêmes préfixes sont publiés sur ExpressRoute et un VPN de site à site, Azure choisit en priorité ExpressRoute. Pour éviter un routage asymétrique entre ExpressRoute et le VPN de site à site, la configuration réseau locale doit en faire autant, en choisissant la connectivité ExpressRoute avant la connectivité VPN de site à site.

Pour plus d’informations sur la configuration de connexions coexistantes pour ExpressRoute et un VPN de site à site, consultez [Configurer la coexistence de connexions de site à site et ExpressRoutee][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Étendre la connectivité du serveur principal aux emplacements branch et réseaux virtuels spoke

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Connectivité de réseau virtuel spoke via le peering de réseaux virtuels

L’architecture de réseau virtuel hub-and-spoke est largement utilisée. Le hub est un réseau virtuel dans Azure qui centralise la connectivité entre vos réseaux virtuels spoke et vers votre réseau local. Les spokes sont des réseaux virtuels appairés avec le hub que vous pouvez utiliser pour isoler les charges de travail. Le trafic circule entre le centre de données local et le hub via une connexion ExpressRoute ou VPN. Pour plus d’informations sur l’architecture, consultez [Implémenter une topologie de réseau Hub et Spoke dans Azure][Hub-n-Spoke].

Dans le cadre du peering de réseaux virtuels dans une région, les réseaux virtuels spoke peuvent utiliser les passerelles de réseau virtuel hub (les passerelles VPN et ExpressRoute) pour communiquer avec les réseaux distants.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Connectivité de réseau virtuel branch à l’aide du VPN de site à site

Vous souhaiterez peut-être que les réseaux virtuels branch, qui se trouvent dans différentes régions, et les réseaux locaux communiquent entre eux via un réseau virtuel Hub. La solution Azure native pour cette configuration est la connectivité VPN de site à site à l’aide d’un VPN. Une alternative consiste à utiliser une appliance virtuelle réseau (NVA) pour le routage dans le hub.

Pour plus d’informations, consultez [Qu’est-ce qu’une passerelle VPN ?][VPN] et [Déployer une appliance virtuelle réseau hautement disponible][Deploy-NVA].

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’[analyse de plan de données][Data-Analysis] de la configuration de test et les affichages des fonctionnalités de supervision de réseau Azure.

Consultez le [Forum Aux Questions sur ExpressRoute][ExR-FAQ] pour :
-   En savoir plus sur le nombre de circuits ExpressRoute que vous pouvez connecter à une passerelle ExpressRoute.
-   En savoir plus sur le nombre de passerelles ExpressRoute que vous pouvez connecter à un circuit ExpressRoute.
-   En savoir plus sur les autres limites de mise à l’échelle d’ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Perspective du réseau virtuel hub-and-spoke de la topologie"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Perspective du réseau local Location 1 et du réseau virtuel distant de la topologie via ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Perspective du réseau local Location 1 et du réseau virtuel branche de la topologie via un VPN de site à site"
[4]: ./media/backend-interoperability/Loc2View.png "Perspective du réseau local Location 2 de la topologie"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Table de routage ExpressRoute 1"

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


