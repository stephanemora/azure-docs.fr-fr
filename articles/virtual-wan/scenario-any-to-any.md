---
title: 'Scénario : any-to-any'
titleSuffix: Azure Virtual WAN
description: Scénarios pour routage - any-to-any
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567313"
---
# <a name="scenario-any-to-any"></a>Scénario : Any-to-any

Lorsque vous travaillez avec le routage de hub virtuel Virtual WAN, il existe un certain nombre de scénarios disponibles. Dans un scénario Any-to-any, tout spoke peut atteindre un autre spoke. En présence de plusieurs hubs, le routage de hub à hub (ou « entre hubs ») est activé par défaut dans le Virtual WAN standard. 

Dans ce scénario, les connexions VPN, ExpressRoute et VPN utilisateur sont associées à la même table de routage. Toutes les connexions VPN, ExpressRoute et VPN utilisateur propagent des itinéraires dans le même ensemble de tables de routage. Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architecture du scénario

Dans la **Figure 1**, tous les réseaux virtuels et les branches (VPN, ExpressRoute, P2S) peuvent se connecter les uns aux autres. Dans un hub virtuel, les connexions fonctionnent comme suit :

* Une connexion VPN connecte un site VPN à une passerelle VPN.
* Une connexion de réseau virtuel connecte un réseau virtuel à un hub virtuel. Le routeur du hub virtuel fournit la fonctionnalité de transit entre les réseaux virtuels.
* Une connexion ExpressRoute connecte un circuit ExpressRoute à une passerelle ExpressRoute.

Ces connexions (par défaut au moment de la création) sont associées à la table de routage par défaut, sauf si vous configurez la configuration de routage de la connexion soit sur **Aucun**, soit sur une table de routage personnalisée. Ces connexions propagent également les itinéraires par défaut vers la table de routage par défaut. C’est ce qui permet un scénario any-to-any où tout spoke (VNet, VPN, ER, P2S) peut atteindre un autre spoke.

**Figure 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="figure 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Workflow du scénario

Ce scénario est activé par défaut pour le Virtual WAN standard. Si le paramètre branche à branche est désactivé dans la configuration WAN, cela empêchera la connectivité entre les spokes de branche. VPN/ExpressRoute/utilisateur VPN sont considérés comme des spokes de branche dans Virtual WAN

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
