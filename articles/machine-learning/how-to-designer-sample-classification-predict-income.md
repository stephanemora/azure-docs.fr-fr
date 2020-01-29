---
title: 'Concepteur : Classifier, prédire un exemple de revenus'
titleSuffix: Azure Machine Learning
description: Suivez cet exemple pour créer un classifieur sans code afin de prédire des revenus avec le concepteur Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: bfae0d8eed80a88475c447a141097022fed9adff
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311110"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Générer un classifieur et utiliser une sélection de fonctionnalités afin de prédire des revenus avec le concepteur Azure Machine Learning

**Échantillon de concepteur 3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Apprenez à générer un classifieur d'apprentissage automatique à l'aide du concepteur, sans écrire la moindre ligne de code. Cet échantillon forme un **arbre de décision optimisé à deux classes** pour prédire le revenu du recensement pour adultes (> = 50 000 ou < = 50 000).

Comme cela répond à la question « Lequel ?», il s’agit d’un problème de classification. Toutefois, vous pouvez appliquer le même processus fondamental pour traiter n’importe quel type de problème d’apprentissage automatique : régression, classification, clustering, etc.

Voici le graphique final du pipeline pour cet échantillon :

![Graphique du pipeline](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Cliquez sur l’échantillon 3 pour l’ouvrir.



## <a name="data"></a>Données

Le jeu de données contient 14 fonctionnalités et une colonne d’étiquettes. Il existe plusieurs types de fonctionnalités, notamment numériques et catégoriques. Le diagramme suivant montre un extrait du jeu de données : ![données](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>Résumé du pipeline

Effectuez les étapes suivantes pour créer le pipeline :

1. Faites glisser le module de jeu de données Adult Census Income Binary dans le canevas du pipeline.
1. Ajoutez un module **Split Data** (Fractionner les données) pour créer les jeux de formation et de test. Définissez la fraction de lignes dans le premier jeu de données de sortie sur 0.7. Ce paramètre spécifie que 70 % des données seront sorties sur le port gauche du module et le reste sur le port droit. Nous utilisons le jeu de données de gauche pour la formation et celui de droite pour le test.
1. Ajoutez le module **Sélection de fonctionnalité basée sur le filtre** pour sélectionner 5 fonctionnalités par PearsonCorreclation. 
1. Ajoutez un module **Two-Class Boosted Decision Tree** (Arbre de décision optimisé à deux classes) pour initialiser un classifieur d’arbre de décision optimisé.
1. Ajoutez un module **Train Model** (Entraîner le modèle). Connectez le classifieur de l’étape précédente au port d’entrée gauche du module **Train Model** (Entraîner le modèle). Connectez le jeu de données filtré à partir du module de sélection de fonctionnalités basée sur le filtre en tant que jeu de données de formation.  Le module **Train Model** (Entraîner le modèle) va effectuer l’apprentissage du classifieur.
1. Ajoutez le module Sélectionner la transformation des colonnes et appliquer la transformation pour appliquer la même transformation (sélection de fonctionnalité basée sur des filtres) au jeu de données de test.
![apply-transformation](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. Ajoutez un module **Modèle de score** et connectez le module **Modèle de formation** à celui-ci. Ajoutez ensuite le jeu de test (la sortie du module Appliquer la transformation qui applique la sélection des fonctionnalités à l’ensemble de tests) au **Modèle de score**. Le module **Score Model** (Noter le modèle) effectuera les prédictions. Vous pouvez sélectionner son port de sortie pour afficher les prédictions et les probabilités de classe positive.


    Ce pipeline comporte deux modules de score : l’un à droite a une colonne d’étiquettes exclues avant d’effectuer la prédiction. Cette opération est préparée au déploiement d’un point de terminaison en temps réel, car l’entrée du service web attend uniquement des fonctionnalités qui ne sont pas étiquetées. 

1. Ajoutez un module **Evaluate Model** (Évaluer le modèle) et connectez le jeu de données noté à son port d’entrée gauche. Pour afficher les résultats de l’évaluation, sélectionnez le port de sortie du module **Evaluate Model** (Évaluer le modèle) et sélectionnez **Visualiser**.

## <a name="results"></a>Résultats

![Évaluer les résultats](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

Dans les résultats d’évaluation, vous pouvez voir que les courbes comme ROC sont, par exemple, des mesures de rappel de précision et de confusion. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres échantillons disponibles pour le concepteur :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemple 2 - Régression : comparer des algorithmes pour prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](how-to-designer-sample-classification-churn.md)
- [Exemple 6 - Classification : Prédire les retards de vols](how-to-designer-sample-classification-flight-delay.md)
- [Exemple 7 - Classification de texte : Jeu de données Wikipédia SP 500](how-to-designer-sample-text-classification.md)
