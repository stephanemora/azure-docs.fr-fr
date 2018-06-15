---
title: Réseau virtuel Azure | Microsoft Docs
description: Découvrez les concepts et les fonctionnalités du réseau virtuel Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/23/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 851c8c1eb13497355038ef4a8d5f1f9326c8c3bc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781178"
---
# <a name="what-is-azure-virtual-network"></a>Présentation du réseau virtuel Azure

Le réseau virtuel Azure permet à de nombreux types de ressources Azure, tels que les machines virtuelles (VM) Azure de communiquer en toute sécurité entre elles, avec Internet et avec les réseaux locaux. Le réseau virtuel Azure fournit les fonctionnalités clés suivantes :

## <a name="isolation-and-segmentation"></a>Isolement et segmentation

Vous pouvez implémenter plusieurs réseaux virtuels au sein de chaque [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Azure et de chaque [région](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) Azure. Chaque réseau privé est isolé des autres réseaux virtuels. Pour chaque réseau virtuel, vous pouvez :
- Spécifier un espace d’adressage IP privé personnalisé à l’aide d’adresses (RFC 1918) publiques et privées. Azure attribue aux ressources d’un réseau virtuel une adresse IP privée à partir de l’espace d’adressage que vous attribuez.
- Segmenter le réseau virtuel en sous-réseaux et allouer une partie de l’espace d’adressage du réseau virtuel à chaque sous-réseau.
- Utilisez la résolution de noms fournie par Azure ou spécifiez votre propre serveur DNS pour une utilisation par les ressources d’un réseau virtuel.

## <a name="communicate-with-the-internet"></a>Communiquer avec Internet

Toutes les ressources d’un réseau virtuel peuvent communiquer en sortie vers Internet, par défaut. Vous pouvez effectuer des communications entrantes vers une ressource en lui assignant une adresse IP publique. Pour en savoir plus, consultez [Adresses IP publiques](virtual-network-public-ip-address.md).

## <a name="communicate-between-azure-resources"></a>Communiquer entre les ressources Azure

Les ressources Azure communiquent en toute sécurité entre elles de l’une des manières suivantes :

- **Via un réseau virtuel** : vous pouvez déployer des machines virtuelles et plusieurs autres types de ressources Azure sur un réseau virtuel, comme des environnements Azure App Service, Azure Kubernetes Service et des groupes de machines virtuelles identiques Azure. Pour obtenir la liste complète des ressources Azure que vous pouvez déployer sur un réseau virtuel, consultez [Intégration des services de réseau virtuel](virtual-network-for-azure-services.md). 
- **Avec un point de terminaison de service de réseau virtuel**: étendez l’espace d’adressage privé de votre réseau virtuel et l’identité de votre réseau virtuel aux ressources du service Azure, tels que les comptes de stockage Azure et les bases de données SQL Azure, par le biais d’une connexion directe. Les points de terminaison de service vous permettent de sécuriser vos ressources critiques du service Azure pour un réseau virtuel uniquement. Pour plus d’informations, consultez [Présentation des points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md).
 
## <a name="communicate-with-on-premises-resources"></a>Communiquer avec les ressources locales

Vous pouvez connecter vos ordinateurs et réseaux locaux à un réseau virtuel à l’aide de n’importe quelle combinaison des options suivantes :

- **Réseau privé virtuel (VPN) de point à site :** connexion établie entre un réseau virtuel et un ordinateur unique de votre réseau. Chaque ordinateur qui doit établir une connexion avec un réseau virtuel doit configurer ses connexions. Ce type de connexion est utile si vous n’êtes pas familiarisé avec Azure, ou pour les développeurs car elle nécessite peu voire pas de modifications de votre réseau existant. La communication entre votre ordinateur et un réseau virtuel passe par un tunnel chiffré via Internet. Pour plus d’informations, consultez [VPN de point à site](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **VPN de site à site :** connexion établie entre votre appareil VPN local et une passerelle VPN Azure déployée dans un réseau virtuel. Ce type de connexion permet à n’importe quelle ressource locale de votre choix d’accéder à un réseau virtuel. La communication entre votre appareil VPN local et une passerelle VPN Azure passe par un tunnel chiffré via Internet. Pour plus d’informations, consultez [VPN de site à site](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute :** connexion établie entre votre réseau et Azure via un partenaire ExpressRoute. Cette connexion est privée. Toutefois, le trafic ne passe pas par Internet. Pour plus d’informations, consultez [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtrer le trafic
Vous pouvez filtrer le trafic réseau entre les sous-réseaux à l’aide d’une des deux options suivantes :
- **Groupes de sécurité réseau :** un groupe de sécurité réseau peut contenir plusieurs règles de sécurité entrantes et sortantes qui vous permettent de filtrer le trafic vers et en provenance des ressources par source et adresse IP de destination, port et protocole. Pour en savoir plus, consultez [Groupes de sécurité réseau](security-overview.md#network-security-groups).
- **Appliances virtuelles de réseau :** une appliance virtuelle de réseau est une machine virtuelle exécutant une fonction réseau, telle qu’un pare-feu, l’optimisation du WAN ou une autre fonction réseau. Pour afficher la liste des appliances virtuelles réseau disponibles que vous pouvez déployer dans un réseau virtuel, consultez [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Router le trafic

Les itinéraires Azure se chargent de l’acheminement entre les sous-réseaux, les réseaux virtuels connectés, les réseaux locaux et Internet, par défaut. Vous pouvez implémenter une ou les deux options suivantes pour remplacer les itinéraires par défaut créés par Azure :
- **Tables de routage :** vous pouvez créer des tables de routage personnalisées avec des itinéraires qui contrôlent où le trafic est acheminé pour chaque sous-réseau. Découvrez-en plus sur les [tables de routage](virtual-networks-udr-overview.md#user-defined).
- **Itinéraires BGP (Border Gateway Protocol) :** si vous connectez votre réseau virtuel à votre réseau local via une connexion ExpressRoute ou la passerelle VPN Azure, vous pouvez propager vos itinéraires BGP locaux à vos réseaux virtuels. En savoir plus sur l’utilisation du protocole BGP avec [la passerelle VPN Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="connect-virtual-networks"></a>Connecter des réseaux virtuels

Vous pouvez connecter des réseaux virtuels entre eux, ce qui permet aux ressources de ces réseaux virtuels de communiquer entre eux à l’aide d’une homologation. Les réseaux virtuels que vous connectez peuvent être situés dans des régions Azure identiques ou différentes. Pour en savoir plus, consultez [Homologation de réseaux virtuels](virtual-network-peering-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant une vue d’ensemble du réseau virtuel Azure. Pour commencer à utiliser un réseau virtuel, créez-en un, déployez-y plusieurs machines virtuelles et communiquez entre les machines virtuelles. Pour en savoir plus, consultez l’article de démarrage rapide [Créer un réseau virtuel](quick-create-portal.md).