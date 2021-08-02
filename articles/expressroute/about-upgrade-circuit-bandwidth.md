---
title: À propos de la mise à niveau de la bande passante du circuit | Azure ExpressRoute
description: Dans cet article, découvrez les bonnes pratiques de mise à niveau de la bande passante d’un circuit ExpressRoute
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: duau
ms.openlocfilehash: 75570beb243fca802ecebebf34edf02b3d8ca9a5
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006488"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>À propos de la mise à niveau de la bande passante d’un circuit ExpressRoute

ExpressRoute permet une connectivité privée et dédiée au réseau global de Microsoft. La connectivité est facilitée par le réseau d’un partenaire ExpressRoute, ou par une connexion directe aux appareils Microsoft Enterprise Edge (MSEE). Une fois la connectivité physique configurée et testée, vous pouvez activer la connectivité de couche 2 et de couche 3 en créant un circuit ExpressRoute et en configurant le peering.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>Mettre à niveau la bande passante d’un circuit

Pour mettre correctement à niveau la bande passante d’un circuit, le partenaire ExpressRoute ou ExpressRoute Direct doit disposer d’une [quantité de bande passante disponible suffisante](#considerations).

Si la capacité est disponible, vous pouvez mettre à niveau le circuit à l’aide des méthodes suivantes :

* [Azure portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>Considérations relatives à la capacité

### <a name="insufficient-capacity-for-physical-connection"></a>Capacité insuffisante pour la connexion physique

Un circuit ExpressRoute est créé sur une connexion physique entre Microsoft et un partenaire ExpressRoute. La connexion physique dispose d’une capacité fixe. Si vous ne parvenez pas à augmenter la taille de votre circuit, cela signifie que la connexion physique sous-jacente de votre circuit existant n’a pas la capacité permettant la mise à niveau. Vous devez créer un nouveau circuit si vous souhaitez modifier la taille du circuit.

Une fois que vous avez créé le circuit ExpressRoute, vous devez lier vos réseaux virtuels existants à ce circuit. Vous pouvez ensuite tester et valider la connectivité du nouveau circuit ExpressRoute avant d’annuler le provisionnement de l’ancien circuit. Il s’agit des étapes de migration recommandées pour réduire le temps d’interruption et les perturbations sur votre charge de travail de production.

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>Bande passante du partenaire ExpressRoute insuffisante

Si vous ne parvenez pas à créer un nouveau circuit ExpressRoute en raison d’une erreur de capacité. Cela signifie que ce partenaire ExpressRoute n’a pas la capacité de se connecter à Microsoft à cet emplacement de peering. Contactez votre partenaire ExpressRoute pour obtenir plus de capacité.

Une fois la nouvelle capacité provisionnée, vous pouvez utiliser les étapes de la section [Mettre à niveau la bande passante d’un circuit](#upgrade) pour créer un circuit, configurer la connectivité et supprimer l’ancien circuit.


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>Bande passante ExpressRoute Direct insuffisante

Si ExpressRoute Direct ne dispose pas d’une capacité suffisante, vous avez deux possibilités. Vous pouvez supprimer les circuits associés à la ressource ExpressRoute Direct dont vous n’avez plus besoin, ou créer une autre ressource ExpressRoute Direct. Pour obtenir des instructions sur la gestion de la ressource ExpressRoute Direct, consultez [Guide pratique pour configurer ExpressRoute Direct](how-to-expressroute-direct-portal.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer et modifier un circuit](expressroute-howto-circuit-portal-resource-manager.md)
* [Créer et modifier une configuration de peering](expressroute-howto-routing-portal-resource-manager.md)
* [Lier un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
