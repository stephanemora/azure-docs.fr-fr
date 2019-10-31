---
title: Interface visuelle
titleSuffix: Azure Machine Learning
description: En savoir plus sur les termes, les concepts et le workflow qui composent l’interface visuelle (préversion) pour Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 9/23/2019
ms.openlocfilehash: a23f123c6ffadaad4f830e1f4eab3820e6ef56f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692220"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Qu’est-ce que l’interface visuelle pour Azure Machine Learning ? 

L’interface visuelle (préversion) pour Azure Machine Learning permet de préparer des données, de former, de tester, de déployer et de suivre les modèles d’apprentissage automatique sans écrire de code.

Aucune programmation n’est nécessaire : il suffit de visualiser la connexion des [jeux de donnée](#datasets)s et des [modules](#module) pour construire votre modèle.

L’interface visuelle utilise votre [espace de travail](concept-workspace.md) Azure Machine Learning pour :

+ Créer, modifier et exécuter des [pipelines](#pipeline) dans l’espace de travail.
+ Accéder aux [jeux de données](#datasets).
+ Utilisez les [ressources de calcul](#compute) dans l’espace de travail pour exécuter le pipeline. 
+ Inscrivez les [modèles](concept-azure-machine-learning-architecture.md#models).
+ [Publier](#publish) des pipelines en tant que points de terminaison REST.
+ [Déployez](#deployment) des modèles en tant que points de terminaison de pipeline (pour l’inférence de lot) ou des points de terminaison en temps réel sur les ressources de calcul dans l’espace de travail.

![Vue d’ensemble de l’interface visuelle](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Workflow

L’interface visuelle offre un canevas visuel interactif pour générer, tester et effectuer une itération rapidement sur un modèle. 

+ Vous glissez-déplacez les [jeux de données](#datasets) et [modules](#module) sur le canevas.
+ Connectez les modules ensemble pour former un [pipeline](#pipeline).
+ Exécutez le pipeline à l’aide de la ressource de calcul de l’espace de travail du service Machine Learning.
+ Effectuez une itération sur la conception de votre modèle en modifiant le pipeline et en l’exécutant à nouveau.
+ Lorsque vous êtes prêt, convertissez votre **pipeline de formation** en un **pipeline d’inférence**.
+ [Publiez](#publish) votre pipeline en tant que point de terminaison REST si vous souhaitez le renvoyer sans que le code Python ne l’ait construit.
+ [Déployez](#deployment) le pipeline d’inférence comme point de terminaison de pipeline ou point de terminaison en temps réel afin que votre modèle soit accessible par d’autres utilisateurs.

## <a name="pipeline"></a>Pipeline

Créez un [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) de Machine Learning à partir de zéro ou utilisez un échantillon de pipeline existant comme modèle. Chaque fois que vous exécutez un pipeline, des artefacts sont stockés dans votre espace de travail. Les exécutions de pipeline sont regroupées en [expériences](concept-azure-machine-learning-architecture.md#experiments).

Un pipeline se compose de jeux de données et de modules d’analyse que vous connectez ensemble pour construire un modèle. En particulier, un pipeline valide a les caractéristiques suivantes :

* Il est possible de connecter des jeux de données uniquement à des modules.
* Les modules peuvent être connectés à des jeux de données ou à d’autres modules.
* Tous les ports d’entrée des modules doivent comporter une connexion au flux de données.
* Tous les paramètres obligatoires de chaque module doivent être configurés.


Pour plus d'informations sur la prise en main de l'interface visuelle, consultez [Tutoriel : Prédire le prix de voitures à l’aide de l’interface visuelle](ui-tutorial-automobile-price-train-score.md).

## <a name="datasets"></a>Groupes de données

Un jeu de données Machine Learning facilite l’accès aux données et l’utilisation de ces dernières. Un certain nombre d’échantillons de jeux de données sont inclus dans l’interface visuelle à expérimenter. Vous pouvez [inscrire](./how-to-create-register-datasets.md) plusieurs jeux de données en fonction de vos besoins.

## <a name="module"></a>Module

Un module est un algorithme que vous appliquez à vos données. L’interface visuelle comporte divers modules, allant de fonctions de saisie des données à des procédures de formation, de notation et de validation.

Un module peut comporter un ensemble de paramètres utilisables pour configurer les algorithmes internes du module. Quand vous sélectionnez un module dans le canevas, ses paramètres sont affichés dans le volet Propriétés à droite du canevas. Vous pouvez modifier les paramètres figurant dans ce volet pour affiner votre modèle.

![Propriétés du module](media/ui-concept-visual-interface/properties.png)

Pour obtenir de l’aide relative à la navigation dans la bibliothèque d’algorithmes d’apprentissage automatique disponibles, consultez [Vue d’ensemble des informations de référence relatives aux algorithmes et aux modules](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> Ressources de calcul

Utilisez les ressources de calcul de votre espace de travail pour exécuter votre pipeline et héberger vos modèles déployés en tant que points de terminaison en temps réel ou points de terminaison de pipeline (pour l’inférence de lot). Les cibles de calcul prises en charge sont les suivantes :

| Cible de calcul | Entrainement | Déploiement |
| ---- |:----:|:----:|
| Capacité de calcul Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Les cibles de calcul sont associées à votre [espace de travail](concept-workspace.md) de Machine Learning. Vous gérez vos cibles de calcul de votre espace de travail dans le [Portail Azure](https://portal.azure.com) ou dans la [page d’arrivée de votre espace de travail (préversion)](https://ml.azure.com).

## <a name="publish"></a>Publish

Une fois que vous avez un pipeline prêt, vous pouvez le publier en tant que point de terminaison REST. Un [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) peut être soumis sans le code Python qui l’a construit.

En outre, un PublishedPipeline peut être utilisé pour renvoyer un pipeline avec différentes valeurs et entrées PipelineParameter.

## <a name="deployment"></a>Déploiement

Une fois votre modèle prédictif prêt, déployez-le en tant que point de terminaison de pipeline ou de point de terminaison en temps réel à partir de l’interface visuelle.

Le point de terminaison de pipeline est un [PublishedPipeline, que vous pouvez soumettre à une exécution de pipeline avec différentes valeurs PipelineParameter et entrées pour l’inférence de lot.

Le point de terminaison en temps réel fournit une interface entre une application et votre modèle de scoring. Une application externe peut communiquer en temps réel avec le modèle de scoring. Un appel à un point de terminaison en temps réel renvoie les résultats de prédiction à une application externe. Pour générer un appel vers un point de terminaison en temps réel, vous transmettez une clé API créée au moment du déploiement du point de terminaison. Le point de terminaison s’appuie sur l’architecture populaire REST, souvent choisie pour les projets de programmation web.

Pour savoir comment déployer votre modèle, consultez [Tutoriel : Déployer un modèle Machine Learning avec l’interface visuelle](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les principes fondamentaux de l’analytique prédictive et du Machine Learning à l’aide du [Tutoriel : Prédire le prix de voitures à l’aide de l’interface visuelle](ui-tutorial-automobile-price-train-score.md)
* Utilisez un des exemples et modifiez-le selon vos besoins :

    * [Exemple 1 - Régression : Prédire le prix](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [Exemple 2 - Régression : Algorithmes de prédiction de prix et de comparaison](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Exemple 3 - Classification : prédire le risque de crédit](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Exemple 5 - Classification : Prédire l’évolution, l’appétence et la vente incitative](how-to-ui-sample-classification-predict-churn.md)
    * [Exemple 6 - Classification : Prédire les retards de vols](how-to-ui-sample-classification-predict-flight-delay.md)

