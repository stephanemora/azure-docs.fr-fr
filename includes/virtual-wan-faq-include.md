---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/18/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d059cab5668eef8d4dafc1442ca9749a7dcf8c9d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162512"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Quelle est la différence entre une passerelle de réseau virtuel Azure (passerelle VPN) et une passerelle VPN Azure WAN virtuel ?

WAN virtuel fournit une connectivité de site à site à grande échelle et est conçu pour le débit, l’évolutivité et la facilité d’utilisation. Les appareils de branche CPE provisionnent automatiquement et se connectent au WAN virtuel Azure. Ces périphériques sont disponibles à partir d’un écosystème croissant des partenaires SD-WAN et VPN.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Quels fournisseurs d’appareils (partenaires WAN virtuel) sont pris en charge au moment du lancement ? 

À ce stade, Citrix et Riverbed prennent en charge l’expérience WAN virtuel entièrement automatisée. Pour plus d’informations, consultez [Partenaires WAN virtuel](https://aka.ms/virtualwan).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Est-il nécessaire d’utiliser un périphérique de partenaire préféré ?

Non. Vous pouvez utiliser n’importe quel appareil prenant en charge les VPN et qui respecte les exigences de préversion pour la prise en charge de IPsec IKEv2.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Comment les partenaires WAN virtuel automatisent-ils la connectivité avec le WAN virtuel Azure ?

Les solutions de connectivité à définition logicielle gèrent généralement leurs appareils de branche à l’aide d’un contrôleur ou un centre de provisionnement des appareils. Le contrôleur peut utiliser des API Azure pour automatiser la connectivité au WAN virtuel Azure. Pour plus d’informations, consultez [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automation de partenaire WAN virtuel).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>WAN virtuel modifie-t-il les fonctionnalités de connectivité existantes ?   

Aucune modification n’est apportée aux fonctionnalités de connectivité Azure existantes.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Existe-t-il des nouvelles ressources de Resource Manager disponibles pour le WAN virtuel ?
  
Oui, WAN virtuel introduit de nouvelles ressources de Resource Manager. Pour plus d’informations, consultez la [Vue d’ensemble](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Existe-t-il des exigences spéciales pour participer à la préversion ? 

Avant de pouvoir configurer un WAN virtuel Azure, vous devez tout d’abord inscrire automatiquement votre abonnement à la préversion. Pour vous abonner, envoyez un e-mail à <azurevirtualwan@microsoft.com> avec votre ID d’abonnement. Vous recevrez un e-mail une fois votre abonnement inscrit. Vous ne serez pas en mesure d’utiliser le WAN virtuel dans le portail avant de recevoir la confirmation de l’inscription de votre abonnement.

Considérations :

* La préversion est limitée aux régions publiques Azure.
* Jusqu'à 100 connexions sont prises en charge par le hub virtuel. Chaque connexion se compose de 2 tunnels qui se trouvent dans une configuration actif-actif. Les tunnels se terminent par une passerelle VPN de Hub virtuel Azure.
* Envisagez d’utiliser cette préversion si :
  * Vous souhaitez déployer une bande passante agrégée inférieure à 1 Gbit/s par hub virtuel.
  * Vous disposez d’un périphérique VPN prenant en charge la configuration basée sur un itinéraire et la connectivité IPsec IKEv2.
  * Vous voulez explorer à l’aide d’appareils de branche de fonctionnement et SD-WAN à partir de partenaires de lancement (Riverbed et Citrix).<br>or<br>Vous voulez configurer une connectivité de branche à branche et de branche à Azure qui inclut la gestion de la configuration de votre appareil local. (Configuration automatique)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Est-ce que le peering Global VNet est pris en charge par le WAN virtuel Azure ? 

 Non.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Est-ce que les réseaux virtuels membres spoke et connectés à un hub virtuel peuvent communiquer entre eux ?

Oui. Vous pouvez effectuer directement un peering de réseau virtuel entre des membres spoke connectés à un hub virtuel. Pour en savoir plus, consultez [Homologation de réseaux virtuels](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Puis-je déployer et utiliser l’appliance virtuelle de mon réseau favori (dans un réseau virtuel NVA) avec le WAN virtuel Azure ?

Oui, vous pouvez connecter le réseau virtuel de l’appliance virtuelle de votre réseau favori (NVA) au WAN virtuel Azure, mais cela nécessitera des fonctionnalités de routage dans le hub qui est sur notre feuille de route. Tous les membres spoke connectés au réseau virtuel NVA doivent en outre être connectés au hub virtuel. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Un réseau virtuel NVA peut-il avoir une passerelle de réseau virtuel ?

Non. Le réseau virtuel NVA ne peut pas avoir de passerelle de réseau virtuel s’il est connecté au hub virtuel. 

### <a name="is-there-support-for-bgp"></a>Le protocole BGP est-il pris en charge ?

Oui, BGP est pris en charge. Pour vous assurer que les itinéraires à partir d’un réseau virtuel NVA sont publiés en conséquence, les membres spoke doivent désactiver le BGP s’ils sont connectés à un réseau virtuel NVA, qui est connecté à son tour à un hub virtuel. En outre, connectez les réseaux virtuels membres spoke au hub virtuel.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Puis-je diriger le trafic à l’aide de UDR dans le hub virtuel ?

Cela est sur notre feuille de route. Restez connecté !

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Y a-t-il des informations de licence ou de tarification pour le WAN virtuel ?
 
La préversion n’engendre pas de frais supplémentaires. La [tarification actuelle de VPN Azure et de sortie](https://azure.microsoft.com/pricing/details/vpn-gateway/) reste en vigueur pendant la préversion.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Comment les nouveaux partenaires qui ne figurent pas dans votre liste de partenaires de lancement sont-ils intégrés ?

Envoyez-nous un e-mail à l’adresse azurevirtualwan@microsoft.com. Un partenaire idéal dispose d’un appareil qui peut être approvisionné pour une connectivité IPSec IKEv2.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Est-il possible de construire le WAN virtuel Azure avec un modèle Resource Manager ?

Nous y travaillons. Pour le moment, le service est orienté REST et portail.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>La connectivité de branche à branche est-elle autorisée dans le WAN virtuel ?

Oui, la connectivité de branche à branche est autorisée dans le WAN virtuel.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Le trafic branche à branche traverse-t-il le WAN virtuel Azure ?

Oui.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>En quoi le WAN virtuel est-il différent de la passerelle de réseau virtuel Azure existante ?

Le VPN de la passerelle de réseau virtuel est limité à 30 tunnels. Pour les connexions, vous devez utiliser un WAN virtuel pour le VPN à grande échelle. Dans la version préliminaire publique, il est limité à 100 connexions de branche avec 1 Gbit/s dans le hub.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Ce WAN nécessite-t-il ExpressRoute à partir de chaque site ?

Non, ce WAN ne nécessite pas ExpressRoute à partir de chaque site. Il utilise la connectivité site à site IPsec standard via des liaisons Internet à partir de l’appareil vers un hub de WAN virtuel Azure.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Existe-t-il une limite de débit réseau lors de l’utilisation du WAN virtuel Azure ?

Dans la version préliminaire publique, le nombre de branches est limité à 100 connexions par hub/région et à un total de 1 Gbit/s dans le hub.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Le WAN virtuel autorise-t-il l’appareil en local à utiliser plusieurs ISP en parallèle, ou faut-il toujours un tunnel VPN unique ?

Oui, vous pouvez avoir des tunnels actif-actif (2 tunnels = 1 connexion WAN virtuel Azure) à partir d’une seule branche en fonction de l’appareil de branche.

### <a name="how-is-traffic-is-routed-on-the-azure-backbone"></a>Comment le trafic est-il acheminé sur la dorsale principale d’Azure ?

Le trafic suit le modèle : appareil de branche -> ISP -> Microsoft Edge - > Microsoft DC -> Microsoft Edge -> ISP -> appareil de branche.

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Dans ce modèle, de quoi avez-vous besoin sur chaque site ? Seulement d’une connexion Internet ?

Oui. Une connexion Internet et un appareil physique, de préférence de nos partenaires intégrés. Sauf si vous souhaitez gérer manuellement la configuration et la connectivité à Azure à partir de l’appareil de votre choix.