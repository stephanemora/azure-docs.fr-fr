---
title: Connexion d’Azure à des clouds publics | Microsoft Docs
description: Décrire les différentes façons de connecter Azure à d’autres clouds publics
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889623"
---
# <a name="connecting-azure-with-public-clouds"></a>Connexion d’Azure à des clouds publics

De nombreuses entreprises poursuivent une stratégie multicloud en raison d’objectifs commerciaux et techniques. Il s’agit notamment du coût, de la flexibilité, de la disponibilité des fonctionnalités, de la redondance, de la souveraineté des données, etc. Cette stratégie les aide à exploiter au mieux les deux clouds. 

Cette approche pose également des difficultés pour l’entreprise en termes d’architecture du réseau et des applications. Voici quelques-uns de ces défis : la latence et le débit des données. Pour relever ces défis, les clients cherchent à se connecter directement à plusieurs clouds. Certains fournisseurs de services fournissent une solution permettant de connecter plusieurs fournisseurs de cloud pour les clients. Dans d’autres cas, le client peut déployer son propre routeur pour connecter plusieurs clouds publics.
## <a name="connectivity-via-expressroute"></a>Connectivité via ExpressRoute
ExpressRoute permet à vos clients d’étendre leurs réseaux locaux au cloud de Microsoft via une connexion privée assurée par un fournisseur de connectivité. Grâce à ExpressRoute, les clients peuvent établir des connexions aux services de cloud Microsoft.

Il existe trois façons de se connecter via ExpressRoute.

1. Fournisseur Layer3
2. Fournisseur Layer2
3. Connexion directe

### <a name="layer3-provider"></a>Fournisseur Layer3

Les fournisseurs Layer3 sont communément appelés fournisseurs VPN IP ou VPN MPLS. Les clients tirent parti de ces fournisseurs pour la connectivité multipoint entre leurs centres de données, les branches et le cloud. Les clients se connectent au fournisseur L3 via BGP ou via un itinéraire statique par défaut. Le fournisseur de services publie des itinéraires entre les sites clients, les centres de données et le cloud public. 
 
Lors de la connexion via le fournisseur Layer3, Microsoft publiera des itinéraires VNET clients auprès du fournisseur de services via le protocole BGP. Le fournisseur peut avoir deux implémentations différentes.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Le fournisseur peut insérer chaque fournisseur de cloud dans un VRF distinct si le trafic provenant de tous les fournisseurs de cloud est amené à accéder au routeur du client. Si le client exécute le protocole BGP avec le fournisseur de services, ces itinéraires seront de nouveau publiés sur d’autres fournisseurs de cloud par défaut. 

Si le fournisseur de services insère tous les fournisseurs de cloud dans le même VRF, les itinéraires seront publiés sur d’autres fournisseurs de services cloud directement à partir du fournisseur de services. Cela suppose une opération BGP standard dans laquelle les itinéraires eBGP sont publiés sur d’autres voisins eBGP par défaut.

Chaque cloud public présente une limite de préfixe différente. Par conséquent, le fournisseur de services d’itinéraires doit être prudent lors la distribution des itinéraires.

### <a name="layer2-provider-and-direct-connection"></a>Fournisseur Layer2 et connexion directe

Bien que la connectivité physique dans les deux modèles soit différente, au niveau Layer3, le protocole BGP est établi directement entre MSEE et le routeur du client. Pour ExpressRoute Direct, le client se connecte directement à MSEE. Dans le cas de Layer2, le fournisseur de services étend le VLAN des sites clients vers le cloud. Les clients exécutent le protocole BGP sur le réseau Layer2 pour connecter leurs contrôleurs de domaine au cloud.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
Dans les deux cas, le client aura des connexions point à point à chacun des clouds publics. Le client établira une connexion BGP distincte à chaque cloud public. Les itinéraires reçus par un fournisseur de cloud seront publiés sur d’autres fournisseurs de cloud par défaut. Dans la mesure où chaque fournisseur de cloud présente une limite de préfixe différente, le client doit prendre en compte ces limites lors de la publication des itinéraires. Le client peut utiliser les boutons BGP habituels avec Microsoft tout en publiant des itinéraires à partir d’autres clouds publics.

## <a name="direct-connection-with-expressroute"></a>Connexion directe avec ExpressRoute

Les clients peuvent choisir de connecter directement ExpressRoute à l’offre de connectivité directe du fournisseur de cloud. Deux fournisseurs de cloud seront connectés dos à dos et le protocole BGP sera établi directement entre leurs routeurs. Ce type de connexion est disponible avec Oracle aujourd’hui.

## <a name="site-to-site-vpn"></a>VPN de site à site

Les clients peuvent tirer parti d’Internet pour connecter leurs instances dans Azure à d’autres clouds publics. Presque tous les fournisseurs de cloud offrent des fonctionnalités de VPN de site à site. Toutefois, il peut y avoir des incompatibilités en raison de l’absence de certaines variantes. Par exemple, certains fournisseurs de cloud prennent uniquement en charge le protocole IKEv1 afin qu’il y ait un point de terminaison de terminaison VPN requis dans ce cloud. Pour les fournisseurs de cloud prenant en charge IKEv2, un tunnel direct peut être établi entre les passerelles VPN sur les deux fournisseurs de cloud.

Le réseau VPN de site à site n’est pas considéré comme étant une solution à débit élevé et à faible latence. Toutefois, il peut être utilisé comme sauvegarde pour la connectivité physique.

## <a name="next-steps"></a>Étapes suivantes
Pour toute question complémentaire sur ExpressRoute et la connectivité des réseaux virtuels, consultez l’article [FAQ sur ExpressRoute][ER-FAQ].

Consultez [Configurer une connexion directe entre Azure et Oracle Cloud][ER-OCI] pour la connectivité entre Azure et Oracle

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



