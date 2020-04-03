---
title: Exemples de pipelines de concepteur
titleSuffix: Azure Machine Learning
description: Utilisez des exemples du concepteur Azure Machine Learning pour démarrer vos pipelines Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389339"
---
# <a name="designer-sample-pipelines"></a>Exemples de pipelines de concepteur

Utilisez les exemples intégrés du concepteur Azure Machine Learning pour commencer rapidement à créer vos propres pipelines Machine Learning. Le [dépôt GitHub](https://github.com/Azure/MachineLearningDesigner) du concepteur Azure Machine Learning contient une documentation détaillée pour mieux comprendre certains scénarios de Machine Learning courants.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree).
* Un espace de travail Azure Machine Learning la référence SKU Entreprise.


## <a name="how-to-use-sample-pipelines"></a>Utilisation des exemples de pipelines

Le concepteur enregistre une copie des exemples de pipelines dans votre espace de travail Studio. Vous pouvez modifier un pipeline pour l’adapter à vos besoins et l’enregistrer afin de vous l’approprier. Utilisez-les comme point de départ pour démarrer vos projets.

### <a name="open-a-sample-pipeline"></a>Ouvrir un exemple de pipeline

1. Connectez-vous à <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>, puis sélectionnez l’espace de travail à utiliser.

1. Sélectionnez **Concepteur**.

1. Sélectionnez un exemple de pipeline dans la section **Nouveau pipeline**.

    Sélectionnez **Afficher plus d’échantillons** pour obtenir la liste complète des exemples.

### <a name="submit-a-pipeline-run"></a>Envoyer une exécution de pipeline

Pour exécuter un pipeline, vous devez d’abord définir la cible de calcul par défaut sur laquelle exécuter le pipeline.

1. Dans le volet **Paramètres** à droite du canevas, sélectionnez **Sélectionner une cible de calcul**.

1. Dans la boîte de dialogue qui s’affiche, sélectionnez une cible de calcul existante ou créez-en une. Sélectionnez **Enregistrer**.

1. Sélectionnez **Envoyer** en haut du canevas pour envoyer une exécution de pipeline.

Selon l’exemple de pipeline et les paramètres de calcul, les exécutions peuvent prendre du temps. Les paramètres de calcul par défaut ont une taille de nœud minimale de 0, ce qui signifie que le concepteur doit allouer des ressources après une période d’inactivité. Les exécutions de pipeline répétées prennent moins de temps dans la mesure où les ressources de calcul sont déjà allouées. Par ailleurs, le concepteur utilise les résultats mis en cache pour chaque module afin d’améliorer l’efficacité.


### <a name="review-the-results"></a>Passer en revue les résultats.

Une fois l’exécution du pipeline terminée, vous pouvez passer en revue le pipeline et examiner la sortie de chaque module pour en savoir plus.

Utilisez les étapes suivantes pour consulter les sorties des modules :

1. Sélectionnez un module dans le canevas.

1. Dans le volet de détails des modules à droite du canevas, sélectionnez **Sorties + journaux**. Sélectionnez l’icône de graphique ![icône de visualisation](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) pour voir les résultats de chaque module. 

Utilisez les exemples comme points de départ pour quelques-uns des scénarios de Machine Learning les plus courants.

## <a name="regression-samples"></a>Exemples de régression

Découvrez des exemples de régression intégrés.

| Exemple de titre | Description | 
| --- | --- |
| [Exemple 1 : Régression - Prédiction du prix des véhicules automobiles (simple)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Prédit le prix des véhicules à l’aide d’une régression linéaire. |
| [Exemple 2 : Régression - Prédiction du prix des véhicules automobiles (avancé)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Prédit le prix des véhicules à l’aide des régresseurs de la forêt de décision et de l’arbre de décision optimisé. Compare les modèles pour trouver le meilleur algorithme.

## <a name="classification-samples"></a>Exemples de classification

Découvrez les exemples de classification intégrés. Pour plus d’information sur ces exemples sans passer par les liens de la documentation, ouvrez les exemples et affichez les commentaires des modules.

| Exemple de titre | Description | 
| --- | --- |
| [Exemple 3 : Classification binaire avec sélection des caractéristiques - Prédiction des revenus](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Prédit des revenus comme étant élevés ou faibles à l’aide d’un arbre de décision optimisé à deux classes. Utilisez la corrélation de Pearson pour sélectionner les caractéristiques.
| [Exemple 4 : Classification binaire avec un script Python personnalisé - Prédiction du risque de crédit](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Classe les demandes de crédit comme présentant un risque élevé ou faible. Utilisez le module Exécuter un script Python pour pondérer vos données.
| [Exemple 5 : Classification binaire - Prédiction de la relation client](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Prédit l’attrition clients à l’aide d’arbres de décision optimisés à deux classes. Utilisez SMOTE pour échantillonner des données biaisées.
| [Exemple 7 : Classification de texte - Jeu de données Wikipédia SP 500](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Classifie des types de sociétés à partir d’articles Wikipédia avec la régression logistique multiclasse. |
| Exemple 12 : Classification multiclasse - Reconnaissance des lettres | Crée un ensemble de classifieurs binaires pour classifier les lettres écrites. |

## <a name="recommender-samples"></a>Exemples du système de recommandation

Découvrez les exemples intégrés du système de recommandation. Pour plus d’information sur ces exemples sans passer par les liens de la documentation, ouvrez les exemples et affichez les commentaires des modules.

| Exemple de titre | Description | 
| --- | --- |
| Exemple 10 : Recommandation - Tweets d’évaluation des films | Crée un moteur de recommandations de films à partir des titres et des évaluations des films. |

## <a name="utility-samples"></a>Exemples des utilitaires

Découvrez les exemples qui montrent les utilitaires et les caractéristiques de Machine Learning. Pour plus d’information sur ces exemples sans passer par les liens de la documentation, ouvrez les exemples et affichez les commentaires des modules.

| Exemple de titre | Description | 
| --- | --- |
| [Exemple 6 : Utiliser un script R personnalisé - Prédiction de retard des vols](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Exemple 8 : Validation croisée pour la classification binaire - Prédiction des revenus des adultes | Utilise la validation croisée afin de créer un classifieur binaire pour les revenus des adultes.
| Exemple 9 : Importance de la fonctionnalité de permutation | Utilise l’importance d’une caractéristique de permutation pour calculer les scores d’importance du jeu de données de test. 
| Exemple 11 : Réglage des paramètres pour la classification binaire - Prédiction des revenus des adultes | Utilise Optimiser les hyperparamètres du modèle afin de trouver des hyperparamètres optimaux pour la création d’un classifieur binaire. |

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Apprendre à créer et à déployer des modèles Machine Learning en suivant le [Tutoriel : Prédire le prix de voitures avec le concepteur](tutorial-designer-automobile-price-train-score.md)
