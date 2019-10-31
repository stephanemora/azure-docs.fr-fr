---
title: 'Exemple d’interface visuelle n°3 : régression pour évaluer les prix et comparer les algorithmes'
titleSuffix: Azure Machine Learning
description: Cet article vous montre comment générer un pipeline de Machine Learning complexe à l’aide de l’interface visuelle, sans écrire une seule ligne de code. Découvrez comment effectuer l’apprentissage de plusieurs modèles de régression et comment les comparer pour prédire le prix d’une voiture en fonction de fonctionnalités techniques
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 651644eaae910792aac2144531d09afc4cde7153
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692787"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Exemple 2 - Régression : Algorithmes de prédiction de prix et de comparaison

Découvrez comment générer un pipeline de Machine Learning complexe à l’aide de l’interface visuelle, sans écrire une seule ligne de code. Cet exemple effectue l’apprentissage de plusieurs modèles de régression et les compare pour prédire le prix d’une voiture en fonction de fonctionnalités techniques. Nous allons fournir la logique pour les choix effectués dans ce pipeline, pour que vous puissiez résoudre vos propres problèmes de Machine Learning.

Si vous êtes débutant en Machine Learning, commencez par examiner la [version de base](how-to-ui-sample-regression-predict-automobile-price-basic.md) de ce pipeline.

Voici le graphique complet associé à ce pipeline :

[![Graphique du pipeline](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le bouton **Ouvrir** pour l’échantillon de pipeline 2 :

    ![Ouvrir le pipeline](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="pipeline-summary"></a>Résumé du pipeline

Pour générer le pipeline de Machine Learning, procédez comme suit :

1. Obtention des données.
1. Prétraitement des données.
1. Apprentissage du modèle.
1. Test, évaluation et comparaison des modèles.

## <a name="get-the-data"></a>Obtenir les données

Cet exemple utilise le jeu de données **Données sur le prix des véhicules automobiles (brutes)** du référentiel UCI Machine Learning. Ce jeu de données contient 26 colonnes avec des informations sur les véhicules automobiles, y compris la marque, le modèle, le prix, les fonctionnalités (telles que le nombre de cylindres), la consommation de carburant et un score de risque d’assurance.

## <a name="pre-process-the-data"></a>Prétraitement des données

Les principales tâches de préparation des données incluent leur nettoyage, intégration, transformation, réduction et discrétisation ou quantification. Dans l’interface visuelle, vous pouvez trouver des modules qui effectuent ces opérations, ainsi que d’autres tâches de prétraitement des données dans le groupe **Transformation des données** du volet gauche.

Utilisez le module **Sélectionner des colonnes dans le jeu de données** pour exclure les pertes normalisées qui ont de nombreuses valeurs manquantes. Nous utilisons ensuite le module **Nettoyage des données manquantes** pour supprimer les lignes qui ont des valeurs manquantes. Cela permet de créer un jeu de données d’apprentissage propre.

![Prétraitement des données](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Formation du modèle

Les problèmes d’apprentissage automatique varient. Les tâches d’apprentissage automatique courantes incluent la classification, le clustering, la régression et les systèmes de recommandation, chacun d’eux pouvant nécessiter un algorithme différent. Votre choix de l’algorithme dépend souvent de la configuration requise pour le cas d’usage. Après avoir sélectionné un algorithme, vous devez ajuster ses paramètres pour effectuer l’apprentissage d’un modèle plus précis. Vous devez ensuite évaluer tous les modèles en fonction des métriques telles que la précision, l’intelligibilité et l’efficacité.

Étant donné que l’objectif de ce pipeline est de prédire le prix des voitures, et puisque la colonne d’étiquette (prix) contient des nombres réels, le fait d’opter pour un modèle de régression est judicieux. Si l’on considère que le nombre de fonctionnalités est relativement bas (inférieur à 100), et que celles-ci ne sont pas éparpillées, la limite de décision est susceptible d’être non linéaire.

Pour comparer les performances de différents algorithmes, nous utilisons deux algorithmes non linéaires, **Régression d’arbre de décision optimisé** et **Régression de forêt d’arbres décisionnels**, pour générer des modèles. Les deux algorithmes ont des paramètres modifiables, mais cet échantillon utilise les valeurs par défaut dans ce pipeline.

Utilisez le module **Fractionner les données** pour diviser de façon aléatoire les données d’entrée, afin que le jeu de données d’apprentissage contienne 70 % des données d’origine et que le jeu de données de test contienne 30 % des données d’origine.

## <a name="test-evaluate-and-compare-the-models"></a>Test, évaluation et comparaison des modèles

Utilisez deux jeux de données choisies aléatoirement pour effectuer entraîner et tester le modèle, comme décrit dans la section précédente. Divisez le jeu de données et utilisez les différents jeux de données obtenus pour entraîner et tester le modèle, afin d’évaluer le modèle de manière plus objective.

Une fois que le modèle est entraîné, utilisez les modules **Noter le modèle** et **Évaluer le modèle** pour générer des résultats de prédiction et évaluer les modèles. Le module **Noter le modèle** génère des prédictions pour le jeu de données de test à l’aide du modèle formé. Passez ensuite les scores au module **Évaluer le modèle** pour générer des métriques d’évaluation.

Dans ce pipeline, vous utilisez deux instances du module **Évaluer le modèle** pour comparer deux paires de modèles.

Tout d’abord, comparez deux algorithmes sur le jeu de données d’entraînement.
Ensuite, comparez deux algorithmes sur le jeu de données de test.

Voici les résultats :

![Comparer les résultats](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Ces résultats montrent que le modèle créé avec **Régression d’arbre de décision optimisé** a un écart quadratique moyen inférieur à celui du modèle généré avec **Régression de forêt d’arbres décisionnels**.

Les deux algorithmes ont un écart inférieur sur le jeu de données d’apprentissage par rapport au jeu de données de test non consulté.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres exemples disponibles pour l’interface visuelle :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Exemple 3 - Classification : prédire le risque de crédit](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](how-to-ui-sample-classification-predict-churn.md)
- [Exemple 6 - Classification : Prédire les retards de vols](how-to-ui-sample-classification-predict-flight-delay.md)
- [Exemple 7 - Classification de texte : Revues de livres](how-to-ui-sample-text-classification.md)
