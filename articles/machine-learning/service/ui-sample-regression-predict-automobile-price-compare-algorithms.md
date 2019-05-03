---
title: 'Régression : Prédire le prix et comparer des algorithmes'
titleSuffix: Azure Machine Learning service
description: Cet exemple d’expérience interface visuelle montre comment comparer les performances des deux modèles de régression pour prédire les prix d’un véhicule automobile. Le processus comprend la formation, de tester et d’évaluer le modèle sur le jeu de données Automobile price data (Raw).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 9748e9706e1140f200458ba77f8b2fc424be3b14
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029099"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Exemple 2 - régression : Prédire le prix et comparer des algorithmes

Cet exemple d’expérience interface visuelle montre comment comparer les performances des deux modèles de régression pour prédire les prix d’un véhicule automobile. Le processus comprend la formation, de test et d’évaluer le modèle à l’aide de la **données de prix des véhicules automobiles (brutes)** jeu de données.

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Sélectionnez le **Open** bouton de l’expérience de l’exemple 2 :

    ![Ouvrez l’expérience](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="related-sample"></a>Exemple

[Exemple 1 : régression : Prédiction de prix automobile (Basic)](ui-sample-regression-predict-automobile-price-basic.md) fournit une expérience plus simple qui résout le problème même en tant que cette expérience, mais qui utilise un seul modèle de régression. Vous y reporter si vous avez besoin d’un exemple de base de la régression.

## <a name="experiment-summary"></a>Résumé de l’expérience

Nous utilisons ces étapes pour créer l’expérience :

1. Obtenir les données.
1. Prétraiter les données.
1. Apprentissage du modèle.
1. Tester, évaluer et comparer les modèles.

Voici le graphique complet de l’expérience :

[ ![Graphique de l’expérience](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png#lightbox)


## <a name="get-the-data"></a>Obtenir les données

Dans cette expérience, nous utilisons le **données de prix des véhicules automobiles (brutes)** jeu de données à partir du référentiel d’apprentissage automatique UCI. Ce jeu de données contient 26 colonnes qui contiennent des informations sur les véhicules automobiles, y compris la marque, modèle, prix, véhicule fonctionnalités (telles que le nombre de cylindres), MPG et un score de risque d’assurance. L’objectif de cette expérience est de prédire le prix d’une voiture.

## <a name="pre-process-the-data"></a>Prétraiter les données

Les tâches de préparation de données principale incluent le nettoyage des données, intégration, transformation, la réduction et discrétisation ou quantification. Dans l’interface visuelle, vous pouvez trouver des modules pour effectuer ces opérations et autres données des tâches de traitement dans le **Transformation des données** groupe dans le volet gauche.

Dans cette expérience, nous utilisons le **Select Columns in Dataset** module pour exclure les pertes normalisées qui ont de nombreuses valeurs manquantes. Nous utilisons ensuite **Clean Missing Data** pour supprimer les lignes qui ont des valeurs manquantes. Cela permet de créer un ensemble de nettoyage de données d’apprentissage.

![Prétraitement des données](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Formation du modèle

Problèmes machine learning varient. Tâches d’apprentissage automatique courants incluent la classification, le clustering, la régression et les systèmes de recommandation, chacun d’eux peut nécessiter un algorithme différent. Votre choix de l’algorithme dépend souvent de la configuration requise du cas d’usage. Une fois que vous sélectionnez un algorithme, vous devez ajuster ses paramètres pour former un modèle plus précis. Vous devez ensuite évaluer tous les modèles en fonction des métriques telles que la précision, intelligibilité et d’efficacité.

Étant donné que l’objectif de cette expérience est de prédire le prix des voitures, et étant donné que la colonne d’étiquette (prix) contient des nombres réels, un modèle de régression est un bon choix. Considérant que le nombre de fonctionnalités est relativement faible (inférieure à 100), et ces fonctionnalités ne sont pas éparses, la limite de décision est susceptible d’être non linéaire.

Pour comparer les performances de différents algorithmes, nous utilisons deux algorithmes non linéaires, **Boosted Decision Tree Regression** et **Decision Forest Regression**, pour générer des modèles. Les deux algorithmes ont des paramètres que vous pouvez modifier, mais nous utilisons les valeurs par défaut pour cette expérience.

Nous utilisons le **fractionner les données** module à diviser de façon aléatoire les données d’entrée afin que le dataset d’apprentissage contient 70 % des données d’origine et le jeu de données de test contient 30 % des données d’origine.

## <a name="test-evaluate-and-compare-the-models"></a>Tester, évaluer et comparer les modèles

Nous utilisons deux différents ensembles de données choisies au hasard pour former et ensuite tester le modèle, comme décrit dans la section précédente. Nous fractionner le jeu de données et utiliser différents jeux de données pour former et tester le modèle pour effectuer l’évaluation du modèle plus objectives.

Une fois que le modèle est formé, nous utilisons le **noter le modèle** et **évaluer le modèle** modules pour générer des résultats prévus et d’évaluer les modèles. **Noter le modèle** génère des prédictions pour le jeu de données de test à l’aide du modèle formé. Nous passons ensuite les scores pour **évaluer le modèle** pour générer des mesures d’évaluation.

Dans cette expérience, nous utilisons deux instances de **évaluer le modèle** pour comparer deux paires de modèles.

Tout d’abord, nous comparons deux algorithmes sur le dataset d’apprentissage.
Ensuite, nous comparons deux algorithmes sur le jeu de données de test.
Voici les résultats :

![Comparez les résultats](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Ces résultats montrent que le modèle créé avec **Boosted Decision Tree Regression** a une racine inférieure signifie que le modèle basé sur l’erreur quadratique **Decision Forest Regression**.

Les deux algorithmes ont une erreur inférieur sur le dataset d’apprentissage que sur le jeu de données de test invisible.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez les autres exemples disponibles pour l’interface visuelle :

- [Exemple 1 : régression : Prédire les prix d’un véhicule automobile](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemple 3 - Classification : Prédire le risque de crédit](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemple 4 : Classification : Prédire le risque de crédit (coût sensible)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](ui-sample-classification-predict-churn.md)
