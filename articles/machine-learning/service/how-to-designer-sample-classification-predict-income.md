---
title: 'Concepteur : Classifier, prédire des revenus'
titleSuffix: Azure Machine Learning
description: Découvrez comment générer un classifieur de Machine Learning sans écrire une seule ligne de code à l’aide du concepteur (préversion).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 383cbc11955598505730a4613c50536afac75f95
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647977"
---
# <a name="sample-3---classification-with-feature-selection-income-prediction"></a>Échantillon 3 - Classification avec sélection des caractéristiques : Prédiction des revenus
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Découvrez comment générer un classifieur de Machine Learning sans écrire une seule ligne de code à l’aide du concepteur (préversion). Cet échantillon forme un **arbre de décision optimisé à deux classes** pour prédire le revenu du recensement pour adultes (> = 50 000 ou < = 50 000).

Comme cela répond à la question « Lequel ?», il s’agit d’un problème de classification. Toutefois, vous pouvez appliquer le même processus fondamental pour résoudre n’importe quel type de problème d’apprentissage automatique : régression, classification, clustering, etc.

Voici le graphique final du pipeline pour cet échantillon :

![Graphique du pipeline](media/how-to-ui-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Cliquez sur l’échantillon 3 pour l’ouvrir.



## <a name="data"></a>Données

Le jeu de données contient 14 fonctionnalités et une colonne d’étiquettes. Il existe plusieurs types de fonctionnalités, notamment numériques et catégoriques. Le diagramme suivant montre un extrait du jeu de données : ![données](media/how-to-ui-sample-classification-predict-income/data.png)



## <a name="pipeline-summary"></a>Résumé du pipeline

Effectuez les étapes suivantes pour créer le pipeline :

1. Faites glisser le module de jeu de données Adult Census Income Binary dans le canevas du pipeline.
1. Ajoutez un module **Split Data** (Fractionner les données) pour créer les jeux de formation et de test. Définissez la fraction de lignes dans le premier jeu de données de sortie sur 0.7. Ce paramètre spécifie que 70 % des données seront sorties sur le port gauche du module et le reste sur le port droit. Nous utilisons le jeu de données de gauche pour la formation et celui de droite pour le test.
1. Ajoutez le module **Sélection de fonctionnalité basée sur le filtre** pour sélectionner 5 fonctionnalités par PearsonCorreclation. 
1. Ajoutez un module **Two-Class Boosted Decision Tree** (Arbre de décision optimisé à deux classes) pour initialiser un classifieur d’arbre de décision optimisé.
1. Ajoutez un module **Train Model** (Entraîner le modèle). Connectez le classifieur de l’étape précédente au port d’entrée gauche du module **Train Model** (Entraîner le modèle). Connectez le jeu de données filtré à partir du module de sélection de fonctionnalités basée sur le filtre en tant que jeu de données de formation.  Le module **Train Model** (Entraîner le modèle) va effectuer l’apprentissage du classifieur.
1. Ajoutez le module Sélectionner la transformation des colonnes et appliquer la transformation pour appliquer la même transformation (sélection de fonctionnalité basée sur des filtres) au jeu de données de test.
![apply-transformation](media/how-to-ui-sample-classification-predict-income/transformation.png)
1. Ajoutez un module **Modèle de score** et connectez le module **Modèle de formation** à celui-ci. Ajoutez ensuite le jeu de test (la sortie du module Appliquer la transformation qui applique la sélection des fonctionnalités à l’ensemble de tests) au **Modèle de score**. Le module **Score Model** (Noter le modèle) effectuera les prédictions. Vous pouvez sélectionner son port de sortie pour afficher les prédictions et les probabilités de classe positive.


    Ce pipeline comporte deux modules de score : l’un à droite a une colonne d’étiquettes exclues avant d’effectuer la prédiction. Cette opération est préparée au déploiement d’un point de terminaison en temps réel, car l’entrée du service web attend uniquement des fonctionnalités qui ne sont pas étiquetées. 

1. Ajoutez un module **Evaluate Model** (Évaluer le modèle) et connectez le jeu de données noté à son port d’entrée gauche. Pour afficher les résultats de l’évaluation, sélectionnez le port de sortie du module **Evaluate Model** (Évaluer le modèle) et sélectionnez **Visualiser**.

## <a name="results"></a>Résultats

![Évaluer les résultats](media/how-to-ui-sample-classification-predict-income/evaluate-result.png)

Dans les résultats d’évaluation, vous pouvez voir que les courbes comme ROC sont, par exemple, des mesures de rappel de précision et de confusion. 

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres échantillons disponibles pour le concepteur :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemple 2 - Régression : Comparer des algorithmes pour prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](how-to-designer-sample-classification-churn.md)
- [Exemple 6 - Classification : Prédire les retards de vols](how-to-designer-sample-classification-flight-delay.md)
- [Exemple 7 - Classification de texte : Jeu de données Wikipédia SP 500](how-to-designer-sample-text-classification.md)
