---
title: Sélectionner un algorithme d'apprentissage automatique
titleSuffix: Azure Machine Learning
description: Sélectionner des algorithmes Azure Machine Learning pour un apprentissage supervisé et non supervisé dans le cadre d'expériences de clustering, de classification ou de régression
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78328661"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Sélectionner des algorithmes Azure Machine Learning

Vous êtes nombreux à vous poser la question suivante : « Quel algorithme d'apprentissage automatique dois-je utiliser ? ». Le choix de l'algorithme repose principalement sur deux aspects distincts de votre scénario de science des données :

 - **Que voulez-vous faire avec vos données ?** Plus précisément, quelle est la question à laquelle vous souhaitez répondre à partir de vos données passées ?

 - **Quelles sont les exigences propres à votre scénario de science des données ?** Plus précisément, quels sont la précision, la durée d'apprentissage, la linéarité, le nombre de paramètres et le nombre de fonctionnalités pris en charge par votre solution ?

 ![Considérations relatives au choix des algorithmes : Que voulez-vous savoir ? Quelles sont les exigences propres au scénario ?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Scénarios d'entreprise et Aide-mémoire sur les algorithmes Machine Learning

L'[Aide-mémoire sur les algorithmes Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) vous permet de répondre à la première question : **Que voulez-vous faire avec vos données ?**  ? Utilisez l'Aide-mémoire sur les algorithmes Machine Learning pour rechercher la tâche que vous souhaitez effectuer, puis recherchez un [concepteur Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) pour la solution d'analyse prédictive. 

Le concepteur Machine Learning fournit une gamme complète d'algorithmes, tels que [Forêt d'arbres de décision multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), [Systèmes de recommandation](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), [Régression de réseau neuronal](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), [Réseau neuronal multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri) et [Clustering k-moyennes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri). Chaque algorithme est conçu pour traiter un type particulier de problème d'apprentissage automatique. Consultez [Informations de référence sur les algorithmes et les modules du concepteur Machine Learning](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri). Vous y trouverez une liste complète ainsi que de la documentation sur le fonctionnement de chaque algorithme et sur les paramètres à configurer pour optimiser l'algorithme.

> [!NOTE]
> Pour télécharger l'aide-mémoire relatif aux algorithmes d'apprentissage automatique, accédez à [Aide-mémoire sur les algorithmes Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Outre les conseils fournis par l'Aide-mémoire sur les algorithmes Azure Machine Learning, vous devez tenir compte d'autres exigences lors du choix d'un algorithme d'apprentissage automatique pour votre solution. Vous trouverez ci-dessous d'autres facteurs à prendre en compte, comme la précision, la durée d'apprentissage, la linéarité, le nombre de paramètres et le nombre de fonctionnalités.

## <a name="additional-requirements-for-a-data-science-scenario"></a>Exigences supplémentaires d'un scénario de science des données

Après avoir déterminé ce que vous voulez faire avec vos données, vous devez tenir compte d'autres exigences pour votre solution. 

Faites des choix, voire des compromis, pour les exigences suivantes :

- Précision
- Durée d’apprentissage
- Linéarité
- Nombre de paramètres
- Nombre de fonctionnalités

## <a name="accuracy"></a>Précision

Dans le domaine de l'apprentissage automatique, la précision mesure l'efficacité d'un modèle sous forme de proportion de résultats réels sur le nombre total de cas. Dans le concepteur Machine Learning, le [module Évaluer un modèle](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) calcule un ensemble de mesures d'évaluation normalisées. Vous pouvez utiliser ce module pour mesurer la précision d'un modèle formé.

Il n'est pas toujours nécessaire d'obtenir la réponse la plus exacte possible. Parfois, en fonction de votre utilisation, une approximation suffit. Dans ce cas, vous réduirez probablement considérablement le temps de traitement en ayant recours à des méthodes plus approximatives. Les méthodes approximatives tendent aussi naturellement à éviter le surajustement.

Trois méthodes d'utilisation du module Évaluer un modèle sont disponibles :

- Générer des scores sur vos données d'apprentissage afin d'évaluer le modèle
- Générer des scores sur le modèle, mais comparer ces scores à ceux d’un jeu de tests réservé
- Comparer les scores de deux modèles différents mais liés, en utilisant le même jeu de données

Pour obtenir la liste complète des mesures et approches disponibles afin d'évaluer la précision des modèles Machine Learning, consultez [Module Évaluer un modèle](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Durée d’apprentissage

Dans le cadre d'un apprentissage supervisé, la formation consiste à utiliser des données historiques pour créer un modèle Machine Learning qui réduit les risques d'erreurs. Le nombre de minutes ou d'heures nécessaires pour l'apprentissage d'un modèle varie beaucoup selon les algorithmes. La durée d'apprentissage est souvent étroitement liée à la précision : l'une accompagne généralement l'autre. 

En outre, certains algorithmes sont plus sensibles au nombre de points de données que d'autres. Vous pouvez être amené à choisir un algorithme spécifique parce que le temps dont vous disposez est limité, en particulier lorsque le jeu de données est volumineux.

Dans le concepteur Machine Learning, la création et l'utilisation d'un modèle Machine Learning s'effectuent généralement en trois étapes :

1.  Configurez un modèle en choisissant un type d'algorithme particulier et en définissant ses paramètres ou hyperparamètres. 

2.  Fournissez un jeu de données doté d'une étiquette et dont les données sont compatibles avec l'algorithme. Connectez les données et le modèle au module [Former le modèle](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  Au terme de la formation, utilisez le modèle formé avec l'un des [modules de scoring](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) pour effectuer des prédictions sur de nouvelles données.

## <a name="linearity"></a>Linéarité

Dans les domaines des statistiques et de l'apprentissage automatique, le terme « linéarité » signifie qu'il existe une relation linéaire entre une variable et une constante de votre jeu de données. Par exemple, les algorithmes de classification linéaires supposent que les classes peuvent être séparées par une ligne droite (ou son analogue de dimension supérieure).

Un grand nombre d'algorithmes d'apprentissage automatique utilisent la linéarité. Dans le concepteur Azure Machine Learning, ces algorithmes sont les suivants : 

- [Régression logistique multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Régression logistique à deux classes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Machines à vecteurs de support](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Les algorithmes de régression linéaire supposent que les tendances des données suivent une ligne droite. Ce postulat convient à certains problèmes, mais pour d'autres, il réduit la précision. Malgré leurs inconvénients, les algorithmes linéaires constituent une première stratégie très prisée. Ils ont tendance à être des algorithmes simples et à apprentissage rapide.

![Limite de classe non linéaire](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Limite de classe non linéaire*** : *l'utilisation d'un algorithme de classification linéaire nuirait à la précision.*

![Données avec une tendance non linéaire](./media/how-to-select-algorithms/nonlinear-trend.png)

***Données avec une tendance non linéaire*** : *l'utilisation d'une méthode de régression linéaire entraînerait trop d'erreurs.*

## <a name="number-of-parameters"></a>Nombre de paramètres

Les paramètres sont les boutons que les scientifiques des données règlent lorsqu’ils configurent un algorithme. Ce sont des nombres qui affectent le comportement de l'algorithme, comme la tolérance aux erreurs ou le nombre d'itérations, ou les variantes du comportement de l'algorithme. La durée d'apprentissage et la précision de l'algorithme peuvent parfois dépendre du choix des paramètres appropriés. En règle générale, les algorithmes avec des paramètres à grands nombres nécessitent plus d’essais pour trouver la bonne combinaison.

Le concepteur Machine Learning comprend également le [module Optimiser les hyperparamètres](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) : L'objectif de ce module est de déterminer les hyperparamètres optimaux pour un modèle Machine Learning. Le module génère et teste plusieurs modèles avec différentes combinaisons de paramètres. Il compare les métriques à tous les modèles pour obtenir les combinaisons de paramètres. 

Même si c'est un excellent moyen de vous assurer que vous avez examiné l'espace de paramétrage, le temps nécessaire pour former un modèle augmente de façon exponentielle avec le nombre de paramètres. Avoir de nombreux paramètres indique généralement qu'un algorithme a une plus grande flexibilité. Cette méthode donne souvent une excellente précision, une fois que vous avez trouvé la bonne combinaison de paramètres.

## <a name="number-of-features"></a>Nombre de fonctionnalités

Dans le domaine de l'apprentissage automatique, une fonctionnalité est une variable quantifiable du phénomène que vous essayez d'analyser. Pour certains types de données, le nombre de fonctionnalités peut être très important par rapport au nombre de points de données. C'est souvent le cas avec les données génétiques ou textuelles. 

La présence d'un grand nombre de fonctionnalités peut ralentir certains algorithmes d'apprentissage et se traduire par une durée d'apprentissage ingérable. Les [machines à vecteurs de support](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) sont particulièrement bien adaptées aux scénarios comportant un grand nombre de fonctionnalités. C'est la raison pour laquelle elles sont utilisées dans de nombreuses applications, de la récupération d'informations à la classification de textes et d'images. Les machines à vecteurs de support peuvent être utilisées pour les tâches de classification et de régression.

Le terme « sélection de fonctionnalités » fait référence au processus d'application de tests statistiques à des entrées, en fonction d'une sortie donnée. L’objectif est de déterminer quelles colonnes prédisent le mieux la sortie. Dans le concepteur Machine Learning, le [module Sélection de fonctionnalités par filtrage](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) permet de choisir parmi plusieurs algorithmes de sélection de fonctionnalités. Le module inclut des méthodes de corrélation telles que les valeurs de corrélation de Pearson et de test du khi-deux.

Vous pouvez également utiliser le [module Importance de la fonctionnalité de permutation](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) pour calculer un ensemble de scores d'importance des fonctionnalités pour votre jeu de données. Vous pouvez ensuite vous aider de ces scores pour déterminer les meilleures fonctionnalités à utiliser dans un modèle.


## <a name="next-steps"></a>Étapes suivantes

 - [En savoir plus sur le concepteur Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Un descriptif de tous les algorithmes d'apprentissage automatique disponibles dans le concepteur Azure Machine Learning est fourni dans [Informations de référence sur les algorithmes et les modules du concepteur Machine Learning](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - Pour en savoir plus sur la relation entre le Deep Learning, le Machine Learning et l'IA, consultez [Deep Learning et Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
