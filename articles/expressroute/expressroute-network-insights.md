---
title: Insights Azure ExpressRoute avec Network Insights
description: En savoir plus sur les insights Azure ExpressRoute avec Network Insights.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: a5e01a4fc84f852b43e5a76114bebe145da10793
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443577"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Insights Azure ExpressRoute avec Network Insights

Cet article explique comment Network Insights peut vous aider à afficher vos métriques et configurations ExpressRoute dans un même emplacement. Avec Network Insights, vous pouvez afficher des cartes topologiques et des tableaux de bord d’intégrité contenant des informations ExpressRoute importantes sans avoir à effectuer d’installation supplémentaire.

:::image type="content" source="./media/expressroute-network-insights/network-monitor-page.png" alt-text="Capture d’écran de la page d’accueil du moniteur de réseaux." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>Visualiser les dépendances fonctionnelles

1. Accédez à la page *Azure Monitor*, puis sélectionnez *Réseaux*.

    :::image type="content" source="./media/expressroute-network-insights/monitor-page.png" alt-text="Capture d’écran de la page de destination de Monitor.":::

1. Sélectionnez la carte *Circuits ExpressRoute*. 

1. Ensuite, sélectionnez le bouton de topologie du circuit que vous souhaitez afficher.

   :::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="Capture d’écran de la page de destination du moniteur ExpressRoute." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png"::: 

1. La vue de dépendance fonctionnelle fournit une image claire de votre configuration ExpressRoute, en soulignant la relation entre les différents composants ExpressRoute (peerings, connexions, passerelles).

    :::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Capture d’écran de la vue topologique pour obtenir des informations sur le réseau." lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

1. Pointez sur un composant de la carte topologique pour afficher les informations de configuration. Par exemple, pointez sur un composant de peering ExpressRoute pour afficher des détails tels que la bande passante du circuit et l’activation de Global Reach.

    :::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="Capture d’écran du survol des ressources de la vue topologique." lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>Afficher un tableau de bord des métriques détaillées et préchargées

Une fois que vous avez examiné la topologie de votre configuration ExpressRoute à l’aide de la vue de dépendance fonctionnelle, sélectionnez **Afficher des métriques détaillées** pour accéder à la vue détaillée des métriques afin de comprendre les performances de votre circuit. Cette vue offre une liste organisée de ressources liées et un tableau de bord riche avec des métriques ExpressRoute importantes.

La section **Ressources liées** répertorie les passerelles ExpressRoute connectées et les peerings configurés, que vous pouvez sélectionner pour accéder à la page de ressources correspondante.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="Capture d’écran de la ressource liée sur la page du moniteur.":::


La section **Métriques ExpressRoute** inclut des graphiques de métriques de circuit importantes dans les catégories **Disponibilité**, **Débit**, **Rejets de paquets** et **Métriques de passerelle**.

### <a name="availability"></a>Disponibilité

L’onglet *Disponibilité* assure le suivi de la disponibilité ARP et BGP, en traçant les données pour le circuit dans son ensemble et pour une connexion individuelle (principale et secondaire). 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="Capture d’écran des graphiques de métriques de disponibilité." lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>Débit

De même, l’onglet *Débit* trace le débit total du trafic entrant et sortant pour le circuit en bits/seconde. Vous pouvez également afficher le débit des connexions individuelles et chaque type de peering configuré.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="Capture d’écran des graphiques de métriques de débit." lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>Rejets de paquets

L’onglet *Rejets de paquets* trace les bits/s supprimés pour le trafic entrant et sortant via le circuit. Cet onglet fournit un moyen simple de surveiller les problèmes de performances qui peuvent se produire si vous avez besoin de dépasser ou dépassez régulièrement la bande passante de votre circuit.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="Capture d’écran des graphiques de paquets rejetés." lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>Métriques des passerelles

Enfin, l’onglet Métriques de passerelle est renseigné avec les graphiques de métriques clés pour une passerelle ExpressRoute sélectionnée (à partir de la section Ressources liées). Utilisez cet onglet lorsque vous avez besoin de surveiller votre connectivité à des réseaux virtuels spécifiques.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="Capture d’écran des métriques de débit et de processeur de la passerelle." lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>Étapes suivantes

Configurez votre connexion ExpressRoute.
  
* En savoir plus sur [Azure ExpressRoute](expressroute-introduction.md), [Network Insights](../azure-monitor/insights/network-insights-overview.md) et [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)
* [Créer et modifier un circuit](expressroute-howto-circuit-arm.md)
* [Créer et modifier une configuration de peering](expressroute-howto-routing-arm.md)
* [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
* [Personnalisez vos métriques](expressroute-monitoring-metrics-alerts.md) et créez un [moniteur de connexion](../network-watcher/connection-monitor-overview.md)
