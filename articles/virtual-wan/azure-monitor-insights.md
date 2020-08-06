---
title: Surveillance d’Azure Virtual WAN à l’aide d’Azure Monitor Insights
description: En savoir plus sur la surveillance de Virtual WAN à l’aide d’Azure Monitor Insights
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: 8553a809173d955a21e6730de35c70de5b69e81b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87135960"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure Monitor Insights pour Virtual WAN (préversion)

[Azure Monitor Insights](../azure-monitor/insights/network-insights-overview.md) pour Virtual WAN offre aux utilisateurs et aux opérateurs la possibilité d’afficher l’état et le statut du WAN virtuel, présenté via une carte topologique autodécouverte. L’état et le statut des ressources sont superposés sur la carte pour vous fournir une vue instantanée de l’intégrité globale du WAN virtuel. La navigation dans les ressources est activée sur la carte via un accès en un clic aux pages de configuration des ressources du portail Virtual WAN.

Les métriques au niveau des ressources du WAN virtuel sont collectées et présentées via un classeur de métriques de WAN virtuel pré-empaquetées qui affiche les métriques au niveau d’un WAN virtuel, d’un hub, d’une passerelle et de la connexion. Cet article vous guide tout au long des étapes à suivre pour utiliser Azure Monitor Insights pour Virtual WAN afin d’afficher la topologie et les métriques de votre WAN virtuel dans un seul emplacement.

> [!NOTE]
> Le menu Insights est en cours de déploiement pour cette préversion.
>

## <a name="before-you-begin"></a>Avant de commencer

Les étapes décrites dans cet article supposent que vous avez déjà déployé un réseau étendu virtuel avec un ou plusieurs hubs. Pour créer un nouveau réseau étendu virtuel et un nouveau hub, suivez les étapes décrites dans les articles suivants :

* [Créer un réseau étendu virtuel](virtual-wan-site-to-site-portal.md#openvwan)
* [Créer un hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Afficher la topologie du WAN virtuel

Dans **Portail Azure > Virtual WAN**, à partir du menu **Surveiller** sur la gauche, sélectionnez **Insights (préversion)** . Cela ouvre la **vue Insights**, qui affiche la carte des dépendances du WAN virtuel et le mini-classeur de métriques de haut niveau.

**Figure 1 : menu Surveiller – Insights**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="figure" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

Dans la vue **Insights**, vous pouvez afficher les ressources de WAN virtuel autodécouvertes, telles que les hubs, les passerelles, les pare-feu, les connexions et les réseaux virtuels spoke, les appliances virtuelles réseau de tiers et les branches d’un WAN virtuel de bout en bout, comme indiqué dans la **figure 2**.

L’**état de la ressource** et le **statut**sont codés par couleur et superposés aux icônes de ressource dans la carte. Les métriques de haut niveau du WAN virtuel, telles que les capacités du hub et l’utilisation de la passerelle, s’affichent à droite via un mini-classeur.

**Figure 2 : vue Insights**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="figure" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Affichage des dépendances

La vue **Dépendances** du WAN virtuel permet de visualiser la vue interconnectée de toutes les ressources du WAN virtuel, largement organisées dans une architecture hub-and-spoke.

**Figure 3 : affichage Dépendances du WAN virtuel**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Carte des dépendances" lightbox="./media/azure-monitor-insights/dependency-map.png":::

La carte de la vue Dépendances affiche les ressources suivantes sous forme de graphe connexe :

* Hubs de WAN virtuel dans les différentes régions Azure.
* Réseaux virtuels spoke connectés directement au hub.
* Sites de branche VPN et ExpressRoute et les utilisateurs P2S qui sont connectés à chaque hub via leurs connexions ExpressRoute, S2S et P2S respectives, ainsi que les passerelles de réseau virtuel.
* Pare-feu Azure (y compris les proxys de pare-feu tiers) déployés dans un hub (hub sécurisé).
* Appliances virtuelles réseau de tiers qui sont déployées dans un réseau virtuel spoke.

La carte des dépendances affiche également des réseaux virtuels connectés indirectement (réseaux virtuels qui sont appairés avec des réseaux virtuels spoke de WAN virtuel).

La carte des dépendances facilite la navigation vers les paramètres de configuration de chaque ressource. Par exemple, vous pouvez pointer sur la ressource hub pour afficher la configuration basique de la ressource, comme la région du hub et le préfixe de hub. Cliquez avec le bouton droit pour accéder à la page Portail Azure de la ressource hub.

**Figure 4 : accéder aux informations spécifiques à la ressource**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Informations sur la ressource":::

La barre de recherche et de filtrage de la vue Dépendances permet d’effectuer facilement des recherches dans le graphique. Différents filtres permettent de restreindre votre recherche à un chemin et à un état spécifiques.

**Figure 5 : recherche et filtrage**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="barre de recherche et de filtrage" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Métriques détaillées

Vous pouvez sélectionner **Afficher les métriques détaillées** pour accéder à la page détaillée **Métriques**. La page Métriques est un tableau de bord préconfiguré avec des onglets distincts fournissant des informations utiles sur la capacité, les performances et l’utilisation des ressources du WAN virtuel au niveau du WAN virtuel, du hub et des connexions individuelles.

**Figure 6 : tableau de bord Métriques détaillé**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="Métriques détaillées" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les métriques dans Azure Monitor, consultez [Métriques dans Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).
* Pour obtenir une description complète de toutes les métriques de WAN virtuel, consultez [Journaux et métriques de WAN virtuel](logs-metrics.md).
