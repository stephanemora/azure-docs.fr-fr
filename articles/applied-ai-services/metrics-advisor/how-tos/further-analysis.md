---
title: Analyser plus en détail un incident et évaluer l’impact
titleSuffix: Azure Cognitive Services
description: Apprenez à tirer parti des outils d’analyse pour approfondir l’analyse d’un incident.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbullwin
ms.openlocfilehash: 0aefc4207f064550b41f9341e946cae2f1611961
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341403"
---
# <a name="further-analyze-an-incident-and-evaluate-impact"></a>Analyser plus en détail un incident et évaluer l’impact

## <a name="metrics-drill-down-by-dimensions"></a>Exploration des mesures par dimensions

Lorsque vous consultez des informations sur les incidents, il peut vous falloir plus de détails, par exemple, sur les différentes dimensions et les timestamps. Si vos données comportent une ou plusieurs dimensions, vous pouvez utiliser la fonction d’exploration au niveau du détail pour obtenir une vue plus détaillée. 

Pour utiliser la fonction d’exploration au niveau du détail, cliquez sur l’onglet **Exploration des métriques** dans le **Hub d’incidents**. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Exploration des métriques":::

Le paramètre **Dimensions** est une liste de dimensions pour un incident. Il est possible de sélectionner d’autres valeurs de dimension disponibles pour chacun d’eux. Après modification de la valeur de dimension Le paramètre **Timestamp** permet d’afficher l’incident actuel à différents moments dans le temps.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Sélection des options d’exploration et choix d’une dimension

Il existe deux types d’options d’exploration : **Exploration au niveau du détail** et **Comparaison horizontale**.

> [!Note]
> - Pour descendre dans la hiérarchie, vous pouvez explorer les données de différentes valeurs de dimension, à l’exception des dimensions actuellement sélectionnées. 
> - La comparaison horizontale permet d’explorer les données de différentes valeurs de dimension, à l’exception des dimensions Tout.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Dimension d’exploration":::

### <a name="value-comparison-for-different-dimension-values"></a>Comparaison de différentes valeurs de dimension

La deuxième section de l’onglet Exploration au niveau du détail est un tableau présentant des comparaisons pour différentes valeurs de dimension. Il comprend la valeur, la valeur de référence, la valeur de différence et la valeur delta, et indique s’il s’agit d’une anomalie.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Comparaison descendante" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Comparaisons entre les valeurs et les valeurs attendues pour différentes valeurs de dimension

La troisième section de l’onglet Exploration au niveau du détail est un histogramme comportant les valeurs et les valeurs attendues, pour différentes valeurs de dimension. L’histogramme est trié selon la différence entre la valeur et la valeur attendue. Il est facile de trouver la valeur inattendue présentant l’impact le plus important. Par exemple, dans l’illustration ci-dessus, nous pouvons constater que, à l’exception de la valeur Tout, c’est **US7** qui contribue le plus à l’anomalie.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Tableau d’exploration" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Visualisation de la valeur brute
La dernière partie de l’onglet Exploration au niveau du détail est un graphique en courbes des valeurs brutes. Ce graphique évite d’avoir à accéder à la page Métrique pour afficher les détails.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Graphique en courbes d’exploration" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="compare-time-series"></a>Comparaison de séries chronologiques

Lorsqu’une anomalie est détectée sur une série chronologique spécifique, il est parfois utile de la comparer à d’autres séries dans une seule visualisation. Cliquez sur l’onglet **Outils de comparaison**, puis cliquez sur le bouton bleu **+ Ajouter** . 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Ajout d’une série à comparer" lightbox="../media/diagnostics/add-series.png":::

Sélectionnez une série à partir de votre flux de données. Vous pouvez choisir la même granularité ou non. Sélectionnez les dimensions cibles et chargez la tendance de la série, puis cliquez sur **OK** pour la comparer à une série précédente. Les séries seront regroupées en une seule visualisation. Vous pouvez ajouter d’autres séries pour comparaison afin d’obtenir des insights supplémentaires. Cliquez sur le menu déroulant en haut de l’onglet **Outils de comparaison** pour comparer les données de la série chronologique sur une période décalée.  

> [!Warning]
> Pour effectuer une comparaison, l’analyse des données de série chronologique peut nécessiter des déplacements de point de données. La granularité de vos données doit donc la prendre en charge. Par exemple, si vos données sont hebdomadaires et que vous utilisez la comparaison **Jour par jour**, vous ne recevrez aucun résultat. Dans cet exemple, vous devrez utiliser la comparaison **Mois par mois**.

Après avoir sélectionné une comparaison décalée, vous pouvez choisir si vous souhaitez comparer les valeurs de données, les valeurs delta ou le pourcentage delta.

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Affichage d’anomalies similaires à l’aide du clustering de séries chronologiques

Lorsque vous affichez un incident, vous pouvez utiliser l’onglet **Clustering de séries chronologiques similaires** pour voir les différentes séries associées. Les séries d’un même groupe sont résumées ensemble. L’image ci-dessus nous apprend qu’il existe au moins deux groupes de séries. Cette fonctionnalité n’est disponible que si les conditions suivantes sont remplies :

- Les métriques doivent comporter une ou plusieurs dimensions ou valeurs de dimension.
- Les séries d’une même métrique doivent présenter une tendance similaire.

Les dimensions disponibles figurent dans la partie supérieure de l’onglet. Vous pouvez effectuer une sélection pour spécifier la série.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="Groupe de séries":::