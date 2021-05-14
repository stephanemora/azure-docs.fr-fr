---
title: Forum aux questions sur Azure Route Server
description: Trouvez des réponses aux questions fréquemment posées sur Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 04/22/2021
ms.author: duau
ms.openlocfilehash: 77b9cd7590b381a29fc0dc19b2a80b72afceca0a
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930296"
---
# <a name="azure-route-server-preview-faq"></a>Forum aux questions sur Azure Route Server (préversion)

> [!IMPORTANT]
> Azure Route Server (préversion) est en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>Qu’est-ce qu’Azure Route Server ?

Azure Route Server est un service complètement managé qui vous permet de gérer facilement le routage entre votre appliance virtuelle réseau (NVA) et votre réseau virtuel.

### <a name="is-azure-route-server-just-a-vm"></a>Azure Route Server est-il juste une machine virtuelle ?

Non. Azure Route Server est un service conçu avec une haute disponibilité. S’il est déployé sur une région Azure qui prend en charge les [zones de disponibilité](../availability-zones/az-overview.md), il offre une redondance au niveau de la zone.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>Combien de serveurs de routes est-ce que je peux créer dans un réseau virtuel ?

Vous ne pouvez créer qu’un seul serveur de routes dans un réseau virtuel. Il doit être déployé dans un sous-réseau désigné appelé *RouteServerSubnet*.

### <a name="does-azure-route-server-support-vnet-peering"></a>Azure Route Server prend-il en charge le peering de réseaux virtuels ?

Oui. Si vous appairez un réseau virtuel hébergeant le serveur de routes Azure à un autre réseau virtuel et que vous activez l’utilisation de la passerelle distante sur ce dernier, le serveur de routes Azure apprend les espaces d’adressage de ce réseau virtuel et les envoie à toutes les appliances virtuelles réseau appairées. Il programme également les routes à partir des appliances virtuelles réseau dans la table de routage des machines virtuelles du réseau virtuel appairé. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Quels sont les protocoles de routage pris en charge par Azure Route Server ?

Azure Route Server prend uniquement en charge BGP (Border Gateway Protocol). Votre NVA doit prendre en charge le protocole BGP externe multitronçon, car vous devez déployer Azure Route Server sur un sous-réseau dédié de votre réseau virtuel. Le numéro [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) que vous choisissez doit être différent de celui utilisé par Azure Route Server quand vous configurez le protocole BGP sur votre NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Azure Route Server route-t-il le trafic de données entre ma NVA et mes machines virtuelles ?

Non. Azure Route Server échange des routes BGP uniquement avec votre NVA. Le trafic de données passe directement de l’appliance virtuelle réseau à la machine virtuelle de destination et directement de la machine virtuelle à l’appliance virtuelle réseau.

### <a name="does-azure-route-server-store-customer-data"></a>Serveur de routes Azure stocke-t-il les données des clients ?
Non. Serveur de routes Azure échange uniquement les itinéraires BGP avec votre appliance virtuelle réseau, puis les propage vers votre réseau virtuel.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Si le serveur de routes Azure reçoit la même route de plusieurs appliances virtuelles réseau, comment la gère-t-il ?

Si la route a à la même longueur de chemin AS, le serveur de routes Azure va programmer plusieurs copies de la route, chacune avec un tronçon suivant différent, vers les machines virtuelles du réseau virtuel. Quand les machines virtuelles envoient le trafic vers la destination de cette route, les hôtes de machines virtuelles effectuent un routage ECMP (Equal-Cost Multi-Path). Cependant, si une appliance virtuelle réseau envoie la route avec une longueur de chemin AS plus courte que celle d’autres appliances virtuelles réseau, le serveur de routes Azure programme seulement la route dont le tronçon suivant est défini sur cette appliance virtuelle réseau pour les machines virtuelles du réseau virtuel.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Le serveur de routes Azure conserve-t-il les communautés BGP de la route qu’il reçoit ?

Oui, le serveur de routes Azure propage la route telle quelle avec les communautés BGP.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Est-ce que je peux utiliser des numéros ASN (Autonomous System Numbers) ?

Vous pouvez utiliser vos propres numéros ASN publics ou privés dans votre appliance virtuelle réseau. Vous ne pouvez pas utiliser les plages réservées par Azure ou l’IANA.
Les ASN suivants sont réservés par Azure ou l’IANA :

* Numéros ASN réservés par Azure :
    * ASN publics : 8074, 8075, 12076
    * ASN privés : 65515, 65517, 65518, 65519, 65520
* ASN [réservés par l’IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) :
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Est-ce que je peux utiliser des numéros ASN 32 bits (4 octets) ?

Non, Azure Route Server ne prend en charge que les numéros ASN 16 bits (2 octets).

## <a name="route-server-limits"></a><a name = "limitations"></a>Limites de Route Server

Azure Route Server présente les limites suivantes (par déploiement).

| Ressource | Limite |
|----------|-------|
| Nombre de pairs BGP pris en charge | 8 |
| Nombre de routes que chaque pair BGP peut publier sur Azure Route Server | 200 |
| Nombre de routes qu’Azure Route Server peut publier sur ExpressRoute ou la passerelle VPN | 200 |
| Nombre de machines virtuelles du réseau virtuel (réseaux virtuels appairés compris) que Serveur de routes Azure peut prendre en charge | 6000 |

Si votre appliance virtuelle réseau publie plus d’itinéraires que la limite, la session BGP est abandonnée. Si cela se produit pour la passerelle et le serveur de routage Azure, vous perdez la connectivité de votre réseau local à Azure. Pour plus d’informations, consultez [Diagnostiquer un problème de routage sur une machine virtuelle Azure](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer Azure Route Server](quickstart-configure-route-server-powershell.md).
