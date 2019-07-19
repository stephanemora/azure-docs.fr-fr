---
title: Supervision de la dérive des données (préversion)
titleSuffix: Azure Machine Learning service
description: Découvrez comment Azure Machine Learning service peut superviser la dérive des données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: a03e3124647869e7148f271810bb523986a851c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442378"
---
# <a name="what-is-data-drift-monitoring-preview"></a>Présentation de la supervision de la dérive des données (préversion)

La dérive des données est la modification de la distribution des données. Dans le contexte du Machine Learning, des modèles Machine Learning entraînés peuvent subir une dégradation des performances de prédiction à cause de la dérive. La supervision de la dérive entre les données d’entraînement et les données utilisées pour effectuer les prédictions peut aider à détecter les problèmes de performance.

La qualité des modèles Machine Learning dépend entièrement des données utilisées pour les entraîner. Déployer des modèles en production sans superviser leurs performances peut les affecter de manière négative sans que les problèmes soient détectés. Grâce à la supervision de la dérive des données, vous pouvez détecter la dérive des données et vous y adapter. 

## <a name="when-to-monitor-for-data-drift"></a>Quand superviser la dérive des données ?

Les métriques suivantes peuvent être supervisées :

+ Étendue de la dérive (coefficient de dérive)
+ Cause de la dérive (contribution à la dérive par fonctionnalité)
+ Mesures de distance (Wasserstein, énergie, etc.)

En mettant en place cette supervision, vous pouvez configurer des alertes ou des actions lorsqu’une dérive est détectée. De plus, les spécialistes des données peuvent examiner la cause racine du problème. 

Si vous pensez que les données d’entrée pour votre modèle déployé sont susceptibles de changer, vous devez envisager d’utiliser la détection de la dérive des données.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Supervision de la dérive des données dans Azure Machine Learning service

Avec **Azure Machine Learning service**, la dérive de données est supervisée par le biais de jeux de données ou de déploiements. Pour superviser la dérive des données, un jeu de données de référence (généralement le jeu de données d’entraînement d’un modèle) est spécifié. Un deuxième jeu de données (généralement les données d’entrée du modèle qui ont été collectées à partir d’un déploiement) est testé en le comparant avec le jeu de données de référence. Les deux jeux de données sont [profilés](how-to-explore-prepare-data.md#explore-with-summary-statistics) et sont utilisés comme entrée pour le service de supervision de la dérive des données. Un modèle Machine Learning est entraîné pour détecter les différences entre les deux jeux de données. Les performances du modèle sont converties pour établir un coefficient dérive mesurant l’étendue de la dérive entre les deux jeux de données. Grâce à l’[interprétabilité des modèles](machine-learning-interpretability-explainability.md), les fonctionnalités qui ont contribué au coefficient de dérive sont calculées. De nombreuses informations (profil du jeu de données, données statistiques, etc.) relatives à chaque fonctionnalité sont suivies. 

## <a name="data-drift-metric-output"></a>Résultat de la métrique de la dérive des données

Il existe plusieurs manières d’afficher les métriques de la dérive :

* À l’aide du widget Jupyter ;
* À l’aide de la fonction `get_metrics()` sur n’importe quel objet `datadriftRun`.
* Afficher les métriques dans le portail Azure sur votre modèle

Les métriques suivantes sont enregistrées dans chaque itération d’exécution pour une tâche de dérive des données :

|Métrique|Description|
--|--|
wasserstein_distance|Distance statistique définie pour la distribution numérique unidimensionnelle.|
energy_distance|Distance statistique définie pour la distribution numérique unidimensionnelle.|
datadrift_coefficient|Également appelé le « Matthews correlation coefficient » (coefficient de corrélation de Matthews), il s’agit d’un nombre réel compris entre -1 et 1. Dans le contexte d’une dérive, 0 indique qu’il n’y a aucune dérive et 1 indique une dérive maximale.|
datadrift_contribution|Importance des caractéristiques des fonctionnalités contribuant à la dérive.|

## <a name="next-steps"></a>Étapes suivantes

Consultez les exemples et découvrez comment superviser la dérive des données :

+ [Découvrez comment superviser la dérive des données des modèles déployés via Azure Kubernetes Service (AKS)](how-to-monitor-data-drift.md)
+ Essayez les [exemples de Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)