---
title: Services de réseau Azure Allemagne | Microsoft Docs
description: Fournir une comparaison des fonctionnalités et des conseils pour la connectivité privée à Azure Allemagne
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 42b68d14776c2c2bd52c69034443052c7d942801
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033490"
---
# <a name="azure-germany-networking-services"></a>Services de réseau Azure Allemagne
## <a name="expressroute-private-connectivity"></a>ExpressRoute (connectivité privée)
Azure ExpressRoute est mis à la disposition générale dans Azure Allemagne. Pour plus d’informations (y compris sur les partenaires et les emplacements de peering), consultez la [documentation générale ExpressRoute ](../expressroute/index.yml).

### <a name="variations"></a>Variantes

* Les clients Azure Allemagne se connectent à une capacité physiquement isolée via une connexion ExpressRoute Azure Allemagne dédiée.
* Azure Allemagne fournit une plus grande disponibilité et une durabilité accrue en tirant parti de plusieurs paires de régions distantes d’au moins 400 kilomètres. 
* Par défaut, toute la connectivité ExpressRoute Azure Allemagne est configurée sous forme redondante actif-actif avec prise en charge de la rupture, et offre des capacités de circuit jusqu’à 10 Go.
* Les emplacements ExpressRoute Azure Allemagne fournissent des itinéraires optimisés (notamment des sauts plus courts, une faible latence, de hautes performances) pour les clients et les régions géo-redondantes Azure Allemagne.
* La connexion privée ExpressRoute Azure Allemagne n’utilise pas, ne parcourt pas et ne dépend pas d’Internet.
* L’infrastructure physique et logique d’Azure Allemagne est physiquement dédiée et séparée du réseau cloud Microsoft international.
* ExpressRoute Azure Allemagne fournit une connectivité privée aux services cloud Microsoft Azure, mais pas aux services cloud Office 365 ou Dynamics 365.

### <a name="considerations"></a>Considérations
Deux services de base fournissent une connectivité réseau privée à Azure Allemagne : ExpressRoute et VPN (site à site pour une organisation typique).

Vous pouvez utiliser ExpressRoute pour créer des connexions privées entre les centres de données Azure Allemagne et votre infrastructure locale, ou au sein d’un environnement de colocalisation. Les connexions ExpressRoute ne sont pas établies par le biais de l'Internet public. Elles offrent davantage de fiabilité, des vitesses supérieures et des latences inférieures par rapport aux connexions Internet classiques. Dans certains cas, l’utilisation de connexions ExpressRoute pour le transfert de données entre des systèmes locaux et Azure offre des avantages significatifs en matière de coûts.   

ExpressRoute vous permet d’établir des connexions à Azure au niveau d’un emplacement ExpressRoute, comme un fournisseur Exchange ExpressRoute. Ou vous pouvez vous connecter directement à Azure à partir de votre WAN existant, comme un VPN MPLS fourni par un fournisseur de services réseau.

Pour que les services réseau prennent en charge les solutions et applications des clients Azure Allemagne, nous vous recommandons fortement d’utiliser ExpressRoute (connectivité privée) pour vous connecter à Azure Allemagne. Si vous utilisez des connexions VPN, tenez compte des points suivants :

* Contactez l’agence/le fonctionnaire responsable de votre autorisation pour déterminer si une connectivité privée ou un autre mécanisme de connexion sécurisée est nécessaire, et pour identifier toute restriction supplémentaire.
* Déterminez s’il faut imposer que la connexion VPN site à site soit acheminée via une zone de connectivité privée.
* Obtenez soit un circuit MPLS, soit une connexion VPN avec un fournisseur d’accès de connectivité privée sous licence.

Si vous utilisez une architecture de connectivité privée, validez qu’une implémentation appropriée est établie et gérée pour la connexion au point de démarcation de routeur périphérique entre le réseau de passerelle et Internet (GN/I) pour Azure Allemagne. De même, votre organisation doit établir une connectivité réseau entre votre environnement local et le point de démarcation de routeur périphérique entre le réseau de passerelle et le client (GN/C) pour Azure Allemagne.

Si vous vous connectez à Microsoft via ExpressRoute dans n’importe quel emplacement de peering de la région Azure Allemagne, vous aurez accès à tous les services cloud Microsoft Azure de toutes les régions situées dans les limites de l’Allemagne. Par exemple, si vous êtes connecté via ExpressRoute à Microsoft, à Berlin, vous aurez accès à tous les services cloud Microsoft hébergés dans Azure Allemagne.

Consultez l’onglet **Vue d’ensemble** de la [documentation générale ExpressRoute](../expressroute/index.yml) pour en savoir plus sur les emplacements et les partenaires, et pour obtenir une liste détaillée des emplacements de peering ExpressRoute pour Azure Allemagne.

Vous pouvez acheter plusieurs circuits ExpressRoute. Le fait de disposer de plusieurs connexions vous offre des avantages significatifs en termes de haute disponibilité en raison de la redondance géographique. Si vous avez plusieurs circuits ExpressRoute, vous recevrez le même jeu de préfixes publiés par Microsoft sur les chemins de peering public. Cela signifie que vous disposez de plusieurs chemins de votre réseau vers Microsoft. Vous risquez ainsi de prendre des décisions de routage non optimales au sein de votre réseau. Et par conséquent, vous risquez de rencontrer des problèmes de connectivité non optimale avec différents services. Pour plus d’informations, consultez l’onglet **Guides pratiques > Bonnes pratiques** de la [documentation générale ExpressRoute](../expressroute/index.yml) et sélectionnez **Optimiser le routage**.

## <a name="support-for-load-balancer"></a>Prise en charge de l’équilibrage de charge
Généralement, Azure Load Balancer est disponible dans Azure Allemagne. Pour plus d’informations, consultez [la documentation générale Load Balancer](../load-balancer/load-balancer-overview.md). 

## <a name="support-for-traffic-manager"></a>Prise en charge de Traffic Manager
Généralement, Traffic Manager est disponible dans Azure Allemagne. Pour plus d’informations, consultez [la documentation générale Traffic Manager](../traffic-manager/traffic-manager-overview.md). 

## <a name="support-for-virtual-network-peering"></a>Prise en charge pour le peering de réseaux virtuels 
Généralement, le peering de réseaux virtuels est disponible dans Azure Allemagne. Pour plus d’informations, consultez la [documentation générale sur le peering de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md). 

## <a name="support-for-vpn-gateway"></a>Prise en charge de la passerelle VPN 
Généralement, la passerelle VPN Azure est disponible dans Azure Allemagne. Pour plus d’informations, consultez [la documentation générale sur la passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, abonnez-vous au [blog Azure Allemagne](https://blogs.msdn.microsoft.com/azuregermany/).
