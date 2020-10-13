---
title: Afficher et filtrer les informations sur les ressources Azure
description: Filtrez les informations et utilisez des vues différentes pour mieux comprendre vos ressources Azure.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.author: mblythe
ms.date: 09/11/2020
ms.openlocfilehash: 3b9783e7f452b38292c784d44ddb60672e150961
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91650394"
---
# <a name="view-and-filter-azure-resource-information"></a>Afficher et filtrer les informations sur les ressources Azure

Le portail Azure vous permet de consulter des informations détaillées sur les ressources dans vos abonnements Azure. Cet article explique comment filtrer les informations et utiliser des vues différentes pour mieux comprendre vos ressources.

Il se concentre sur l’écran **Toutes les ressources** présenté dans la capture d’écran suivante. Les écrans des types de ressources individuels, tels que les machines virtuelles, ont des options différentes, telles que le démarrage et l’arrêt d’une machine virtuelle.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Portail Azure  : vue de toutes les ressources":::

## <a name="filter-resources"></a>Filtrer les ressources

Commencez à explorer **Toutes les ressources** à l’aide de filtres pour vous concentrer sur un sous-ensemble de vos ressources. La capture d’écran suivante montre le filtrage sur des groupes de ressources, en sélectionnant deux des six groupes de ressources dans un abonnement.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Portail Azure  : vue de toutes les ressources":::

Vous pouvez combiner des filtres, y compris ceux basés sur des recherches de texte, comme illustré dans la capture d’écran suivante. Dans ce cas, les résultats sont limités aux ressources qui contiennent « SimpleWinVM » dans l’un des deux groupes de ressources déjà sélectionnés.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Portail Azure  : vue de toutes les ressources":::

Pour changer les colonnes incluses dans une vue, sélectionnez **Gérer la vue**, puis **Modifier les colonnes**.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Portail Azure  : vue de toutes les ressources":::

## <a name="save-use-and-delete-views"></a>Enregistrer, utiliser et supprimer des vues

Vous pouvez enregistrer des vues qui incluent les filtres et les colonnes que vous avez sélectionnés. Pour enregistrer et utiliser une vue :

1. Sélectionnez **Gérer la vue**, puis **Enregistrer la vue**.

1. Entrez un nom pour la vue, puis sélectionnez **OK**. La vue enregistrée s’affiche désormais dans le menu **Gérer la vue**.

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Portail Azure  : vue de toutes les ressources":::

1. Pour utiliser une vue, basculez entre **Par défaut** et l’une de vos propres vues pour voir comment cela affecte la liste de ressources affichée.

Pour supprimer une vue :

1. Sélectionnez **Gérer la vue**, puis **	Parcourir toutes les vues**.

1. Dans le volet **Vues enregistrées pour « Toutes les ressources »** , sélectionnez la vue, puis sélectionnez l’icône **Supprimer** ![Icône Supprimer la vue](media/manage-filter-resource-views/icon-delete.png).

## <a name="summarize-resources-with-visuals"></a>Synthétiser les ressources avec des visuels

Les vues que nous avons étudiées jusqu’à présent sont des _vues de liste_, mais il existe également des _vues de synthèse_ qui incluent des visuels. Vous pouvez enregistrer et utiliser ces vues de la même façon que les vues de liste. Les filtres sont conservés entre les deux types de vues. Il existe des vues standard, telles que la vue par **localisation** illustrée ci-dessous, ainsi que des vues qui s’appliquent à des services spécifiques, telles que la vue par **état** pour les machines virtuelles.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="Portail Azure  : vue de toutes les ressources":::

Pour enregistrer et utiliser une vue de synthèse :

1. Dans le menu des vues, sélectionnez **Vue Synthèse**.

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Portail Azure  : vue de toutes les ressources":::

1. La vue de synthèse vous permet d’effectuer une synthèse par différents attributs, notamment la **Localisation** et le **Type**. Sélectionnez une option **Synthétiser par** et un visuel approprié. La capture d’écran suivante montre la **Synthèse de type** avec un visuel **Histogramme**.

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Portail Azure  : vue de toutes les ressources":::

1. Sélectionnez **Gérer la vue**, puis **Enregistrer** pour enregistrer cette vue comme vous l’avez fait avec la vue de liste.

1. Dans la vue de synthèse, sous **Synthèse de type**, sélectionnez une barre dans le graphique. La sélection de la barre génère une liste filtrée sur un type de ressource.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Portail Azure  : vue de toutes les ressources":::

## <a name="run-queries-in-azure-resource-graph"></a>Exécuter des requêtes dans Azure Resource Graph

Azure Resource Graph offre une exploration efficace et performante des ressources avec la possibilité d’une interrogation à grande échelle sur un ensemble d’abonnements. L’écran **Toutes les ressources** dans le portail Azure contient un lien permettant d’ouvrir une requête Resource Graph dont l’étendue correspond à la vue filtrée actuelle.

Pour exécuter une requête Resource Graph :

1. Sélectionnez **Ouvrir une requête**.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Portail Azure  : vue de toutes les ressources":::

1. Dans l’**Explorateur Azure Resource Graph**, sélectionnez **Exécuter la requête** pour afficher les résultats.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Portail Azure  : vue de toutes les ressources":::

    Pour plus d’informations, consultez [Exécuter votre première requête Resource Graph à l’aide de l’Explorateur Azure Resource Graph](../governance/resource-graph/first-query-portal.md).

## <a name="next-steps"></a>Étapes suivantes

[Présentation du portail Azure](azure-portal-overview.md)

[Créer et partager des tableaux de bord dans le portail Azure](azure-portal-dashboards.md)
