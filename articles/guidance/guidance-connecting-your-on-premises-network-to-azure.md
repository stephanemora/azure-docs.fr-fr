---
title: Connexion de votre réseau local à Azure | Microsoft Docs
description: Explique et compare les différentes méthodes disponibles pour la connexion aux services de cloud Microsoft comme Azure, Office 365 et Dynamics CRM Online.
services: ''
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: ''
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 9361c0b2ee84d7459bd432f5a8ce13fc5fd85ef8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583506"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Connexion de votre réseau local vers Azure
Microsoft fournit plusieurs types de services cloud. Bien que vous pouvez vous connecter à tous les services via l’Internet public, vous pouvez également connecter à certains des services à l’aide d’un tunnel de réseau privé virtuel (VPN) via Internet ou via une connexion privée directe à Microsoft. Cet article vous permet de déterminer l’option de connectivité qui répondra le mieux à vos besoins en fonction des types de services de cloud Microsoft que vous consommez. La plupart des organisations utilisent plusieurs types de connexion décrits ci-dessous.

## <a name="connecting-over-the-public-internet"></a>Connexion via l’Internet public
Ce type de connexion fournit l’accès aux services cloud Microsoft directement via Internet, comme indiqué ci-dessous.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Cette connexion est généralement le premier type utilisé pour la connexion aux services de cloud de Microsoft. Le tableau ci-dessous répertorie les avantages et inconvénients de ce type de connexion.

| **Avantages** | **Considérations** |
| --- | --- |
| Ne nécessite aucune modification à votre réseau local, que tous les appareils clients ont un accès illimité à toutes les adresses IP et ports sur Internet. |Bien que le trafic est chiffré souvent à l’aide de HTTPS, il peut être intercepté en transit, car il transite par l’Internet public. |
| Peut se connecter à tous les services de cloud de Microsoft exposés à l’Internet public. |Latence imprévisible, car la connexion transite par Internet. |
| Utilise une connexion Internet existante. | |
| Ne nécessite pas la gestion de tous les périphériques connectivité. | |

Cette connexion n’a aucune connectivité ou les coûts de bande passante, car vous utilisez votre connexion Internet existante.

## <a name="connecting-with-a-point-to-site-connection"></a>Connexion avec une connexion point-to-site
Ce type de connexion fournit l’accès à certains services de cloud Microsoft via un tunnel Secure Socket Tunneling Protocol (SSTP) via Internet, comme indiqué ci-dessous.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "connexionPoint-à-site")

La connexion est effectuée via votre connexion Internet existante, mais nécessite l’utilisation d’une passerelle VPN Azure. Le tableau ci-dessous répertorie les avantages et inconvénients de ce type de connexion.

| **Avantages** | **Considérations** |
| --- | --- |
| Ne nécessite aucune modification à votre réseau local, que tous les appareils clients ont un accès illimité à toutes les adresses IP et ports sur Internet. |Bien que le trafic est chiffré à l’aide d’IPSec, il peut être intercepté en transit, car il transite par l’Internet public. |
| Utilise une connexion Internet existante. |Latence imprévisible, car la connexion transite par Internet. |
| Débit jusqu'à 200 Mo/s par passerelle. |Nécessite la création et la gestion des connexions distinctes entre chaque appareil sur votre réseau local et de chaque passerelle à que chaque appareil doit se connecter. |
| Peut être utilisé pour se connecter aux services Azure qui peuvent être connectés aux réseaux virtuels Azure (VNet) telles que les Machines virtuelles et Services Cloud Azure. |Nécessite une administration minimale en cours d’une passerelle VPN Azure. |
| Ne peut pas être utilisé pour se connecter à Microsoft Office 365 ou Dynamics CRM Online. | |
| Ne peut pas être utilisé pour se connecter aux services Azure qui ne peut pas être connectés à un réseau virtuel. | |

En savoir plus sur la [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) service, son [tarification](https://azure.microsoft.com/pricing/details/vpn-gateway)et le transfert de données sortantes [tarification](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Connexion avec une connexion de site à site
Ce type de connexion fournit l’accès à certains services de cloud Microsoft via un tunnel IPSec sur Internet, comme indiqué ci-dessous.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "connexion Site à site")

La connexion est effectuée via votre connexion Internet existante, mais nécessite l’utilisation d’une passerelle VPN d’Azure avec sa tarification associé et le prix du transfert de données sortantes. Le tableau ci-dessous répertorie les avantages et inconvénients de ce type de connexion.

| **Avantages** | **Considérations** |
| --- | --- |
| Tous les appareils sur votre réseau local peuvent communiquer avec les services Azure connectés à un réseau virtuel, il est donc inutile de configurer des connexions individuelles pour chaque appareil. |Bien que le trafic est chiffré à l’aide d’IPSec, il peut être intercepté en transit, car il transite par l’Internet public. |
| Utilise une connexion Internet existante. |Latence imprévisible, car la connexion transite par Internet. |
| Peut être utilisé pour se connecter à des services Azure qui peuvent être connectés à un réseau virtuel comme les Machines virtuelles et Services de Cloud. |Doit configurer et gérer un appareil VPN validé * en local. |
| Débit jusqu'à 200 Mo/s par passerelle. |Nécessite une administration minimale en cours d’une passerelle VPN Azure. |
| Peut forcer le trafic sortant en provenance de cloud de machines virtuelles via le réseau local pour l’inspection et de journalisation à l’aide d’itinéraires définis par l’utilisateur ou le protocole BGP (Border Gateway) **. |Ne peut pas être utilisé pour se connecter à Microsoft Office 365 ou Dynamics CRM Online. |
| Ne peut pas être utilisé pour se connecter aux services Azure qui ne peut pas être connectés à un réseau virtuel. | |
| Si vous utilisez des services qui établissent des connexions vers les appareils en local et vos stratégies de sécurité ont besoin, vous devrez peut-être un pare-feu entre le réseau local et Azure. | |

* * Afficher la liste des [appareils VPN validés](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* ** En savoir plus sur l’utilisation de [itinéraires définis par l’utilisateur](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ou [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) pour forcer le routage des réseaux virtuels Azure vers un périphérique local.

## <a name="connecting-with-a-dedicated-private-connection"></a>Connexion avec une connexion privée dédiée
Ce type de connexion fournit l’accès à tous les services de cloud de Microsoft via une connexion privée dédiée à Microsoft qui ne passent pas par Internet, comme indiqué ci-dessous.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "connexion ExpressRoute")

La connexion nécessite l’utilisation du service ExpressRoute et une connexion à un fournisseur de connectivité. Le tableau ci-dessous répertorie les avantages et inconvénients de ce type de connexion.

| **Avantages** | **Considérations** |
| --- | --- |
| Le trafic ne peut pas être intercepté en transit via l’Internet public, car une connexion dédiée via un fournisseur de service est utilisée. |Requiert la gestion du routeur local. |
| Bande passante jusqu'à 10 Go/s par circuit ExpressRoute et de débit jusqu'à 2 Go/s pour chaque passerelle. |Nécessite une connexion dédiée à un fournisseur de connectivité. |
| Latence prévisible, car il est une connexion dédiée à Microsoft qui ne passent pas par Internet. |Nécessiter une administration minimale en cours d’une ou plusieurs passerelles VPN Azure (si la connexion au circuit aux réseaux virtuels). |
| Ne nécessite pas de communication chiffrée, bien que vous pouvez chiffrer le trafic, si vous le souhaitez. |Si vous utilisez des services de cloud qui établissent des connexions vers les appareils en local, vous devrez peut-être un pare-feu entre le réseau local et Azure. |
| Se connecter directement à tous les services de cloud de Microsoft, avec quelques exceptions *. |Requiert la traduction d’adresses réseau (NAT) d’adresses d’IP local entrant des centres de données Microsoft pour les services qui ne peut pas être connectés à un VNet.* * |
| Peut forcer le trafic sortant lancé à partir de cloud de machines virtuelles via le réseau local pour l’inspection et la journalisation à l’aide du protocole BGP. | |

* * Afficher un [liste des services](../expressroute/expressroute-faqs.md#supported-services) qui ne peut pas être utilisé avec ExpressRoute. Votre abonnement Azure doit être approuvé pour vous connecter à Office 365.  Consultez le [Azure ExpressRoute pour Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) article pour plus d’informations.
* ** En savoir plus sur ExpressRoute [NAT](../expressroute/expressroute-nat.md) exigences.

En savoir plus sur [ExpressRoute](../expressroute/expressroute-introduction.md), elle est associée [tarification](https://azure.microsoft.com/pricing/details/expressroute), et [fournisseurs de connectivité](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Considérations supplémentaires
* Plusieurs des options ci-dessus ont différentes limites maximales, qu'ils peuvent prendre en charge pour les connexions réseau virtuel, les connexions de passerelle et les autres critères. Il est recommandé de consulter l’Azure [limites mise en réseau](../azure-subscription-service-limits.md#networking-limits) à comprendre si un d’eux un impact sur les types de connectivité que vous choisissez d’utiliser.
* Si vous envisagez de connecter une passerelle à partir d’une connexion VPN de site à site au même réseau virtuel comme une passerelle ExpressRoute, vous devez vous familiariser avec les contraintes importantes tout d’abord. Consultez le [coexistence de connexions ExpressRoute de configurer et de Site à Site](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) article pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes
Les ressources ci-dessous expliquent comment implémenter les types de connexion abordées dans cet article.

* [Implémenter une connexion point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implémenter une connexion site à site](guidance-hybrid-network-vpn.md)
* [Implémenter une connexion privée dédiée](guidance-hybrid-network-expressroute.md)
* [Implémenter une connexion privée dédiée avec une connexion de site à site pour la haute disponibilité](guidance-hybrid-network-expressroute-vpn-failover.md)
