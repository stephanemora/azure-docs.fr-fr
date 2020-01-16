---
title: 'Concepteur : Exemple de prédiction des prix des véhicules (de base)'
titleSuffix: Azure Machine Learning
description: Créez un modèle de régression Machine Learning pour prédire le prix d’un véhicule automobile sans écrire une seule ligne de code grâce au concepteur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 156b963fc1644d1f863d8ddd1d86c15b311e18a8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763383"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>Utiliser la régression pour prédire les prix des voitures à l’aide du concepteur Azure Machine Learning

**Exemple 1 du concepteur (préversion)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Découvrez comment générer un modèle de régression Machine Learning sans écrire une seule ligne de code à l’aide du concepteur (préversion).

Ce pipeline permet d’effectuer l'apprentissage d’un **régresseur linéaire** pour prédire le prix d’une voiture selon des fonctionnalités techniques, tels que la marque, le modèle, la puissance et la taille. Étant donné que vous essayez de répondre à la question « Combien ? », il s’agit d’un problème de régression. Toutefois, vous pouvez appliquer les mêmes étapes de base dans cet exemple pour résoudre n’importe quel autre type de problème d’apprentissage automatique : régression, classification, clustering, etc.

Les étapes fondamentales d’un modèle Machine Learning sont les suivantes :

1. Obtenir les données
1. Prétraitement des données
1. Effectuer l’apprentissage du modèle
1. Évaluer le modèle

Voici le graphique final terminé du pipeline. Cet article fournit le raisonnement pour tous les modules afin de prendre des décisions similaires concernant le vôtre.

![Graphique du pipeline](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Cliquez sur l’échantillon 1 pour l’ouvrir.


## <a name="get-the-data"></a>Obtenir les données

Cet exemple utilise le jeu de données **Données sur le prix des véhicules automobiles (brutes)** du référentiel UCI Machine Learning. Ce jeu de données contient 26 colonnes avec des informations sur les véhicules automobiles, y compris la marque, le modèle, le prix, les fonctionnalités (telles que le nombre de cylindres), la consommation de carburant et un score de risque d’assurance. L’objectif de cet échantillon est de prédire le prix de la voiture.

## <a name="pre-process-the-data"></a>Prétraitement des données

Les principales tâches de préparation des données incluent leur nettoyage, intégration, transformation, réduction et discrétisation ou quantification. Dans le concepteur, vous pouvez trouver des modules qui effectuent ces opérations, ainsi que d’autres tâches de prétraitement des données dans le groupe **Transformation des données** du panneau gauche.

Utilisez le module **Sélectionner des colonnes dans le jeu de données** pour exclure les pertes normalisées qui ont de nombreuses valeurs manquantes. Utilisez ensuite le module **Nettoyage des données manquantes** pour supprimer les lignes où il manque des valeurs. Cela permet de créer un jeu de données d’apprentissage propre.

![Prétraitement des données](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Effectuer l’apprentissage du modèle

Les problèmes d’apprentissage automatique varient. Les tâches d’apprentissage automatique courantes incluent la classification, le clustering, la régression et les systèmes de recommandation, chacun d’eux pouvant nécessiter un algorithme différent. Votre choix de l’algorithme dépend souvent de la configuration requise pour le cas d’usage. Après avoir sélectionné un algorithme, vous devez ajuster ses paramètres pour effectuer l’apprentissage d’un modèle plus précis. Vous devez ensuite évaluer tous les modèles en fonction des métriques telles que la précision, l’intelligibilité et l’efficacité.

Étant donné que l’objectif de cet échantillon est de prédire le prix des voitures et puisque la colonne d’étiquette (prix) contient des nombres réels, le fait d’opter pour un modèle de régression est judicieux. Si l’on considère que le nombre de fonctionnalités est relativement bas (inférieur à 100), et que celles-ci ne sont pas éparpillées, la limite de décision est susceptible d’être non linéaire. Nous utilisons donc **Régression de forêt de décision** pour ce pipeline.

Utilisez le module **Fractionner les données** pour diviser de façon aléatoire les données d’entrée, afin que le jeu de données d’apprentissage contienne 70 % des données d’origine et que le jeu de données de test contienne 30 % des données d’origine.

## <a name="test-evaluate-and-compare"></a>Test, évaluation et comparaison

Divisez le jeu de données et utilisez les différents jeux de données obtenus pour entraîner et tester le modèle, afin d’évaluer le modèle de manière plus objective.

Une fois que le modèle est entraîné, vous pouvez utiliser les modules **Noter le modèle** et **Évaluer le modèle** pour générer des résultats de prédiction et évaluer les modèles.

Le module **Noter le modèle** génère des prédictions pour le jeu de données de test à l’aide du modèle formé. Pour vérifier le résultat, sélectionnez le port de sortie du module **Noter le modèle**, puis cliquez sur **Visualiser**.

![Résultat de la notation](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

Passez ensuite les scores au module **Évaluer le modèle** pour générer des métriques d’évaluation. Pour vérifier le résultat, sélectionnez le port de sortie du module **Noter le modèle**, puis cliquez sur **Visualiser**.

![Évaluer le résultat](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres échantillons disponibles pour le concepteur :

- [Exemple 2 - Régression : comparer des algorithmes pour prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification avec sélection des caractéristiques : Prédiction des revenus](how-to-designer-sample-classification-predict-income.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](how-to-designer-sample-classification-churn.md)
- [Exemple 6 - Classification : Prédire les retards de vols](how-to-designer-sample-classification-flight-delay.md)
- [Exemple 7 - Classification de texte : Jeu de données Wikipédia SP 500](how-to-designer-sample-text-classification.md)