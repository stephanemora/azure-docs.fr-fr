---
title: Insights sur les analyses de vos données dans Azure Purview
description: Ce guide pratique explique comment afficher et utiliser les rapports Purview d’insights sur les analyses en lien avec vos données.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 17935a3dc4aef3c5cd9d6b1e162a2002b3c06e2c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211372"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Insights sur les analyses de vos données dans Azure Purview

Ce guide pratique explique comment accéder aux rapports d’insights sur les analyses Azure Purview en lien avec vos données, ainsi que comment les afficher et les filtrer.

> [!IMPORTANT]
> Azure Purview est actuellement disponible en PRÉVERSION. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans ce guide pratique, vous allez apprendre à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Afficher des insights à partir de votre compte Purview.
> * Obtenir une vue globale de vos analyses.

## <a name="prerequisites"></a>Prérequis

Avant de commencer à utiliser les insights Purview, assurez-vous d’avoir effectué les étapes suivantes :

* Configurez vos ressources Azure et renseignez le compte avec des données.
* Configurez et effectuez une analyse sur la source de données.

Pour plus d’informations, consultez [Gérer des sources de données dans Azure Purview](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Utiliser les insights sur les analyses dans Purview

Dans Azure Purview, vous pouvez inscrire et analyser les types de sources. Vous pouvez afficher l’état de l’analyse dans le temps dans les insights sur les analyses. Les insights vous indiquent le nombre d’analyses qui ont échoué, qui ont réussi ou qui ont été annulées pendant une période donnée.

### <a name="view-scan-insights"></a>Afficher des insights sur les analyses

1. Accédez à l’écran de l’instance **Azure Purview** dans le portail Azure et sélectionnez votre compte Purview.

1. Sur la page **Overview (Vue d’ensemble)** , dans la section **Get Started (Démarrer)** , sélectionnez la vignette **Open Purview Studio (Ouvrir Purview Studio)** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Lancer Purview à partir du portail Azure":::

1. Sur la **page d'accueil** de Purview, sélectionnez **Insights** dans le menu de gauche.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Afficher vos insights dans le portail Azure":::

1. Dans la zone **Insights** , sélectionnez **Scans (Analyses)** pour afficher le rapport Purview **Scan insights (Insights sur les analyses)** .

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Afficher les indicateurs de performance clés de haut niveau pour accéder au nombre d’analyses par état
 
1. Les indicateurs de performance clés affichent le nombre total d’analyses exécutées sur une période donnée. La période est définie par défaut sur les 30 derniers jours. Toutefois, vous pouvez également sélectionner les sept derniers jours. En fonction du filtre de temps, les valeurs d’indicateur de performance clé reflètent le nombre d’analyses de façon appropriée.


1. En fonction de la valeur de filtre de temps sélectionnée, vous pouvez voir la distribution des analyses qui ont réussi, qui ont échoué et qui ont été annulées par semaine ou par jour dans le graphe.

1. En bas du graphique se trouve un lien **Voir plus** pour vous permettre d’approfondir votre exploration. Ce lien ouvre la page **État de l'analyse** dans l’expérience Insights relatifs aux analyses. Ici, vous pouvez voir un nom d’analyse et le nombre de fois où elle a abouti, échoué ou a été annulée au cours des 30 derniers jours.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Afficher l’état de l’analyse au fil du temps":::

4. Vous pouvez approfondir l’exploration d’une analyse spécifique en sélectionnant le **nom de l’analyse** pour vous connecter à l’historique d’analyse au sein de l’expérience **Data Map** d’Azure Purview. À partir de la page de l’historique des exécutions, vous pouvez obtenir l’ID d’exécution qui vous aidera à effectuer d’autres investigations.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Afficher les détails de l’analyse":::

5. Enfin, vous pouvez revenir à la page **État de l'analyse** en suivant les éléments de navigation dans le coin supérieur gauche de la page de l’historique des exécutions.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Afficher l’historique d’analyse"::: 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les **Insights** Azure Purview avec [Insights sur les données](./concept-insights.md)

* En savoir plus sur l’expérience **Data Map** d’Azure Purview avec [Gérer les sources de données](./manage-data-sources.md)
