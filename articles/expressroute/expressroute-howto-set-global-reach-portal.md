---
title: 'Azure ExpressRoute : Configurer Global Reach à l’aide du portail Azure'
description: Cet article vous aide à associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux locaux et d’activer Global Reach à l’aide du portail Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/05/2021
ms.author: duau
ms.openlocfilehash: 336bd4aaf881b7315921ef374c92a2ac95ff3c8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431313"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Configurer ExpressRoute Global Reach à l’aide du portail Azure

Cet article vous permet de configurer ExpressRoute Global Reach à l’aide de PowerShell. Pour plus d’informations, consultez [ExpressRoute Global Reach](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer la configuration, vérifiez les critères suivants :

* Vous connaissez les [flux de travail](expressroute-workflows.md) de provisionnement du circuit ExpressRoute.
* Vos circuits ExpressRoute sont à l’état provisionné.
* Le peering privé Azure est configuré sur vos circuits ExpressRoute.
* Si vous souhaitez exécuter PowerShell localement, vérifiez que la dernière version d’Azure PowerShell est installée sur votre ordinateur.

## <a name="identify-circuits"></a>Identifier les circuits

1. Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

2. Identifiez les circuits ExpressRoute que vous souhaitez utiliser. Vous pouvez activer ExpressRoute Global Reach entre le peering privé de deux circuits ExpressRoute à condition qu’ils soient dans les pays/régions pris en charge. Les circuits doivent être créés à des emplacements de peering différents. 

   * Si votre abonnement comprend les deux circuits, vous pouvez choisir celui de votre choix pour exécuter la configuration dans les sections suivantes.
   * Si les deux circuits se trouvent dans des abonnements Azure différents, vous devez avoir l’autorisation d’un abonnement Azure, puis transférer la clé d’autorisation lorsque vous exécutez la commande de configuration dans l’autre abonnement Azure.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Capture d’écran de la liste des circuits ExpressRoute.":::

## <a name="enable-connectivity"></a>Activez la connectivité.

Activez la connectivité entre vos réseaux locaux. Il existe des ensembles distincts d’instructions pour les circuits qui sont dans le même abonnement Azure et ceux qui sont dans des abonnements différents.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Circuits ExpressRoute dans le même abonnement Azure

1. Sélectionnez la configuration de peering **privé Azure**. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Capture d’écran de la page de présentation d’ExpressRoute.":::

1. Sélectionnez **Ajouter Global Reach** pour ouvrir la page de configuration *Ajouter Global Reach*.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Activer Global Reach à partir d’un peering privé":::

1. Dans la page de configuration *Ajouter Global Reach*, attribuez un nom à cette configuration. Sélectionnez le *circuit ExpressRoute* auquel vous souhaitez connecter ce circuit et entrez **/29 IPv4** pour le *sous-réseau Global Reach*. Nous utilisons les adresses IP de ce sous-réseau pour établir la connexion entre les deux circuits ExpressRoute. N’utilisez pas ces adresses dans vos réseaux virtuels Azure ou vos réseaux locaux. Sélectionnez **Ajouter** pour ajouter le circuit à la configuration de peering privé.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Capture d’écran de l’ajout de Global Reach dans le peering privé.":::

1. Sélectionnez **Enregistrer** pour terminer la configuration de Global Reach. Une fois l’opération terminée, vous disposerez d’une connectivité entre vos réseaux locaux par le biais des deux circuits ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Capture d’écran de l’enregistrement des configurations de peering privé.":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuits ExpressRoute dans différents abonnements Azure

Si les deux circuits ne se trouvent pas dans le même abonnement Azure, vous avez besoin d’une autorisation. Dans la configuration suivante, l’autorisation est générée à partir de l’abonnement du circuit 2. La clé d’autorisation est ensuite transmise au circuit 1.

1. Générez une clé d’autorisation.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Capture d’écran de la génération de la clé d’autorisation."::: 

   Notez l’ID de ressource du circuit 2, ainsi que la clé d’autorisation.

1. Sélectionnez la configuration de peering **privé Azure**. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Capture d’écran du peering privé sur la page de présentation.":::

1. Sélectionnez **Ajouter Global Reach** pour ouvrir la page de configuration *Ajouter Global Reach*.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Capture d’écran d’Ajouter Global Reach dans le peering privé.":::

1. Dans la page de configuration *Ajouter Global Reach*, attribuez un nom à cette configuration. Cochez la case **Utiliser l’autorisation**. Entrez la **clé d’autorisation** et **l’ID du circuit ExpressRoute** générés et obtenus à l’étape 1. Ensuite, entrez **/29 IPv4** pour le *sous-réseau Global Reach*. Nous utilisons les adresses IP de ce sous-réseau pour établir la connexion entre les deux circuits ExpressRoute. N’utilisez pas ces adresses dans vos réseaux virtuels Azure ou vos réseaux locaux. Sélectionnez **Ajouter** pour ajouter le circuit à la configuration de peering privé.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Capture d’écran d’Ajouter Global Reach avec une clé d’autorisation.":::

1. Sélectionnez **Enregistrer** pour terminer la configuration de Global Reach. Une fois l’opération terminée, vous disposerez d’une connectivité entre vos réseaux locaux par le biais des deux circuits ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Capture d’écran de l’enregistrement de la configuration de peering privé avec Global Reach.":::

## <a name="verify-the-configuration"></a>Vérification de la configuration

Vérifiez la configuration de Global Reach en sélectionnant *Peering privé* sous la configuration du circuit ExpressRoute. Une fois la configuration correcte, elle doit se présenter comme suit :

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Capture d’écran de Global Reach configuré.":::

## <a name="disable-connectivity"></a>Désactiver la connectivité

Pour désactiver la connectivité avec un circuit individuel, sélectionnez le bouton Supprimer en regard du *nom Global Reach*. Ensuite, sélectionnez **Enregistrer** pour terminer l’opération.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Capture d’écran montrant comment désactiver Global Reach.":::

Une fois l’opération terminée, la connectivité entre vos réseaux locaux via vos circuits ExpressRoute n’est plus établie.

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur ExpressRoute Global Reach](expressroute-global-reach.md)
- [Vérifier la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
- [Lier un circuit ExpressRoute à un réseau virtuel Azure](expressroute-howto-linkvnet-arm.md)
