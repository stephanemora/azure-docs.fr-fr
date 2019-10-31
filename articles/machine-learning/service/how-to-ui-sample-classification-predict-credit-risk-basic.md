---
title: 'Exemple n°3 d’interface visuelle : Classification pour prédire le risque de crédit'
titleSuffix: Azure Machine Learning
description: Découvrez comment créer un classifieur d’apprentissage automatique sans écrire une seule ligne de code à l’aide de l’interface visuelle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 4649303b8ee643130b8e254f01bfffbe8ad9eb2b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693503"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Exemple 3 - Classification : Prédire le risque de crédit

Découvrez comment créer un classifieur d’apprentissage automatique sans écrire une seule ligne de code à l’aide de l’interface visuelle. Cet l’échantillon de pipeline est un **arbre de décision optimisé à deux classes** pour prédire le risque de crédit (élevé ou faible) selon les informations sur les applications de crédit (historique de crédit, âge et nombre de cartes de crédit, par exemple).

Comme cela répond à la question « Lequel ?», il s’agit d’un problème de classification. Toutefois, vous pouvez appliquer le même processus fondamental pour résoudre n’importe quel type de problème d’apprentissage automatique : régression, classification, clustering, etc.

Voici le graphique final du pipeline pour cet échantillon :

![Graphique du pipeline](media/how-to-ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le bouton **Ouvrir** pour l’échantillon de pipeline 3 :

    ![Ouvrir le pipeline](media/how-to-ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Exemple associé

[Exemple 4 - Classification : Prédiction du risque de crédit (sensible au coût)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md) fournit un pipeline avancé qui résout le même problème que cet échantillon. Il montre comment effectuer une classification *sensible au coût* à l’aide un module **Exécuter le Script Python** et compare les performances des deux algorithmes de classification binaires. Vous pouvez vous y reporter si vous souhaitez en savoir plus sur la création de pipelines de classification.


## <a name="data"></a>Données

L’exemple utilise le jeu de données German Credit Card (Carte de crédit allemande) issu du référentiel UC Irvine. Il contient 1 000 échantillons avec 20 fonctionnalités et 1 étiquette. Chaque exemple représente une personne. Les caractéristiques incluent des caractéristiques numériques et catégorielles. Consultez le [site web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) pour connaître la signification des caractéristiques catégorielles. La dernière colonne est l’étiquette, qui désigne le risque de crédit et a uniquement deux valeurs possibles : risque de crédit élevé = 2 et risque de crédit faible = 1.

## <a name="pipeline-summary"></a>Résumé du pipeline

Effectuez les étapes suivantes pour créer le pipeline :

1. Faites glisser le module du jeu de données Données UCI de cartes de crédit allemandes dans le canevas du pipeline.
1. Ajoutez un module **Modifier les métadonnées**, qui permet d’ajouter des noms explicites pour chaque colonne.
1. Ajoutez un module **Split Data** (Fractionner les données) pour créer les jeux de formation et de test. Définissez la fraction de lignes dans le premier jeu de données de sortie sur 0.7. Ce paramètre spécifie que 70 % des données seront sorties sur le port gauche du module et le reste sur le port droit. Nous utilisons le jeu de données de gauche pour la formation et celui de droite pour le test.
1. Ajoutez un module **Two-Class Boosted Decision Tree** (Arbre de décision optimisé à deux classes) pour initialiser un classifieur d’arbre de décision optimisé.
1. Ajoutez un module **Train Model** (Entraîner le modèle). Connectez le classifieur de l’étape précédente au port d’entrée gauche du module **Train Model** (Entraîner le modèle). Ajoutez le jeu d’apprentissage (port de sortie de gauche du module **Split Data** (Fractionner les données)) au port d’entrée de droite du module **Train Model** (Entraîner le modèle). Le module **Train Model** (Entraîner le modèle) va effectuer l’apprentissage du classifieur.
1. Ajoutez un module **Score Model** (Noter le modèle) et connectez le module **Train Model** (Former le modèle) à celui-ci. Puis, ajoutez le jeu de test (port de droite du module **Split Data** (Fractionner les données)) au module **Score Model** (Noter le modèle). Le module **Score Model** (Noter le modèle) effectuera les prédictions. Vous pouvez sélectionner son port de sortie pour afficher les prédictions et les probabilités de classe positive.
1. Ajoutez un module **Evaluate Model** (Évaluer le modèle) et connectez le jeu de données noté à son port d’entrée gauche. Pour afficher les résultats de l’évaluation, sélectionnez le port de sortie du module **Evaluate Model** (Évaluer le modèle) et sélectionnez **Visualiser**.

## <a name="results"></a>Résultats

![Évaluer les résultats](media/how-to-ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Dans les résultats de l’évaluation, vous pouvez voir que la valeur AUC du modèle est 0.776. Au seuil 0.5, la précision est de 0.621, le rappel est de 0.456 et le score F1 est de 0.526.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres exemples disponibles pour l’interface visuelle :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Exemple 2 - Régression : Comparer des algorithmes pour prédire le prix de véhicules automobiles](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](how-to-ui-sample-classification-predict-churn.md)
- [Exemple 6 - Classification : Prédire les retards de vols](how-to-ui-sample-classification-predict-flight-delay.md)
- [Exemple 7 - Classification de texte : Revues de livres](how-to-ui-sample-text-classification.md)