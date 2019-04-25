---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/18/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a89a5d753eaa241b11eb4c7eed9500c9715d405d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737073"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Quelle est la différence entre une passerelle de réseau virtuel Azure (passerelle VPN) et une passerelle VPN Azure WAN virtuel ?

WAN virtuel fournit une connectivité de site à site à grande échelle et est conçu pour le débit, l’évolutivité et la facilité d’utilisation. La fonctionnalité de connectivité de point à site et ExpressRoute est actuellement en préversion. Les appareils de branche CPE sont automatiquement provisionnés et connectés à Azure Virtual WAN. Ces périphériques sont disponibles à partir d’un écosystème croissant des partenaires SD-WAN et VPN. Consultez la [liste des partenaires préférés](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Quels fournisseurs d’appareils (partenaires WAN virtuel) sont pris en charge au moment du lancement ?

À ce stade, de nombreux partenaires prennent en charge l’expérience Virtual WAN entièrement automatisée. Pour plus d’informations, consultez [Partenaires WAN virtuel](https://go.microsoft.com/fwlink/p/?linkid=2019615). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quelles sont les étapes d’automatisation des partenaires Virtual WAN ?

Pour découvrir les étapes d’automatisation des partenaires, consultez l’article [Partenaires Virtual WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Est-il nécessaire d’utiliser un périphérique de partenaire préféré ?

 Non. Vous pouvez utiliser n’importe quel appareil prenant en charge les VPN et qui respecte les exigences Azure pour la prise en charge de IPsec IKEv2/IKEv1.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Comment les partenaires WAN virtuel automatisent-ils la connectivité avec le WAN virtuel Azure ?

Les solutions de connectivité à définition logicielle gèrent généralement leurs appareils de branche à l’aide d’un contrôleur ou un centre de provisionnement des appareils. Le contrôleur peut utiliser des API Azure pour automatiser la connectivité au WAN virtuel Azure. Pour plus d’informations, consultez Virtual WAN partner automation (Automation de partenaire WAN virtuel).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>WAN virtuel modifie-t-il les fonctionnalités de connectivité existantes ?   

Aucune modification n’est apportée aux fonctionnalités de connectivité Azure existantes.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Existe-t-il des nouvelles ressources de Resource Manager disponibles pour le WAN virtuel ?
  
Oui, WAN virtuel introduit de nouvelles ressources de Resource Manager. Pour plus d’informations, consultez la [Vue d’ensemble](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Combien d’appareils VPN peuvent se connecter à un même concentrateur ?

Chaque hub virtuel prend en charge jusqu’à 1 000 connexions. Chaque connexion se compose de 2 tunnels qui se trouvent dans une configuration actif-actif. Les tunnels se terminent par une passerelle VPN de Hub virtuel Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>L’appareil VPN local peut-il se connecter à plusieurs hubs ?

Oui. Au départ, le flux du trafic s’effectue de l’appareil local vers l’appareil Microsoft Edge le plus proche, puis vers le hub virtuel.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Est-ce que le peering Global VNet est pris en charge par le WAN virtuel Azure ? 

  Non.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Est-ce que les réseaux virtuels membres spoke et connectés à un hub virtuel peuvent communiquer entre eux ?

Oui. Les réseaux virtuels Spoke peuvent communiquer directement par le biais du peering de réseau virtuel. Toutefois, nous ne prenons pas en charge les réseaux virtuels qui communiquent transitivement par le biais du hub. Pour en savoir plus, consultez [Homologation de réseaux virtuels](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Puis-je déployer et utiliser l’appliance virtuelle de mon réseau favori (dans un réseau virtuel NVA) avec le WAN virtuel Azure ?

Oui, vous pouvez connecter le réseau virtuel VNet de l’appliance virtuelle (NVA) au WAN virtuel Azure. Connectez d’abord le réseau virtuel VNet de l’appliance virtuelle au hub à l’aide d’une connexion au réseau virtuel du hub. Créez ensuite un itinéraire de hub virtuel dont le prochain tronçon pointe vers l’appliance virtuelle. Vous pouvez appliquer plusieurs itinéraires à la table de routage du hub virtuel. Par ailleurs, les membres spoke connectés au réseau virtuel VNet de l’appliance virtuelle (NVA) doivent être connectés au hub virtuel pour s’assurer que leurs itinéraires VNet sont propagés vers les systèmes locaux.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Un réseau virtuel NVA peut-il avoir une passerelle de réseau virtuel ?

 Non. Le réseau virtuel NVA ne peut pas avoir de passerelle de réseau virtuel s’il est connecté au hub virtuel. 

### <a name="is-there-support-for-bgp"></a>Le protocole BGP est-il pris en charge ?

Oui, BGP est pris en charge. Quand vous créez un site VPN, vous pouvez fournir les paramètres BGP qu’il contient. Cela implique que toutes les connexions créées dans Azure pour ce site sont activées pour BGP. Par ailleurs, si vous avez un réseau virtuel avec une appliance virtuelle réseau, et si ce réseau virtuel d’appliance virtuelle réseau est attaché à un hub Virtual WAN, vous devez désactiver BGP pour les spokes attachés au réseau virtuel d’appliance virtuelle réseau pour assurer la publication appropriée des routes de ce dernier. Enfin, connectez ces réseaux virtuels de spokes au réseau virtuel Virtual Hub pour assurer la propagation des routes de réseau virtuel sur les systèmes locaux.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Puis-je diriger le trafic à l’aide de UDR dans le hub virtuel ?

Oui, vous pouvez diriger le trafic vers un réseau virtuel à l’aide de la table de routage du hub virtuel.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Y a-t-il des informations de licence ou de tarification pour le WAN virtuel ?
 
Oui. Consultez la page [Tarification](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Comment calculer le prix d’un hub ?
 
Vous payez les frais de service dans le hub. Par exemple, si vous voulez connecter 10 branches ou appareils locaux à Azure Virtual WAN, vous devez établir une connexion aux points de terminaison VPN du hub. S’il s’agit d’un VPN de 1 unité d’échelle = 500 Mbits/s, vous êtes facturé 0,361 USD/h. Chaque connexion est facturée 0,08 USD/h. Pour 10 connexions, le total des frais de service/h est 0,361 USD + 0,8 USD/h. Les frais de données pour le trafic quittant Azure s’appliquent. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Comment les nouveaux partenaires qui ne figurent pas dans votre liste de partenaires de lancement sont-ils intégrés ?

Envoyez-nous un e-mail à l’adresse azurevirtualwan@microsoft.com. Un partenaire idéal dispose d’un appareil qui peut être approvisionné pour une connectivité IPsec IKEv1 ou IKEv2.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Que se passe-t-il si j’utilise un appareil qui n’est pas dans la liste des partenaires Virtual WAN ? Puis-je toujours l’utiliser pour me connecter au VPN Azure Virtual WAN ?

Oui, si l’appareil prend en charge IPsec IKEv1 ou IKEv2. Les partenaires Virtual WAN automatisent la connectivité de l’appareil aux points de terminaison du VPN Azure. Cela implique des étapes d’automatisation comme « chargement des informations de branche », « IPsec et configuration » et « connectivité ». Comme votre appareil ne vient pas d’un écosystème de partenaires Virtual WAN, vous devez vous-même récupérer la configuration d’Azure et mettre à jour votre appareil pour configurer la connectivité IPsec. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Est-il possible de construire le WAN virtuel Azure avec un modèle Resource Manager ?

Une configuration simple de WAN virtuel avec un hub et un site VPN peut être créée en utilisant un [modèle de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Le WAN virtuel est principalement un service piloté par REST ou le portail.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>La connectivité de branche à branche est-elle autorisée dans le WAN virtuel ?

Oui, la connectivité de branche à branche est autorisée dans le WAN virtuel pour VPN et VPN vers ExpressRoute. Le VPN de site à site est en disponibilité générale, mais ExpressRoute et point à site sont actuellement en préversion.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Le trafic branche à branche traverse-t-il le WAN virtuel Azure ?

Oui.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>En quoi le WAN virtuel est-il différent de la passerelle de réseau virtuel Azure existante ?

Le VPN de la passerelle de réseau virtuel est limité à 30 tunnels. Pour les connexions, vous devez utiliser un WAN virtuel pour le VPN à grande échelle. Vous pouvez connecter jusqu’à 1 000 connexions de branche avec 2 Gbits/s dans le hub pour la totalité des régions, à l’exception de la région Centre-Ouest. Pour la région Centre-Ouest, 20 Gbits/s sont disponibles. A l’avenir, nous déploierons également 20 Gbits/s dans les autres régions. Une connexion est un tunnel actif-actif entre l’appareil VPN local et le hub virtuel. Vous pouvez disposer d’un hub par région, ce qui signifie que vous pouvez connecter plus de 1 000 branches au niveau des hubs.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Comment Virtual WAN prend en charge les appareils SD-WAN ?

Les partenaires Virtual WAN automatisent la connectivité IPsec vers les points de terminaison du VPN Azure. Si le partenaire Virtual WAN est un fournisseur de SD-WAN, il est nécessaire que le contrôleur SD-WAN gère l’automatisation et la connectivité IPsec vers les points de terminaison de VPN Azure. Si l’appareil SD-WAN nécessite son propre point de terminaison au lieu du VPN Azure pour toutes les fonctionnalités SD-WAN propriétaires, vous pouvez déployer le point de terminaison SD-WAN dans un réseau virtuel Azure et le faire coexister avec Azure Virtual WAN.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Ce WAN nécessite-t-il ExpressRoute à partir de chaque site ?

Non, ce WAN ne nécessite pas ExpressRoute à partir de chaque site. Il utilise la connectivité site à site IPsec standard via des liaisons Internet à partir de l’appareil vers un hub de WAN virtuel Azure. Vos sites peuvent être connectés à un réseau de fournisseur à l’aide d’un circuit ExpressRoute. Pour les sites connectés à l’aide d’ExpressRoute dans le hub virtuel (en préversion), les sites peuvent afficher un trafic de branche à branche entre VPN et ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Existe-t-il une limite de débit réseau lors de l’utilisation du WAN virtuel Azure ?

Le nombre de branches est limité à 1 000 connexions par hub/région et à un total de 2 Gbits/s dans le hub. La seule exception est la région USA Centre-Ouest, qui dispose d’un total de 20 Gbits/s. A l’avenir, nous déploierons également 20 Gbits/s dans les autres régions.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Le WAN virtuel autorise-t-il l’appareil en local à utiliser plusieurs ISP en parallèle, ou faut-il toujours un tunnel VPN unique ?

Une connexion entrante dans le VPN Virtual WAN est toujours un tunnel actif-actif (pour assurer la résilience dans la même région, le même hub) utilisant un lien disponible au niveau de la branche. Ce lien peut être un lien de fournisseur de services Internet (ISP) au niveau de la branche locale. Virtual WAN ne fournit pas de logique spéciale pour configurer plusieurs ISP en parallèle. La gestion du basculement entre ISP au niveau de la branche est une opération réseau complètement centrée sur la branche. Vous pouvez utiliser votre solution SD-WAN préférée pour effectuer la sélection du chemin au niveau de la branche.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Comment le trafic est-il acheminé sur la dorsale principale d’Azure ?

Le trafic suit le modèle suivant : appareil de branche -> ISP -> Microsoft Edge - > Microsoft DC (réseau virtuel du hub)-> Microsoft Edge -> ISP -> appareil de branche

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Dans ce modèle, de quoi avez-vous besoin sur chaque site ? Seulement d’une connexion Internet ?

Oui. Une connexion Internet et un appareil physique qui prend en charge IPsec, de préférence parmi nos [partenaires](https://go.microsoft.com/fwlink/p/?linkid=2019615) intégrés. Vous pouvez gérer manuellement la configuration et la connectivité à Azure à partir de l’appareil de votre choix.
