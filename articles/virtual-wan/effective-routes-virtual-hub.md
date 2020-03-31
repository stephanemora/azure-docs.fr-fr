---
title: 'Voir les routes effectives d’un hub virtuel : Azure Virtual WAN | Microsoft Docs'
description: Voir les routes effectives d’un hub virtuel dans Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73511210"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Voir les routes effectives d’un hub virtuel

Vous pouvez voir toutes les routes de votre hub WAN virtuel dans le portail Azure. Pour voir les routes, accédez au hub virtuel, puis sélectionnez **Routing -> View Effective Routes** (Routage -> Voir les routes effectives).

## <a name="understanding-routes"></a><a name="understand"></a>Comprendre les routes

L’exemple suivant peut vous aider à mieux comprendre l’affichage des routes d’un réseau WAN virtuel.

Cet exemple illustre un réseau WAN virtuel comportant trois hubs. Le premier hub se trouve dans la région USA Est, le deuxième hub est situé dans la région Europe Ouest et le troisième hub est dans la région USA Ouest. Dans un réseau WAN virtuel, tous les hubs sont interconnectés. Dans cet exemple, nous partons du principe que les hubs USA Est et Europe Ouest ont des connexions partant de branches locales (rayons) et de réseaux virtuels Azure (rayons).

Un rayon de réseau virtuel Azure (10.4.0.0/16) avec une appliance réseau virtuelle (10.4.0.6) est également appairé à un réseau virtuel (10.5.0.0/16). Pour en savoir plus sur la table de routes d’un hub, consultez [Informations supplémentaires](#abouthubroute), plus loin dans cet article.

Dans cet exemple, nous supposons aussi que la branche 1 Europe Ouest est connectée à la fois au hub USA Est et au hub Europe Ouest. Un circuit ExpressRoute dans la région USA Est connecte la branche 2 au hub USA Est.

![diagramme](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Voir les routes effectives

Quand vous sélectionnez « View Effective Routes » (Voir les routes effectives) dans le portail, cela génère la sortie présentée dans la [table de routes du hub](#routetable) USA Est.

En contexte, la première ligne implique que le hub USA Est a appris la route 10.20.1.0/24 (branche 1) grâce à la connexion du *type de tronçon suivant* VPN ('Next hop' VPN Gateway Instance0 IP 10.1.0.6, Instance1 IP 10.1.0.7). *Origine de la route* référence l’ID de la ressource. *Chemin AS* indique le chemin AS pour la branche 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Table de routes du hub

Utilisez la barre de défilement au bas de la table pour voir le « Chemin AS ».

| **Préfixe** |  **Type de tronçon suivant** | **Tronçon suivant** |  **Origine de la route** |**Chemin AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Connexion de réseau virtuel| On-link |  |  |
|10.5.0.0/16| Adresse IP| 10.4.0.6|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| Adresse IP| `<Azure Firewall IP>` |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Hub distant|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Hub distant|  On-link |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Dans notre exemple de topologie, si les hubs USA Est et Europe Ouest ne communiquaient pas entre eux, la route apprise (10.9.0.0/16) n’existerait pas. Les hubs présentent uniquement les réseaux qui y sont connectés directement.
>

## <a name="additional-information"></a><a name="additional"></a>Informations supplémentaires

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>À propos de la table de routes du hub

Vous pouvez créer une route de hub virtuel et l’appliquer à la table de routage de hub virtuel. Vous pouvez appliquer plusieurs itinéraires à la table de routage du hub virtuel. Cela vous permet de définir une route pour le réseau virtuel de destination en utilisant une adresse IP (généralement l’appliance réseau virtuelle dans un réseau virtuel en rayon). Pour plus d’informations sur les appliances réseau virtuelles, consultez [Router le trafic d’un hub virtuel vers une appliance réseau virtuelle (NVA)](virtual-wan-route-table-portal.md).

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>À propos de la route par défaut (0.0.0.0/0)

Un hub virtuel a la capacité de propager une route par défaut apprise vers une connexion de réseau virtuel, VPN site à site ou ExpressRoute si l’indicateur est « Activé » sur la connexion. Cet indicateur est visible lorsque vous modifiez une connexion de réseau virtuel, une connexion VPN ou une connexion ExpressRoute. « EnableInternetSecurity » a toujours la valeur false par défaut sur les connexions de réseau virtuel, ExpressRoute et VPN des hubs.

La route par défaut n’a pas pour origine le hub WAN virtuel. Elle est propagée si elle a déjà été apprise par le hub WAN virtuel suite au déploiement d’un pare-feu dans le hub, ou si le tunneling forcé est activé sur un autre site connecté.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Virtual WAN, consultez l’article [Vue d’ensemble d’Azure Virtual WAN](virtual-wan-about.md).