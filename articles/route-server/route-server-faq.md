---
title: Forum aux questions sur Azure Route Server
description: Trouvez des réponses aux questions fréquemment posées sur Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 994fdf3f929f1f64ab4a064d7f2bd24c19322629
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678400"
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

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Quels sont les protocoles de routage pris en charge par Azure Route Server ?

Azure Route Server prend uniquement en charge BGP (Border Gateway Protocol). Votre NVA doit prendre en charge le protocole BGP externe multitronçon, car vous devez déployer Azure Route Server sur un sous-réseau dédié de votre réseau virtuel. Le numéro [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) que vous choisissez doit être différent de celui utilisé par Azure Route Server quand vous configurez le protocole BGP sur votre NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Azure Route Server route-t-il le trafic de données entre ma NVA et mes machines virtuelles ?

Non. Azure Route Server échange des routes BGP uniquement avec votre NVA. Le trafic de données passe directement de la NVA à la machine virtuelle choisie et directement de la machine virtuelle à la NVA.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Si Azure Route Server reçoit la même route à partir de plusieurs NVA, programme-t-il toutes les copies de la route (mais chacune avec un tronçon suivant différent) vers les machines virtuelles du réseau virtuel ?

Oui, uniquement si la route a la même longueur de chemin AS. Quand les machines virtuelles envoient le trafic vers la destination de cette route, les hôtes de machines virtuelles effectuent un routage ECMP (Equal-Cost Multi-Path). Toutefois, si une NVA envoie la route avec une plus petite longueur de chemin AS que les autres NVA, Azure Route Server ne programme que la route dont le tronçon suivant est défini sur cette NVA sur les machines virtuelles du réseau virtuel.

### <a name="does-azure-route-server-support-vnet-peering"></a>Azure Route Server prend-il en charge le peering de réseaux virtuels ?

Oui. Si vous appairez un réseau virtuel hébergeant Azure Route Server à un autre réseau virtuel et que vous activez l’option Utiliser la passerelle distante sur ce réseau virtuel. Azure Route Server découvre les espaces d’adressage de ce réseau virtuel et les envoie à toutes les NVA appairées.

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

Pour plus d’informations, consultez [Diagnostiquer un problème de routage sur une machine virtuelle Azure](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer Azure Route Server](quickstart-configure-route-server-powershell.md).
