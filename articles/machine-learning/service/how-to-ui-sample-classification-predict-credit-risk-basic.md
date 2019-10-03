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
ms.date: 05/10/2019
ms.openlocfilehash: b9d5308a0b7d9249ea816bafb5c6cb7d9c5e5fd6
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131240"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Exemple 3 - Classification : Prédire le risque de crédit

Découvrez comment créer un classifieur d’apprentissage automatique sans écrire une seule ligne de code à l’aide de l’interface visuelle. Cet exemple effectue l’apprentissage d’un **arbre de décision optimisé à deux classes** pour prédire le risque de crédit (élevé ou faible) selon les informations sur les demandes de crédit (historique de crédit, âge et nombre de cartes de crédit, par exemple).

Comme cela répond à la question « Lequel ?», il s’agit d’un problème de classification. Toutefois, vous pouvez appliquer le même processus fondamental pour résoudre n’importe quel type de problème d’apprentissage automatique : régression, classification, clustering, etc.

Voici le graphe final de l’expérience pour cet exemple :

![Graphique de l’expérience](media/how-to-ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le bouton **Ouvrir** pour accéder à l’exemple d’expérience 3 :

    ![Ouvrir l’expérience](media/how-to-ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Exemple associé

[L’exemple 4 - Classification : Prédire le risque de crédit (sensible au coût)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md) fournit une expérience avancée qui résout le même problème que celui faisant l’objet de cette expérience. Il montre comment effectuer une classification *sensible au coût* à l’aide un module **Exécuter le Script Python** et compare les performances des deux algorithmes de classification binaires. Vous pouvez vous y reporter si vous souhaitez en savoir plus sur la création de pipelines de classification.

## <a name="data"></a>Données

L’exemple utilise le jeu de données German Credit Card (Carte de crédit allemande) issu du référentiel UC Irvine.
Le jeu de données contient 1 000 exemples avec 20 caractéristiques et 1 étiquette. Chaque exemple représente une personne. Les caractéristiques incluent des caractéristiques numériques et catégorielles. Consultez le [site web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) pour connaître la signification des caractéristiques catégorielles. La dernière colonne est l’étiquette, qui désigne le risque de crédit et a uniquement deux valeurs possibles : risque de crédit élevé = 2 et risque de crédit faible = 1.

## <a name="experiment-summary"></a>Résumé de l’expérience

Effectuez les étapes suivantes pour créer l’expérience :

1. Faites glisser le module du jeu de données German Credit Card UCI Data (Données UCI de carte de crédit allemande) dans le canevas de l’expérience.
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
