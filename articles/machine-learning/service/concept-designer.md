---
title: Générer des modèles ML avec un concepteur
titleSuffix: Azure Machine Learning
description: En savoir plus sur les termes, les concepts et le workflow qui composent le concepteur pour Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 73facea2b99ee038b16053fd818d93d35da4cbdd
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196170"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Qu’est-ce que le concepteur Azure Machine Learning (préversion) ? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Le concepteur Azure Machine Learning vous permet de connecter visuellement les [jeux de données](#datasets) et les [modules](#module) sur un canevas interactif afin de créer des modèles Machine Learning. Pour plus d'informations sur la prise en main du concepteur, consultez [Tutoriel : Prédire le prix de voitures avec le concepteur](tutorial-designer-automobile-price-train-score.md)

![Exemple de concepteur Azure Machine Learning](./media/concept-ml-pipelines/designer-drag-and-drop.gif)

Le concepteur utilise votre [espace de travail](concept-workspace.md) Azure Machine Learning pour organiser des ressources partagées telles que :

+ [Pipelines](#pipeline)
+ [Groupes de données](#datasets)
+ [Ressources de calcul](#compute)
+ [Modèles inscrits](concept-azure-machine-learning-architecture.md#models)
+ [Pipelines publiés](#publish)
+ [Points de terminaison en temps réel](#deploy)

## <a name="model-training-and-deployment"></a>Formation et déploiement du modèle

Le concepteur vous donne un canevas visuel permettant de générer, tester et déployer des modèles Machine Learning. À l’aide du concepteur, vous pouvez :

+ Glisser-déplacer les [jeux de données](#datasets) et les [modules](#module) sur le canevas.
+ Connecter les modules ensemble pour former un [brouillon de pipeline](#pipeline-draft).
+ Envoyer une [exécution de pipeline](#pipeline-run) à l’aide des ressources de calcul de votre espace de travail Azure Machine Learning.
+ Convertir vos **pipelines de formation** en **pipelines d’inférence**.
+ [Publier](#publish) vos pipelines sur un **point de terminaison de pipeline** REST pour envoyer de nouvelles exécutions de pipeline avec des paramètres et des jeux de données différents.
    + Publier un **pipeline de formation** pour réutiliser un pipeline unique afin d’effectuer l’apprentissage de plusieurs modèles tout en modifiant les paramètres et les jeux de données.
    + Publier un **pipeline d’inférence par lot** pour effectuer des prédictions sur de nouvelles données à l’aide d’un modèle préalablement formé.
+ [Déployer](#deploy) un **pipeline d’inférence en temps réel** vers un point de terminaison en temps réel pour effectuer des prédictions sur de nouvelles données en temps réel.

![Diagramme de flux de travail pour la formation, l’inférence par lot et l’inférence en temps réel dans le concepteur](media/ui-concept-visual-interface/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Un [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) se compose de jeux de données et de modules d’analyse que vous connectez ensemble. Les pipelines ont de nombreuses utilisations : vous pouvez créer un pipeline qui effectue l’apprentissage d’un modèle unique ou un pipeline qui effectue l’apprentissage de plusieurs modèles. Vous pouvez créer un pipeline qui réalise des prédictions en temps réel ou par lot ou un pipeline qui nettoie uniquement les données. Les pipelines vous permettent de réutiliser votre travail et d’organiser vos projets.

### <a name="pipeline-draft"></a>Brouillon de pipeline

Lorsque vous modifiez un pipeline dans le concepteur, votre progression est enregistrée en tant que **brouillon de pipeline**. Vous pouvez modifier un brouillon de pipeline à tout moment en ajoutant ou en supprimant des modules, en configurant des cibles de calcul, en créant des paramètres et ainsi de suite.

Un pipeline valide a les caractéristiques suivantes :

* Les jeux de données peuvent uniquement se connecter à des modules.
* Les modules peuvent uniquement se connecter à des jeux de données ou à d’autres modules.
* Tous les ports d’entrée des modules doivent comporter une connexion au flux de données.
* Tous les paramètres obligatoires de chaque module doivent être configurés.

Lorsque vous êtes prêt à exécuter votre brouillon de pipeline, vous soumettez une exécution de pipeline.

### <a name="pipeline-run"></a>Exécution du pipeline

Chaque fois que vous exécutez un pipeline, la configuration du pipeline et ses résultats sont stockés dans votre espace de travail sous la forme d’une **exécution de pipeline**. Vous pouvez revenir à n’importe quelle exécution de pipeline pour l’inspecter à des fins de résolution de problèmes ou d’audit. **Clonez** une exécution de pipeline pour créer un brouillon de pipeline que vous pouvez modifier.

Les exécutions de pipeline sont regroupées en [expériences](concept-azure-machine-learning-architecture.md#experiments) afin d’organiser l’historique des exécutions. Vous pouvez définir l’expérience pour chaque exécution de pipeline. 

## <a name="datasets"></a>Groupes de données

Un jeu de données Machine Learning facilite l’accès aux données et l’utilisation de ces dernières. Un certain nombre d’échantillons de jeux de données sont inclus dans le concepteur à expérimenter. Vous pouvez [inscrire](./how-to-create-register-datasets.md) plusieurs jeux de données en fonction de vos besoins.

## <a name="module"></a>Module

Un module est un algorithme que vous appliquez à vos données. Le concepteur comporte divers modules, allant de fonctions d’entrée des données à des procédures de formation, de notation et de validation.

Un module peut comporter un ensemble de paramètres utilisables pour configurer les algorithmes internes du module. Quand vous sélectionnez un module dans le canevas, ses paramètres sont affichés dans le volet Propriétés à droite du canevas. Vous pouvez modifier les paramètres figurant dans ce volet pour affiner votre modèle. Vous pouvez définir les ressources de calcul pour des modules individuels dans le concepteur. 

![Propriétés du module](media/ui-concept-visual-interface/properties.png)

Pour obtenir de l’aide relative à la navigation dans la bibliothèque d’algorithmes d’apprentissage automatique disponibles, consultez [Vue d’ensemble des informations de référence relatives aux algorithmes et aux modules](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> Ressources de calcul

Utilisez les ressources de calcul de votre espace de travail pour exécuter votre pipeline et héberger vos modèles déployés en tant que points de terminaison en temps réel ou points de terminaison de pipeline (pour l’inférence de lot). Les cibles de calcul prises en charge sont les suivantes :

| Cible de calcul | Entrainement | Déploiement |
| ---- |:----:|:----:|
| Capacité de calcul Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Les cibles de calcul sont associées à votre [espace de travail](concept-workspace.md) de Machine Learning. Vous gérez vos cibles de calcul dans votre espace de travail de [Azure Machine Learning Studio](https://ml.azure.com).

## <a name="deploy"></a>Déployer

Pour effectuer une inférence en temps réel, vous devez déployer un pipeline en tant que **point de terminaison en temps réel**. Le point de terminaison en temps réel crée une interface entre une application externe et votre modèle de scoring. Un appel à un point de terminaison en temps réel renvoie les résultats de prédiction à l’application en temps réel. Pour générer un appel vers un point de terminaison en temps réel, vous transmettez la clé API créée au moment du déploiement du point de terminaison. Le point de terminaison s’appuie sur l’architecture populaire REST, souvent choisie pour les projets de programmation web.

Les points de terminaison en temps réel doivent être déployés sur un cluster Azure Kubernetes Service.

Pour savoir comment déployer votre modèle, consultez [Tutoriel : Déployez un modèle Machine Learning avec le concepteur](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publish

Vous pouvez également publier un pipeline sur un **point de terminaison de pipeline**. Semblable à un point de terminaison en temps réel, un point de terminaison de pipeline vous permet d’envoyer de nouvelles exécutions de pipeline à partir d’applications externes à l’aide d’appels REST. Toutefois, vous ne pouvez pas envoyer ni recevoir de données en temps réel à l’aide d’un point de terminaison de pipeline.

Les pipelines publiés sont flexibles ; ils peuvent être utilisés pour effectuer ou réeffectuer l’apprentissage de modèles, effectuer des inférences par lot, traiter de nouvelles données et bien plus encore. Vous pouvez publier plusieurs pipelines sur un point de terminaison de pipeline unique et spécifier la version de pipeline à exécuter.

Un pipeline publié s’exécute sur les ressources de calcul que vous définissez dans le brouillon de pipeline pour chaque module.

Le concepteur crée le même objet [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) que le Kit de développement logiciel (SDK).


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Passer de l’interface visuelle au concepteur

L’interface visuelle (préversion) a été mise à jour et est désormais Concepteur Azure Machine Learning (préversion). Le concepteur a été remanié pour utiliser un back-end basé sur un pipeline qui s’intègre entièrement aux autres fonctionnalités d’Azure Machine Learning. 

Suite à ces mises à jour, certains concepts et termes de l’interface visuelle ont été modifiés ou renommés. Consultez le tableau ci-dessous pour connaître les modifications conceptuelles les plus importantes. 

| Concept dans le concepteur | Précédemment dans l’interface visuelle |
| ---- |:----:|
| Brouillon de pipeline | Expérience |
| Points de terminaison en temps réel | Service Web |

### <a name="migrating-to-the-designer"></a>Migration vers le concepteur

Vous pouvez convertir des expériences d’interface visuelle et des services Web existants en pipelines et points de terminaison en temps réel dans le concepteur. Procédez comme suit pour migrer vos ressources d’interface visuelle :

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Étapes suivantes

* Découvrez les principes fondamentaux de l’analytique prédictive et du Machine Learning à l’aide du [Tutoriel : Prédire le prix de voitures avec le concepteur](tutorial-designer-automobile-price-train-score.md)
* Utilisez un des exemples et modifiez-le selon vos besoins :

- [Exemple 1 - Régression : Prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemple 2 - Régression : comparer des algorithmes pour prédire le prix de véhicules automobiles](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemple 3 - Classification avec sélection des caractéristiques : Prédiction des revenus](how-to-designer-sample-classification-predict-income.md)
- [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemple 5 - Classification : Prédire l’évolution](how-to-designer-sample-classification-churn.md)
- [Exemple 6 - Classification : Prédire les retards de vols](how-to-designer-sample-classification-flight-delay.md)
- [Exemple 7 - Classification de texte : Jeu de données Wikipédia SP 500](how-to-designer-sample-text-classification.md)

