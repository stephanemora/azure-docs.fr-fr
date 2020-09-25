---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 32e4658af48a0ae3bde08de18cf1d8204878d671
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025173"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Le protocole BGP est-il pris en charge sur toutes les références de passerelle VPN Azure ?
Le protocole BGP est pris en charge sur toutes les références SKU de passerelle VPN Azure, à l’exception de la référence SKU De base.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Puis-je utiliser le protocole BGP avec les passerelles VPN Azure basées sur des stratégies ?
Non. Le protocole BGP est pris en charge uniquement sur les passerelles VPN basées sur des itinéraires.

### <a name="what-asn-autonomous-system-numbers-can-i-use"></a>Est-ce que je peux utiliser des numéros ASN (Autonomous System Numbers) ?
Vous pouvez utiliser vos propres numéros ASN publics ou privés pour vos réseaux locaux et réseaux virtuels Azure **à l’exception** des plages réservées par Azure ou l’IANA :

> [!IMPORTANT]
>
> Les ASN suivants sont réservés par Azure ou l’IANA :
> * Numéros ASN réservés par Azure :
>    * ASN publics : 8074, 8075, 12076
>    * ASN privés : 65515, 65517, 65518, 65519, 65520
> * Numéros ASN [réservés par l’IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
>    * 23456, 64496-64511, 65535-65551 et 429496729.
>
> Vous ne pouvez pas spécifier ces ASN pour vos périphériques VPN locaux quand vous les connectez à des passerelles VPN Azure.
>

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>Est-ce que je peux utiliser des numéros ASN 32 bits (4 octets) ?
Oui, les passerelles VPN Azure prennent désormais en charge les ASN 32 bits (4 octets). Utilisez PowerShell/CLI/SDK pour configurer l’utilisation des numéros ASN au format décimal.

### <a name="what-private-asns-can-i-use"></a>Quels sont les ASN privés que je peux utiliser ?
Voici la plage des ASN pouvant être utilisés :

* 64512-65514, 65521-65534

Ces ASN ne sont pas réservés pour IANA ou Azure. Ils peuvent donc être affectés à votre passerelle VPN Azure.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Quelle adresse la passerelle VPN Azure utilise-t-elle pour l’IP de pair BGP ?

* Par défaut, la passerelle VPN Azure alloue une adresse IP unique à partir de la plage de sous-réseau de passerelle pour les passerelles VPN de type actif/passif, ou deux adresses IP pour les passerelles VPN actif-actif. Ces adresses sont allouées automatiquement lorsque vous créez la passerelle VPN. Vous pouvez obtenir les adresses IP BGP réelles allouées à l’aide de PowerShell (Get-AzVirtualNetworkGateway, recherchez la propriété « bgpPeeringAddress »), ou dans le portail Azure (sous la propriété « Configurer un ASN BGP » sur la page Configuration de la passerelle).

* Si vos routeurs VPN locaux utilisent des adresses IP **APIPA** (169.254. x. x) comme adresses IP BGP, vous devez spécifier une autre **adresse IP BGP Azure APIPA** sur votre passerelle VPN Azure. La passerelle VPN Azure sélectionne l’adresse APIPA à utiliser avec le pair BGP APIPA local spécifié dans la passerelle de réseau local ou l’adresse IP privée pour le pair BGP local non-APIPA. Pour plus d’informations, consultez [Configurer BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quelles sont les conditions requises concernant les adresses IP de pair BGP sur mon périphérique VPN ?
Votre adresse de pair BGP locale **NE DOIT PAS** être identique à l’adresse IP publique de votre appareil VPN ni issue de l’espace d’adressage VNet de la passerelle VPN. Utilisez une adresse IP différente de l’IP de pair BGP pour votre périphérique VPN. Il peut s’agir d’une adresse affectée à l’interface de bouclage sur l’appareil, soit une adresse IP normale, soit une adresse APIPA. Si votre appareil utilise une adresse APIPA pour BGP, vous devez spécifier une adresse IP BGP APIPA sur votre passerelle VPN Azure, comme décrit dans [Configurer BGP](../articles/vpn-gateway/bgp-howto.md). Spécifiez cette adresse sur la passerelle de réseau local correspondante, représentant l’emplacement.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Que dois-je spécifier comme préfixes d’adresse pour la passerelle de réseau local lorsque j’utilise le protocole BGP ?

> [!IMPORTANT]
>
>Il y a un changement par rapport à la spécification précédemment documentée. Si vous utilisez BGP pour une connexion, vous devez laisser le champ **Espace d’adressage** ***vide*** pour la ressource de passerelle de réseau local correspondante. La passerelle VPN Azure ajoute en interne une route hôte à l’IP de pair BGP local sur le tunnel IPsec. **N’ajoutez PAS** la route /32 dans le champ Espace d’adressage. C’est redondant et si vous utilisez une adresse APIPA comme adresse IP BGP du périphérique VPN local, vous ne pouvez pas l’ajouter à ce champ. Si vous ajoutez d’autres préfixes dans le champ Espace d’adressage, ceux-ci seront ajoutés comme **routes statiques** sur la passerelle VPN Azure, en plus des routes apprises via BGP.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Puis-je utiliser le même ASN pour les réseaux VPN locaux et les réseaux virtuels Azure ?
Non. Vous devez attribuer des ASN différents à vos réseaux locaux et vos réseaux virtuels Azure si vous les interconnectez avec le protocole BGP. L’ASN 65515 est attribué aux passerelles VPN Azure par défaut, et ce, que le protocole BGP soit activé ou non pour la connectivité entre les sites locaux. Vous pouvez remplacer cette valeur par défaut en attribuant un autre ASN lors de la création de la passerelle VPN, ou modifier l’ASN après avoir créé la passerelle. Vous devez affecter vos ASN locaux aux passerelles de réseau local Azure correspondantes.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Quels préfixes d’adresse les passerelles VPN Azure publieront-elles pour moi ?
La passerelle VPN Azure publiera les itinéraires suivants pour vos périphériques BGP locaux :

* préfixes d’adresse de votre réseau virtuel ;
* préfixes d’adresse de chaque passerelle de réseau local connectée à la passerelle VPN Azure ;
* itinéraires obtenus à partir d’autres sessions de peering BGP connectées à la passerelle VPN Azure, **à l’exception de l’itinéraire par défaut ou des itinéraires se chevauchant avec un préfixe de réseau virtuel**.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Combien de préfixes puis-je publier sur la passerelle VPN Azure ?
Nous prenons en charge jusqu’à 4 000 préfixes. La session BGP s’arrête si le nombre de préfixes dépasse la limite.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Puis-je publier l’itinéraire par défaut (0.0.0.0/0) vers les passerelles VPN Azure ?
Oui.

Notez que cela dirigera tout le trafic de sortie du réseau virtuel vers votre site local et empêchera les machines virtuelles du réseau virtuel d’accepter des communications publiques directement à partir d’Internet, par exemple RDP ou SSH d’Internet vers les machines virtuelles.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Puis-je publier les mêmes préfixes que ceux de mon adresse de réseau virtuel ?

Non. La publication des mêmes préfixes que ceux de votre adresse de réseau virtuel est bloquée ou filtrée par la plateforme Azure. Toutefois, vous pouvez publier un préfixe qui soit un sur-ensemble des éléments de votre réseau virtuel. 

Par exemple, votre réseau virtuel peut utiliser l’espace d’adresse 10.0.0.0/16 et vous pouvez publier 10.0.0.0/8. Par contre, vous ne pouvez pas publier 10.0.0.0/16 ou 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Puis-je utiliser le protocole BGP avec des connexions entre réseaux virtuels ?
Oui. Vous pouvez utiliser le protocole BGP pour les connexions entre sites locaux et entre réseaux virtuels.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Puis-je combiner des connexions BGP et non-BGP pour mes passerelles VPN Azure ?
Oui. Vous pouvez combiner des connexions BGP et non-BGP pour la même passerelle VPN Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>La passerelle VPN Azure prend-elle en charge le routage de transit BGP ?
Oui. Le routage de transit BGP est pris en charge. Cependant, les passerelles VPN Azure ne publient **PAS** les routes par défaut sur les autres pairs BGP. Pour activer le routage de transit via plusieurs passerelles VPN Azure, vous devez activer le protocole BGP sur toutes les connexions intermédiaires entre réseaux virtuels. Pour plus d’informations, consultez [À propose de BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Puis-je créer plusieurs tunnels entre ma passerelle VPN Azure et mon réseau local ?
Oui. Vous pouvez établir plusieurs tunnels VPN S2S entre une passerelle VPN Azure et votre réseau local. Notez que tous ces tunnels seront comptabilisés dans le nombre total de tunnels pour vos passerelles VPN Azure et que vous devez activer BGP sur les deux tunnels.

Par exemple, si vous établissez deux tunnels redondants entre votre passerelle VPN Azure et l’un de vos réseaux locaux, 2 tunnels seront utilisés sur le quota total de votre passerelle VPN Azure.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Puis-je avoir plusieurs tunnels entre deux réseaux virtuels Azure avec protocole BGP ?
Oui, mais au moins une des passerelles de réseau virtuel doit être dans une configuration active-active.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-coexistence-configuration"></a>Est-ce que je peux utiliser BGP pour un VPN S2S dans une configuration de coexistence ExpressRoute/S2S ?
Oui. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Que dois-je ajouter à mon périphérique VPN local pour la session de peering BGP ?
Vous devez ajouter un itinéraire hôte de l’adresse IP de pair BGP Azure sur votre périphérique VPN pointant vers le tunnel VPN S2S IPsec. Par exemple, si l’adresse IP de pair VPN Azure est « 10.12.255.30 », vous devez ajouter un itinéraire hôte pour « 10.12.255.30 » avec l’interface de tronçon suivant de l’interface de tunnel IPsec correspondante sur votre périphérique VPN.
