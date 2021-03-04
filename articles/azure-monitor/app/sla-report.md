---
title: Classeur avec les temps d’arrêt, les contrats SLA et les interruptions - Application Insights
description: Calculez et indiquez le SLA pour les tests web via un seul et même volet pour vos ressources Application Insights et vos abonnements Azure.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714049"
---
# <a name="downtime-sla-and-outages-workbook"></a>Classeur avec les temps d’arrêt, les contrats SLA et les interruptions

Présentation d’un moyen simple de calculez et d’indiquer le SLA pour les tests web via un seul et même volet pour vos ressources Application Insights et vos abonnements Azure. Le rapport sur les temps d’arrêt et les interruptions offre de puissantes fonctionnalités et des visualisations de données prédéfinies pour améliorer votre compréhension de la connectivité de votre client, du temps de réponse habituel des applications et des temps d’arrêt.

Le modèle de classeur SLA est accessible via la Galerie de classeurs de votre ressource Application Insights ou via l’onglet Disponibilité en sélectionnant **Rapports SLA** en haut.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Capture d’écran de l’onglet Disponibilité avec des rapports SLA mis en surbrillance." lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Capture d’écran de la Galerie de classeurs avec le classeur des temps d’arrêt et des interruptions en surbrillance." lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Flexibilité des paramètres

Les paramètres définis dans le classeur influencent le reste de votre rapport.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" Capture d’écran de l’onglet des paramètres d’interruption/maintenance dans le classeur des temps d’arrêt et des interruptions." lightbox ="./media/sla-report/outages.png":::

Les paramètres `Subscriptions`, `App Insights Resources` et `Web Test` déterminent vos options générales pour les ressources. Ces paramètres sont basés sur des requêtes Log Analytics et sont utilisés dans chaque requête des rapports.

`Failure Threshold` et `Outage Window` vous permettent de déterminer vos propres critères pour une interruption de service, par exemple les critères de l’alerte de disponibilité d’Application Insights en fonction du nombre d’emplacements en échec sur une période choisie. Le seuil standard est de trois emplacements dans une fenêtre de cinq minutes.

`Maintenance Period` vous permet de sélectionner la fréquence de maintenance standard, et `Maintenance Window` est un sélecteur de date/heure pour un exemple de période de maintenance. Toutes les données qui sont générées au cours de la période identifiée seront ignorées dans vos résultats.

`Availability Target 9s` spécifie votre objectif de 9s cibles, de deux 9s à cinq 9s.

## <a name="overview-page"></a>Page Vue d’ensemble

La page Vue d’ensemble contient des informations générales sur votre SLA total (à l’exclusion des périodes de maintenance si elles sont définies), les instances d’interruption de bout en bout et les temps d’arrêt des applications. Les instances d’interruption sont définies comme ceci : le moment où un test commence à échouer jusqu’à ce qu’il réussisse, en fonction de vos paramètres pour les interruptions. Si un test commence à échouer à 8h00 et réussit à nouveau à 10h00, cette période de données tout entière est considérée comme étant la même interruption.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" Image GIF de la page Vue d’ensemble montrant le tableau de vue d’ensemble par test." lightbox="./media/sla-report/overview.gif":::

Vous pouvez également examiner votre interruption la plus longue qui s’est produite sur la période couverte par votre rapport.

Certains tests peuvent être liés en retour à leur ressource Application Insights pour une investigation plus poussée, mais cela n’est possible que dans la [ressource Application Insights basée sur un espace de travail](create-workspace-resource.md).

## <a name="downtime-outages-and-failures"></a>Temps d’arrêt, interruptions et défaillances

L’onglet **Interruptions et temps d’arrêt** contient des informations sur le nombre total d’instances d’interruption et le nombre total de temps d’arrêt décomposés par test. L’onglet **Défaillances par emplacement** a une carte géographique des emplacements de tests ayant échoué pour aider à identifier les zones de connexion ayant des problèmes potentiels.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" GIF de l’onglet Interruptions et temps d’arrêt et onglet Défaillances par emplacement dans le classeur Temps d’arrêt et interruptions." lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>Modifier le rapport

Vous pouvez modifier le rapport comme n’importe quel autre [classeur Azure Monitor](../visualize/workbooks-overview.md). Vous pouvez personnaliser les requêtes ou les visualisations en fonction des besoins de votre équipe.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" GIF de la sélection du bouton Modifier pour changer la visualisation en un graphique à secteurs." lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

Les requêtes peuvent toutes être exécutées dans [Log Analytics](../logs/log-analytics-overview.md) et être utilisées dans d’autres rapports ou tableaux de bord. Supprimez la restriction du paramètre et réutilisez la requête principale.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" GIF d’une requête de journal." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Accès et partage

Le rapport peut être partagé avec vos équipes ou votre direction, ou épinglées à un tableau de bord pour une utilisation ultérieure. L’utilisateur doit disposer d’une autorisation de lecture ou d’un accès à la ressource Application Insights où le classeur réel est stocké.

:::image type="content" source="./media/sla-report/share.png" alt-text=" Capture d’écran du partage de ce modèle." lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Étapes suivantes

- [Conseils d’optimisation des requêtes Log Analytics](../logs/query-optimization.md)
- Découvrez comment [créer un graphique dans les classeurs](../visualize/workbooks-chart-visualizations.md).
- Découvrez comment superviser votre site web avec des [tests de disponibilité](monitor-web-app-availability.md).