---
title: 'Exemple n°3 d’interface visuelle : Classification pour prédire le risque de crédit'
titleSuffix: Azure Machine Learning service
description: Découvrez comment créer un classifieur d’apprentissage automatique sans écrire une seule ligne de code à l’aide de l’interface visuelle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 7565f94910d0e926682a72af42b02059fe7295ea
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990051"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Exemple 3 - Classification : Prédire le risque de crédit

Découvrez comment créer un classifieur d’apprentissage automatique sans écrire une seule ligne de code à l’aide de l’interface visuelle. Cet exemple effectue l’apprentissage d’un **arbre de décision optimisé à deux classes** pour prédire le risque de crédit (élevé ou faible) selon les informations sur les demandes de crédit (historique de crédit, âge et nombre de cartes de crédit, par exemple).

Étant donné que nous essayons de répondre à la question « Lequel ? », il s’agit d’un problème de classification. Toutefois, vous pouvez appliquer le même processus fondamental pour résoudre n’importe quel type de problème d’apprentissage automatique : régression, classification, clustering, etc.

Voici le graphique complet associé à cette expérience :

![Graphique de l’expérience](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le bouton **Ouvrir** pour accéder à l’exemple d’expérience 3 :

    ![Ouvrir l’expérience](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Exemple associé

[L’exemple 4 - Classification : Prédire le risque de crédit (sensible au coût)](ui-sample-classification-predict-credit-risk-cost-sensitive.md) fournit une expérience avancée qui résout le même problème que celui faisant l’objet de cette expérience. Il montre comment effectuer une classification _sensible au coût_ à l’aide un module **Exécuter le Script Python** et compare les performances des deux algorithmes de classification binaires. Vous pouvez vous y reporter si vous souhaitez en savoir plus sur la création d’expériences de classification.

## <a name="data"></a>Données

Nous utilisons le jeu de données German Credit Card (Carte de crédit allemande) à partir du référentiel UC Irvine.
Le jeu de données contient 1 000 exemples avec 20 caractéristiques et 1 étiquette. Chaque exemple représente une personne. Les caractéristiques incluent des caractéristiques numériques et catégorielles. Consultez le [site web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) pour connaître la signification des caractéristiques catégorielles. La dernière colonne est l’étiquette, qui désigne le risque de crédit et a uniquement deux valeurs possibles : risque de crédit élevé = 2 et risque de crédit faible = 1.

## <a name="experiment-summary"></a>Résumé de l’expérience

Effectuez les étapes suivantes pour créer l’expérience :

1. Faites glisser le module du jeu de données German Credit Card UCI Data (Données UCI de carte de crédit allemande) dans le canevas de l’expérience.
1. Ajoutez un module **Modifier les métadonnées**, qui permet d’ajouter des noms explicites pour chaque colonne.
1. Ajoutez un module **Split Data** (Fractionner les données) pour créer les jeux de formation et de test. Définissez la fraction de lignes dans le premier jeu de données de sortie sur 0.7. Ce paramètre spécifie que 70 % des données seront sorties sur le port gauche du module et le reste sur le port droit. Nous utilisons le jeu de données de gauche pour la formation et celui de droite pour le test.
1. Ajoutez un module **Two-Class Boosted Decision Tree** (Arbre de décision optimisé à deux classes) pour initialiser un classifieur d’arbre de décision optimisé.
1. Ajoutez un module **Train Model** (Entraîner le modèle). Connectez le classifieur de l’étape précédente au port d’entrée gauche du module **Train Model** (Entraîner le modèle). Ajoutez le jeu d’apprentissage (port de sortie de gauche du module **Split Data** (Fractionner les données)) au port d’entrée de droite du module **Train Model** (Entraîner le modèle). Le module **Train Model** (Entraîner le modèle) va effectuer l’apprentissage du classifieur.
1. Ajoutez un module **Score Model** (Noter le modèle) et connectez le module **Train Model** (Former le modèle) à celui-ci. Puis, ajoutez le jeu de test (port de droite du module **Split Data** (Fractionner les données)) au module **Score Model** (Noter le modèle). Le module **Score Model** (Noter le modèle) effectuera les prédictions. Vous pouvez sélectionner son port de sortie pour afficher les prédictions et les probabilités de classe positive.
1. Ajoutez un module **Evaluate Model** (Évaluer le modèle) et connectez le jeu de données noté à son port d’entrée gauche. Pour afficher les résultats de l’évaluation, sélectionnez le port de sortie du module **Evaluate Model** (Évaluer le modèle) et sélectionnez **Visualiser**.

Voici le graphique complet de l’expérience :

![Graphique de l’expérience](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>Résultats

![Évaluer les résultats](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Dans les résultats de l’évaluation, vous pouvez voir que la valeur AUC du modèle est 0.776. Au seuil 0.5, la précision est de 0.621, le rappel est de 0.456 et le score F1 est de 0.526.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres exemples disponibles pour l’interface visuelle :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemple 2 - Régression : Comparer des algorithmes pour prédire le prix de véhicules automobiles](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](ui-sample-classification-predict-churn.md)
- [Exemple 6 - Classification : Prédire les retards de vols](ui-sample-classification-predict-flight-delay.md)
