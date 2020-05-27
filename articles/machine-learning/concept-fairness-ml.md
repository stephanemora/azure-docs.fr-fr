---
title: Évaluer et atténuer l’impartialité dans les modèles d’apprentissage automatique
titleSuffix: Azure Machine Learning
description: Découvrez l’impartialité dans les modèles d’apprentissage automatique et la façon dont le package Python Fairlearn peut vous aider à créer des modèles plus impartiaux.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596358"
---
# <a name="fairness-in-machine-learning-models"></a>Impartialité dans les modèles d’apprentissage automatique

Découvrez l’impartialité dans l’apprentissage automatique et la façon dont le package Python open source Fairlearn peut vous aider à créer des modèles plus impartiaux.

## <a name="what-is-fairness-in-machine-learning-systems"></a>Qu’est-ce que l’impartialité dans les systèmes d’apprentissage automatique ?

Des systèmes d’intelligence artificielle et d’apprentissage automatique affichent parfois un comportement partial. On peut définir un comportement partial comme un comportement qui nuit à des personnes ou a une incidence sur celles-ci. Les systèmes d’intelligence artificielle peuvent occasionner de nombreux types de préjudices. Parmi les types courants de préjudices que peut occasionner l’intelligence artificielle, citons les deux suivants :

- Préjudice d’allocation : un système d’intelligence artificielle élargit ou restreint des opportunités, ressources ou informations. Il peut s’agir, par exemple, d’embauche, d’admission dans un établissement d’enseignement ou d’octroi de prêt, où un modèle pourrait être enclin à sélectionner des candidats dans un groupe spécifique de personnes, que dans d’autres groupes.

- Préjudice de qualité de service : un système d’intelligence artificielle ne fonctionne pas aussi bien pour un groupe de personnes que pour un autre. Par exemple, un système de reconnaissance vocale peut ne pas fonctionner aussi bien pour les femmes que pour les hommes.

Pour minimiser les comportements partiaux des systèmes d’intelligence artificielle, vous devez évaluer et atténuer ces préjudices.

>[!NOTE]
> L’impartialité est un défi socio-technique. De nombreux aspects de l’impartialité, tels que la justice et l’équité des processus, ne sont pas intégrés dans les métriques quantitatives d’impartialité. De même, de nombreuses métriques quantitatives d’impartialité ne peuvent pas être satisfaites simultanément. L’objectif est de permettre à un intervenant humain d’évaluer différentes stratégies d’atténuation de la partialité, puis de faire des compromis appropriés en fonction des circonstances.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Évaluation et atténuation de la partialité avec Fairlearn

Fairlearn est un package Python open source qui permet aux développeurs de systèmes d’apprentissage automatique d’évaluer l’impartialité de leurs systèmes et d’atténuer les problèmes de partialité observés.

Fairlearn intègre deux composantes :

- Tableau de bord d’évaluation : widget de bloc-notes Jupyter permettant d’évaluer la façon dont les prévisions d’un modèle affectent différents groupes. Il permet également de comparer plusieurs modèles à l’aide de métriques d’impartialité et de performances.
- Algorithmes d’atténuation : série d’algorithmes permettant d’atténuer la partialité en matière de classification et de régression binaires.

Ensemble, ces composants permettent aux scientifiques des données et aux dirigeants d’explorer divers compromis entre impartialité et performances, et de choisir la stratégie d’atténuation correspondant le mieux à leurs besoins.

## <a name="fairness-assessment"></a>Évaluation d’impartialité

Dans Fairlearn, l’impartialité est conceptualisée au travers d’une approche nommée **impartialité de groupe**, qui s’interroge sur les groupes de personnes exposés à un éventuel préjudice.

Les groupes pertinents, également appelés sous-populations, sont définis par des **caractéristiques sensibles** ou attributs sensibles. Ces caractéristiques sensibles sont transmises à un estimateur Fairlearn sous la forme d’un vecteur ou d’une matrice nommés `sensitive_features`. Cette dénomination suggère que le concepteur du système doit être sensible à ces caractéristiques lors de l’évaluation de l’impartialité du groupe. Il convient toujours de se demander si ces caractéristiques peuvent avoir une incidence sur la confidentialité découlant de la présence d’informations d’identification personnelle. Le mot « sensible » n’implique cependant pas que ces caractéristiques ne doivent pas être utilisées pour effectuer des prévisions.

Pendant la phase d’évaluation, l’impartialité est quantifiée à l’aide de métriques de disparité. Ces **métriques de disparité** permettent d’évaluer et de comparer le comportement du modèle dans différents groupes par le biais de ratios ou de différences. Fairlearn prend en charge deux classes de métriques de disparité :


- Disparité de performances du modèle : ces ensembles de métriques calculent la disparité (fluctuation) des valeurs d’une métrique de performances sélectionnée entre différents sous-groupes. Voici quelques exemples :

  - disparité de taux d’exactitude
  - disparité de taux d’erreurs
  - disparité de précision
  - disparité de rappel
  - disparité d’erreur absolue moyenne
  - et bien plus

- Disparité de taux de sélection : cette métrique exprime la différence de taux de sélection entre différents sous-groupes. Il s’agit, par exemple, d’une disparité de taux d’approbation de prêt. Le taux de sélection signifie la fraction de points de données dans chaque classe qui sont classifiés comme 1 (dans une classification binaire), ou la distribution de valeurs de prédiction (dans une régression).

## <a name="unfairness-mitigation"></a>Atténuation de partialité

### <a name="parity-constraints"></a>Contraintes de parité

Fairlearn inclut un vaste éventail d’algorithmes d’atténuation d’impartialité. Ces algorithmes prennent en charge un ensemble de contraintes déterminant le comportement du prédicteur, appelées **contraintes de parité** ou critères. Les contraintes de parité requièrent que certains aspects du comportement du prédicteur soient comparables dans les groupes que des caractéristiques sensibles définissent (par exemple, la nationalité). Les algorithmes d’atténuation de Fairlearn utilisent de telles contraintes de parité pour atténuer les problèmes de partialité observés.

Fairlearn prend en charge les types suivants de contraintes de parité suivants :

|Contrainte de parité  | Objectif  |Tâche d’apprentissage automatique  |
|---------|---------|---------|
|Parité démographique     |  Atténuer les préjudices d’allocation | Classification binaire, régression |
|Chances équilibrées  | Diagnostiquer les préjudice d’allocation et de qualité de service | Classification binaire        |
|Perte de groupe limité     |  Atténuer les préjudices de qualité de service | régression ; |

### <a name="mitigation-algorithms"></a>Algorithmes d’atténuation

Fairlearn fournit des algorithmes d’atténuation d’impartialité par réduction et post-traitement :

- Réduction : ces algorithmes prennent un estimateur d’apprentissage automatique de boîte noire standard (par exemple, un modèle LightGBM) et génèrent un ensemble de modèles reformés à l’aide d’une séquence de jeux de données d’apprentissage re-pondérés. Par exemple, les candidats d’un sexe peuvent être pondérés positivement ou négativement pour reformer des modèles et réduire les disparités entre groupes de sexe différent. Les utilisateurs peuvent ensuite choisir un modèle offrant le meilleur compromis entre l’exactitude (ou d’autres métriques de performances) et la disparité, qui devraient généralement reposer sur des règles métier et des calculs de coût.  
- Post-traitement  : ces algorithmes prennent un classifieur existant et la caractéristique sensible comme entrée. Ils dérivent ensuite une transformation de la prédiction du classifieur pour appliquer les contraintes d’impartialité spécifiées. Le principal avantage de l’optimisation de seuil réside dans sa simplicité et sa flexibilité, car elle n’a pas besoin de reformer le modèle. 

| Algorithm | Description | Tâche d’apprentissage automatique | Caractéristiques sensibles | Contraintes de parité prises en charge | Type d’algorithme |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Approche de la boîte noire pour une classification impartiale décrite dans le document [A Reductions Approach to Fair Classification](https://arxiv.org/abs/1803.02453) | Classification binaire | Par catégorie | [Parité démographique](#parity-constraints), [Chances équilibrées](#parity-constraints) | Réduction |
| `GridSearch` | Approche de la boîte noire décrite dans l’article [A Reductions Approach to Fair Classification](https://arxiv.org/abs/1803.02453)| Classification binaire | Binary | [Parité démographique](#parity-constraints), [Chances équilibrées](#parity-constraints) | Réduction |
| `GridSearch` | Approche de boîte noire qui implémente une variante formulaire de recherche de la Régression impartiale avec l’algorithme de perte de groupe limité décrit dans le document [Fair Regression: Quantitative Definitions and Reduction-based Algorithms](https://arxiv.org/abs/1905.12843) | régression ; | Binary | [Perte de groupe limité](#parity-constraints) | Réduction |
| `ThresholdOptimizer` | Algorithme de post-traitement basé sur le document [Equality of Opportunity in Supervised Learning](https://arxiv.org/abs/1610.02413). Cette technique prend comme entrée un classifieur et la caractéristique sensible, et dérive une transformation monotone de la prédiction du classifieur pour appliquer les contraintes de parité spécifiées. | Classification binaire | Par catégorie | [Parité démographique](#parity-constraints), [Chances équilibrées](#parity-constraints) | Post-traitement |

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment utiliser les différents composants, consultez le [Dépôt GitHub Fairlearn](https://github.com/fairlearn/fairlearn/) et les [exemples de bloc-notes](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- Découvrez la préservation de la confidentialité des données avec [Confidentialité différentielle et package WhisperNoise](concept-differential-privacy.md).