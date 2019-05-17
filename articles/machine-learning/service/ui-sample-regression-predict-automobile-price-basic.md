---
title: 'Régression : Prédire le prix'
titleSuffix: Azure Machine Learning service
description: Découvrez comment générer un modèle machine learning pour prédire les prix d’un véhicule automobile sans écrire une seule ligne de code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 9dfa4b62f5cb79a5716f6f29651e85d0f8a3a409
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787857"
---
# <a name="sample-1---regression-predict-price"></a>Exemple 1 : régression : Prédire le prix

Découvrez comment créer un modèle de régression d’apprentissage sans écrire une seule ligne de code à l’aide de l’interface visuelle.

Expérience trains un **régresseur de forêt de décision** pour prédire une voiture de prix basé sur des fonctionnalités techniques, telles que la marque, de modèle, de puissance et de taille. Étant donné que nous essayons de répondre à la question « Combien ? » Il s’agit d’un problème de régression. Toutefois, vous pouvez appliquer les mêmes étapes fondamentales dans cette expérience pour s’attaquer à n’importe quel type de problème d’apprentissage automatique qu’il s’agisse de régression, la classification, clustering et ainsi de suite.

Les étapes fondamentales d’un modèle d’apprentissage formation sont :

1. Obtenir les données
1. Prétraiter les données
1. Formation du modèle
1. Évaluer le modèle

Voici le graphique terminé, final de l’expérience que nous allons travailler. Nous vous fournirons le raisonnement pour tous les modules afin de prendre des décisions similaires sur votre propre.

![Graphique de l’expérience](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le **Open** bouton de l’expérience de l’exemple 1 :

    ![Ouvrez l’expérience](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Obtenir les données

Dans cette expérience, nous utilisons le **données de prix des véhicules automobiles (brutes)** jeu de données à partir du référentiel d’apprentissage automatique UCI. Le jeu de données contient 26 colonnes qui contiennent des informations sur les véhicules automobiles, y compris la marque, modèle, prix, véhicule fonctionnalités (telles que le nombre de cylindres), MPG et un score de risque d’assurance. L’objectif de cette expérience est de prédire le prix de la voiture.

## <a name="pre-process-the-data"></a>Prétraiter les données

Les tâches de préparation de données principale incluent le nettoyage des données, intégration, transformation, la réduction et discrétisation ou quantification. Dans l’interface visuelle, vous pouvez trouver des modules pour effectuer ces opérations et autres données des tâches de traitement dans le **Transformation des données** groupe dans le volet gauche.

Nous utilisons le **Select Columns in Dataset** module pour exclure les pertes normalisées qui ont de nombreuses valeurs manquantes. Nous utilisons ensuite **Clean Missing Data** pour supprimer les lignes qui ont des valeurs manquantes. Cela permet de créer un ensemble de nettoyage de données d’apprentissage.

![Prétraitement des données](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Formation du modèle

Problèmes machine learning varient. Tâches d’apprentissage automatique courants incluent la classification, le clustering, la régression et les systèmes de recommandation, chacun d’eux peut nécessiter un algorithme différent. Votre choix de l’algorithme dépend souvent de la configuration requise du cas d’usage. Une fois que vous sélectionnez un algorithme, vous devez ajuster ses paramètres pour former un modèle plus précis. Vous devez ensuite évaluer tous les modèles en fonction des métriques telles que la précision, intelligibilité et d’efficacité.

Étant donné que l’objectif de cette expérience est de prédire le prix des voitures, et étant donné que la colonne d’étiquette (prix) contient des nombres réels, un modèle de régression est un bon choix. Considérant que le nombre de fonctionnalités est relativement faible (inférieure à 100), et ces fonctionnalités ne sont pas éparses, la limite de décision est susceptible d’être non linéaire. Nous utilisons **Decision Forest Regression** pour cette expérience.

Nous utilisons le **fractionner les données** module à diviser de façon aléatoire les données d’entrée afin que le dataset d’apprentissage contient 70 % des données d’origine et le jeu de données de test contient 30 % des données d’origine.

## <a name="test-evaluate-and-compare"></a>Tester, évaluer et comparer

 Nous fractionner le jeu de données et utiliser différents jeux de données pour former et tester le modèle pour effectuer l’évaluation du modèle plus objectives.

Une fois que le modèle est formé, nous utilisons le **noter le modèle** et **évaluer le modèle** modules pour générer des résultats prévus et d’évaluer les modèles.

**Noter le modèle** génère des prédictions pour le jeu de données de test à l’aide du modèle formé. Pour vérifier le résultat, sélectionnez le port de sortie **noter le modèle** , puis sélectionnez **visualiser**.

![Résultat de notation](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Nous passons ensuite les scores pour le **évaluer le modèle** module pour générer des mesures d’évaluation. Pour vérifier le résultat, sélectionnez le port de sortie le **évaluer le modèle** , puis sélectionnez **visualiser**.

![Évaluer les résultats](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres exemples disponibles pour l’interface visuelle :

- [Exemple 2 - régression : Comparer des algorithmes pour la prédiction de prix des véhicules automobiles](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification : Prédire le risque de crédit](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemple 4 : Classification : Prédire le risque de crédit (coût sensible)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](ui-sample-classification-predict-churn.md)