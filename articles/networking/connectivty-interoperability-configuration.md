---
title: 'Interopérabilité d’ExpressRoute, du VPN de site à site et de VNet Peering - Détails de la configuration : interopérabilité des fonctionnalités de connectivité du serveur principal Azure | Microsoft Docs'
description: Cette page fournit les détails de la configuration de test qui est créée pour analyser l’interopérabilité des fonctionnalités d’ExpressRoute, du VPN de site à site et de VNet Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946997"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>Interopérabilité d’ExpressRoute, du VPN de site à site et de VNet Peering - Détails de la configuration de test

Dans cet article, nous allons passer en revue les détails de la configuration de test. Pour passer en revue la configuration de test, consultez l’article relatif à la [configuration de test][Setup]. 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>Connectivité de réseau virtuel spoke via le peering de réseaux virtuels

La capture d’écran du Portail Azure suivante montre les détails du peering du réseau virtuel spoke. Pour obtenir des instructions détaillées afin de configurer le peering entre deux réseaux virtuels, consultez [Créer, modifier ou supprimer un peering entre des réseaux virtuels][VNet-Config]. Si vous souhaitez que le réseau virtuel spoke utilise les passerelles connectées au réseau virtuel hub, vous devez cocher l’option *Use remote gateways* (Utiliser des passerelles à distance).

[![1]][1]

La capture d’écran du Portail Azure suivante montre les détails du peering du réseau virtuel hub. Si vous souhaitez que le réseau virtuel hub autorise le réseau virtuel spoke à utiliser ses passerelles, vous devez cocher l’option *Use remote gateways* (Utiliser des passerelles à distance).

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Connectivité de réseau virtuel branch à l’aide du VPN de site à site

La connectivité VPN de site à site entre les réseaux virtuels hub et branch est configurée via les passerelles VPN. Par défaut, les passerelles VPN et ExpressRoute sont configurés avec la valeur ASN privée de 65515. La passerelle VPN vous permet de modifier la valeur ASN. Dans la configuration de test, comme illustré dans la capture d’écran du Portail Azure suivante, la valeur ASN de la passerelle VPN du réseau virtuel branch est remplacée par 65516 pour autoriser le routage eBGP entre les réseaux virtuels hub et branch.


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>Connectivité locale Location-1 à l’aide d’ExpressRoute et du VPN de site à site

###<a name="expressroute1-configuration-details"></a>Détails de la configuration ExpressRoute1

La capture d’écran du portail suivante montre la configuration du circuit ExpressRoute de région Azure 1 vers les routeurs CE locaux Location-1.

[![4]][4]

La capture d’écran du portail suivante montre la configuration de la connexion entre le circuit ExpressRoute1 et le réseau virtuel hub.

[![5]][5]

La configuration suivante correspond à la liste de la configuration du routeur CE principal (les routeurs Cisco ASR1000 sont utilisés en tant que routeur CE dans la configuration de test) associée à la connectivité du peering privé ExpressRoute. Lorsque le circuit ExpressRoute et le VPN de site à site sont configurés en parallèle pour connecter un réseau local à Azure, Azure choisit le circuit ExpressRoute par défaut. Pour éviter un routage asymétrique, le réseau local doit également choisir ExpressRoute plutôt que le VPN de site à site pour les itinéraires reçus via le VPN de site à site et ExpressRoute. Cela peut être effectué dans la configuration suivante à l’aide de l’attribut de préférence locale BGP. 

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

###<a name="site-to-site-vpn-configuration-details"></a>Détails de la configuration VPN de site à site

Voici la liste de la configuration du routeur CE principal associée à la connectivité VPN de site à site :

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>Connectivité locale Location 2 à l’aide d’ExpressRoute

Un deuxième circuit ExpressRoute, très proche de l’emplacement local Location 2, connecte l’emplacement local Location 2 au réseau virtuel hub. La capture d’écran du portail suivante montre la deuxième configuration ExpressRoute.

[![6]][6]

La capture d’écran du portail suivante montre la configuration de la connexion entre le deuxième circuit ExpressRoute et le réseau virtuel hub.

[![7]][7]

Le circuit ExpressRoute1 connecte le réseau virtuel hub et l’emplacement local Location-1 à un réseau virtuel distant dans une autre région Azure.

[![8]][8]

## <a name="further-reading"></a>Pour aller plus loin

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Utilisation en tandem de la connectivité d’ExpressRoute et du VPN de site à site

#### <a name="site-to-site-vpn-over-expressroute"></a>VPN de site à site sur ExpressRoute

Le VPN de site à site peut être configuré sur le peering Microsoft ExpressRoute pour échanger des données de façon privée entre votre réseau local et vos réseaux virtuels Azure en garantissant confidentialité, anti-relecture, authenticité et intégrité. Pour plus d’informations sur la configuration du VPN IPSec de site à site en mode tunnel via le peering Microsoft ExpressRoute, consultez l’article [Configurer un réseau VPN de site à site via le peering Microsoft ExpressRoute][S2S-Over-ExR]. 

La principale restriction à la configuration d’un VPN de site à site sur le peering Microsoft concerne le débit. Le débit sur le tunnel IPSec est limité par la capacité de la passerelle VPN. Le débit de la passerelle VPN est inférieur au débit ExpressRoute. Dans des scénarios de ce type, l’utilisation du tunnel IPSec pour le trafic très sécurisé et celle du peering privé pour les autres catégories de trafic permettraient d’optimiser l’utilisation de la bande passante ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN de site à site en tant que chemin d’accès de basculement sécurisé pour ExpressRoute
ExpressRoute est proposé comme une paire de circuits redondants pour garantir une haute disponibilité. Vous pouvez configurer une connectivité ExpressRoute géo-redondante dans différentes régions Azure. En outre, comme nous l’avons fait dans notre configuration de test, si vous avez besoin d’un chemin d’accès de basculement pour votre connectivité ExpressRoute, vous pouvez l’obtenir via le VPN de site à site. Lorsque les mêmes préfixes sont publiés sur ExpressRoute et le VPN de site à site, Azure choisit ExpressRoute plutôt que le VPN de site à site. Pour éviter un routage asymétrique entre ExpressRoute et le VPN de site à site, la configuration de réseau locale doit en faire autant, en choisissant ExpressRoute plutôt que la connectivité VPN de site à site.

Pour plus d’informations sur la configuration des connexions ExpressRoute et VPN de site à site coexistantes, consultez [Configurer la coexistence de connexions de site à site et ExpressRoute (classique)][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Extension de la connectivité du serveur principal aux emplacements branch et réseaux virtuels spoke

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Connectivité de réseau virtuel spoke via le peering de réseaux virtuels

L’architecture de réseau virtuel hub-and-spoke est largement utilisée. Le hub est un réseau virtuel dans Azure qui centralise la connectivité entre vos réseaux virtuels spoke et vers votre réseau local. Les spokes sont des réseaux virtuels appairés avec le hub et qui peuvent être utilisés pour isoler les charges de travail. Le trafic circule entre le centre de données local et le hub via une connexion ExpressRoute ou VPN. Pour plus d’informations sur l’architecture, consultez [Implémenter une topologie de réseau hub-and-spoke dans Azure][Hub-n-Spoke]

Le peering de réseaux virtuels dans une région permet aux réseaux virtuels spoke d’utiliser les passerelles de réseau virtuel hub (les passerelles VPN et ExpressRoute) pour communiquer avec les réseaux distants.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Connectivité de réseau virtuel branch à l’aide du VPN de site à site

Si vous souhaitez que les réseaux virtuels branch (dans différentes régions) et les réseaux locaux communiquent entre eux via un réseau virtuel hub, la solution Azure native est la connectivité VPN de site à site via le VPN. Vous pouvez également utiliser une appliance virtuelle réseau pour le routage dans le hub.

Pour configurer des passerelles VPN, consultez [Configuration d’une passerelle VPN][VPN]. Pour déployer des appliances virtuelles réseau hautement disponible, consultez [Déployer des appliances virtuelles réseau hautement disponibles][Deploy-NVA].

## <a name="next-steps"></a>Étapes suivantes

Pour l’analyse de plan de contrôle de la configuration de test et comprendre les vues de différents réseaux virtuels/réseaux locaux de la topologie, consultez [Interoperability of ExpressRoute, Site-to-site VPN, and VNet-Peering - Control plane analysis][Control-Analysis] (Interopérabilité d’ExpressRoute, du VPN de site à site et de VNet Peering - Analyse du plan de contrôle).

Pour l’analyse de plan de données de la configuration de test et les affichages des fonctionnalités de supervision de réseau Azure, consultez [Interoperability of ExpressRoute, Site-to-site VPN, and VNet-Peering - Data plane analysis][Data-Analysis] (Interopérabilité d’ExpressRoute, du VPN de site à site et de VNet Peering - Analyse du plan de données).

Pour connaître le nombre de circuits ExpressRoute que vous pouvez connecter à une passerelle ExpressRoute ou le nombre de passerelles ExpressRoute que vous pouvez connecter à un circuit ExpressRoute ou découvrir d’autres limites de mise à l’échelle d’ExpressRoute, consultez [Forum Aux Questions ExpressRoute][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Peering du réseau virtuel spoke"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Peering du réseau virtuel hub"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "Configuration de passerelle VPN du réseau virtuel branch"
[4]: ./media/backend-interoperability/ExR1.png "Configuration ExpressRoute1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Configuration de la connexion d’ExpressRoute1 à la passerelle EXR de réseau virtuel hub"
[6]: ./media/backend-interoperability/ExR2.png "Configuration ExpressRoute2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Configuration de la connexion d’ExpressRoute2 à la passerelle EXR de réseau virtuel hub"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Configuration de la connexion d’ExpressRoute2 à la passerelle EXR de réseau virtuel distant"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
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




