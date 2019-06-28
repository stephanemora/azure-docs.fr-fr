---
title: 'Régression : Prédire le prix'
titleSuffix: Azure Machine Learning service
description: Découvrez comment créer un modèle Machine Learning pour prédire le prix d’un véhicule automobile sans écrire une seule ligne de code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 9dfa4b62f5cb79a5716f6f29651e85d0f8a3a409
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787857"
---
# <a name="sample-1---regression-predict-price"></a>Exemple 1 - Régression : Prédire le prix

Découvrez comment créer un modèle de régression Machine Learning sans écrire une seule ligne de code à l’aide de l’interface visuelle.

Cette expérience permet d’entraîner un **régresseur de forêt de décision** pour prédire le prix d’une voiture en fonction d’aspects techniques, tels que la marque, le modèle, la puissance et la taille. Étant donné que nous essayons de répondre à la question « Combien ? », il s’agit d’un problème de régression. Toutefois, vous pouvez appliquer les mêmes étapes fondamentales dans cette expérience pour faire face à n’importe quel type de problème d’apprentissage automatique : régression, classification, clustering, etc.

Les étapes fondamentales d’un modèle Machine Learning sont les suivantes :

1. Obtenir les données
1. Prétraitement des données
1. Formation du modèle
1. Évaluer le modèle

Voici le graphique final de l’expérience sur lequel nous allons travailler. Nous vous fournirons le raisonnement pour tous les modules afin de prendre des décisions similaires concernant le vôtre.

![Graphique de l’expérience](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le bouton **Ouvrir** pour accéder à l’exemple de l’expérience 1 :

    ![Ouvrir l’expérience](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Obtenir les données

Dans cette expérience, nous utilisons le jeu de données **Données sur le prix des véhicules automobiles (brutes)** du référentiel Machine Learning UCI. Ce jeu de données contient 26 colonnes avec des informations sur les véhicules automobiles, y compris la marque, le modèle, le prix, les fonctionnalités (telles que le nombre de cylindres), la consommation de carburant et un score de risque d’assurance. L’objectif de cette expérience est de prédire le prix d’un véhicule automobile.

## <a name="pre-process-the-data"></a>Prétraitement des données

Les principales tâches de préparation des données incluent leur nettoyage, intégration, transformation, réduction et discrétisation ou quantification. Dans l’interface visuelle, vous pouvez trouver des modules qui effectuent ces opérations, ainsi que d’autres tâches de prétraitement des données dans le groupe **Transformation des données** du volet gauche.

Nous utilisons le module **Sélectionner des colonnes dans le jeu de données** pour exclure les pertes normalisées qui ont de nombreuses valeurs manquantes. Nous utilisons ensuite le module **Nettoyage des données manquantes** pour supprimer les lignes qui ont des valeurs manquantes. Cela permet de créer un jeu de données d’apprentissage propre.

![Prétraitement des données](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Formation du modèle

Les problèmes d’apprentissage automatique varient. Les tâches d’apprentissage automatique courantes incluent la classification, le clustering, la régression et les systèmes de recommandation, chacun d’eux pouvant nécessiter un algorithme différent. Votre choix de l’algorithme dépend souvent de la configuration requise pour le cas d’usage. Après avoir sélectionné un algorithme, vous devez ajuster ses paramètres pour effectuer l’apprentissage d’un modèle plus précis. Vous devez ensuite évaluer tous les modèles en fonction des métriques telles que la précision, l’intelligibilité et l’efficacité.

Étant donné que l’objectif de cette expérience est de prédire le prix des voitures, et puisque la colonne d’étiquette (prix) contient des nombres réels, le fait d’opter pour un modèle de régression est judicieux. Si l’on considère que le nombre de fonctionnalités est relativement bas (inférieur à 100), et que celles-ci ne sont pas éparpillées, la limite de décision est susceptible d’être non linéaire. Nous utilisons le module **Régression de forêt d’arbres de décision** pour cette expérience.

Nous utilisons le module **Fractionner les données** pour diviser de façon aléatoire les données d’entrée, afin que le jeu de données d’apprentissage contienne 70 % des données d’origine et que le jeu de données de test contienne 30 % des données d’origine.

## <a name="test-evaluate-and-compare"></a>Test, évaluation et comparaison

 Nous fractionnons le jeu de données et utilisons différents jeux de données pour former et tester le modèle, afin d’évaluer le modèle de manière plus objective.

Une fois que le modèle est formé, nous utilisons les modules **Noter le modèle** et **Évaluer le modèle** pour générer des résultats de prédiction et évaluer les modèles.

Le module **Noter le modèle** génère des prédictions pour le jeu de données de test à l’aide du modèle formé. Pour vérifier le résultat, sélectionnez le port de sortie du module **Noter le modèle**, puis cliquez sur **Visualiser**.

![Résultat de la notation](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Nous transmettons ensuite les notes au module **Évaluer le modèle**, afin de générer des métriques d’évaluation. Pour vérifier le résultat, sélectionnez le port de sortie du module **Noter le modèle**, puis cliquez sur **Visualiser**.

![Évaluer le résultat](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres exemples disponibles pour l’interface visuelle :

- [Exemple 2 - Régression : comparer des algorithmes pour prédire le prix de véhicules automobiles](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification : prédire le risque de crédit](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : prédire l’évolution](ui-sample-classification-predict-churn.md)