---
title: 'Azure ExpressRoute : Réinitialiser des peerings de circuit à l’aide du Portail Azure'
description: Découvrez comment désactiver et activer les peerings d’un circuit Azure ExpressRoute à l’aide du Portail Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680283"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Réinitialiser des peerings de circuit ExpressRoute à l’aide du Portail Azure

Cet article décrit comment désactiver et activer les peerings d’un circuit ExpressRoute à l’aide du Portail Azure. Lorsque vous désactivez un peering, la session BGP (Border Gateway Protocol) sur les connexions principale et secondaire de votre circuit ExpressRoute s’arrête. Lorsque vous activez un peering, la session BGP sur les connexions principale et secondaire de votre circuit ExpressRoute est restaurée.

> [!Note]
> Lorsque vous configurez les peerings pour la première fois sur votre circuit ExpressRoute, ils sont activés par défaut.

La réinitialisation de vos peerings ExpressRoute peut être utile dans les scénarios suivants :

* Vous testez la conception et l’implémentation de votre récupération d'urgence. Par exemple, supposons que vous avez deux circuits ExpressRoute. Vous pouvez désactiver les peerings d’un circuit et forcer votre trafic réseau à utiliser l’autre circuit.

* Vous voulez activer l'état BFD (Bidirectional Forwarding Detection) sur le peering privé Azure ou le peering Microsoft. Si votre circuit ExpressRoute a été créé avant le 1er août 2018, sur le peering privé Azure ou avant le 10 janvier 2020, sur le peering Microsoft, BFD n’était pas activé par défaut. Réinitialisez le peering pour activer BFD.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Dans un navigateur, accédez au [Portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

## <a name="reset-a-peering"></a>Réinitialiser un peering

Vous pouvez réinitialiser le peering Microsoft et le peering privé Azure de manière indépendante sur un circuit ExpressRoute.

1. Choisissez le circuit que vous souhaitez modifier.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Capture d’écran montrant le choix d’un circuit dans la liste de circuit ExpressRoute.":::

1. Choisissez la configuration de peering que vous souhaitez réinitialiser.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Capture d’écran montrant le choix d’un peering dans la présentation du circuit ExpressRoute.":::

1. Décochez la case **Activer le peering**, puis sélectionnez **Enregistrer** pour désactiver la configuration du peering.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Capture d’écran montrant la désactivation de la case à cocher Activer le peering.":::

1. Cochez la case **Activer le peering**, puis sélectionnez **Enregistrer** pour réactiver la configuration du peering.

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre des problèmes liés à ExpressRoute, consultez les articles suivants :

* [Vérification de la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Résolution des problèmes de performances réseau](expressroute-troubleshooting-network-performance.md)
