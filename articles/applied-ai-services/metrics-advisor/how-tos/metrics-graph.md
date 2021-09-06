---
title: Graphe de métriques de Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Guide pratique pour configurer votre graphe de métriques et visualiser les anomalies associées dans vos données.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: 7842cdd6d5336e90346d666d8c2d31d20c19caa0
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341244"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Procédure : Créer un graphe de métriques pour analyser les métriques associées

Chaque série chronologique de Metrics Advisor est analysée séparément par un modèle qui apprend à partir de données historiques pour prédire les tendances futures. Des anomalies seront détectées si un point de données ne correspond pas au modèle historique. Toutefois, il peut arriver que plusieurs métriques soient liées les unes aux autres et qu’il soit nécessaire d’analyser les anomalies sur plusieurs métriques. Le **Graphe de métriques** est l’outil qui peut vous être utile. 

Par exemple, si vous disposez de plusieurs métriques qui surveillent votre activité dans diverses perspectives, la détection des anomalies s’appliquera respectivement. Toutefois, dans des cas métiers réels, les anomalies détectées sur plusieurs métriques peuvent être liées entre elles, il peut alors être utile de découvrir ces relations et d’analyser la cause racine lors de la résolution de problèmes réels. Le graphe de métriques permet de mettre automatiquement en corrélation les anomalies détectées sur les métriques associées pour accélérer le processus de résolution des problèmes. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Sélection d’une métrique pour placer le premier nœud dans le graphe

Cliquez sur l’onglet **Graphe de métriques** dans la barre de navigation. La première étape de création d’un graphe de métriques consiste à placer un nœud sur le graphe. Sélectionnez un flux de données et une métrique en haut de la page. Un nœud apparaît dans le volet inférieur. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Sélectionner la métrique":::

## <a name="add-a-noderelation-on-existing-node"></a>Ajout d’un nœud/d’une relation sur un nœud existant

Ensuite, vous devez ajouter un autre nœud et spécifier une relation avec un ou plusieurs nœuds existants. Sélectionnez un nœud existant et cliquez dessus avec le bouton droit. Le menu contextuel qui s’affiche propose plusieurs options. 

Sélectionnez **Ajouter une relation** : vous pouvez choisir une autre métrique et spécifier le type de relation entre les deux nœuds. Vous pouvez également appliquer des filtres de dimension spécifiques. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Ajout d’un nœud et d’une relation":::

Après avoir répété les étapes ci-dessus, vous disposerez d’un graphe de métriques décrivant les relations entre toutes les métriques associées.

Vous pouvez effectuer d’autres actions sur le graphe : 
1.  Supprimer un nœud
2.  Accéder aux métriques
3.  Accéder au hub d’incidents
4.  Développez
5.  Supprimer une relation

## <a name="legend-of-metrics-graph"></a>Légende du graphe de métriques

Chaque nœud du graphe représente une métrique. Il y a quatre genres de nœuds dans le graphe de métriques :

-  **Nœud vert** : le nœud indiquant que la gravité de l’incident de la métrique actuelle est faible.
- **Nœud orange** : le nœud indiquant que la gravité de l’incident de la métrique actuelle est moyenne.
- **Nœud rouge** : le nœud indiquant que la gravité de l’incident de la métrique actuelle est élevée.
- **Nœud bleu** : le nœud sans gravité de l’anomalie.


## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Affichage de l’état d’anomalie des métriques associées dans le hub d’incidents

Lorsque le graphe de métriques est créé, chaque fois qu’une anomalie est détectée sur ses métriques, vous pouvez afficher l’état de l’anomalie et obtenir une vue d’ensemble de l’incident. 

Cliquez sur un incident au sein du graphe et faites défiler jusqu’à **Analyse croisée des métriques**, sous les informations de diagnostic.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Affichage des métriques et anomalies associées":::

## <a name="next-steps"></a>Étapes suivantes

- [Ajustement de la détection d’anomalie selon les commentaires](anomaly-feedback.md)
- [Diagnostiquer un incident](diagnose-an-incident.md).
- [Configurer des métriques et affiner la configuration de la détection](configure-metrics.md)