---
title: Interface visuelle
titleSuffix: Azure Machine Learning
description: En savoir plus sur les termes, les concepts et le workflow qui composent l’interface visuelle (préversion) pour Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: f560887a48ce4754b26a54ef0e18093c5577af34
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128807"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Qu’est-ce que l’interface visuelle pour Azure Machine Learning ? 

L’interface visuelle (préversion) pour Azure Machine Learning permet de préparer des données, de former, de tester, de déployer et de suivre les modèles d’apprentissage automatique sans écrire de code.

Aucune programmation n’est nécessaire : il suffit de visualiser la connexion des [jeux de donnée](#dataset)s et des [modules](#module) pour construire votre modèle.

L’interface visuelle utilise votre [espace de travail](concept-workspace.md) Azure Machine Learning pour :

+ Écrivez des artefacts d’exécutions [expérimentales](#experiment) dans l’espace de travail.
+ Accéder aux [jeux de données](#dataset).
+ Utilisez les [ressources de calcul](#compute) dans l’espace de travail pour exécuter l’expérience. 
+ Inscrivez les [modèles](concept-azure-machine-learning-architecture.md#models).
+ [Déployez](#deployment) les modèles sous forme de services web sur des ressources de calcul dans l’espace de travail.

![Vue d’ensemble de l’interface visuelle](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Workflow

L’interface visuelle offre un canevas visuel interactif pour générer, tester et effectuer une itération rapidement sur un modèle. 

+ Vous glissez-déplacez les [modules](#module) sur le canevas.
+ Associez les modules pour former une [expérience](#experiment).
+ Exécutez l’expérience à l’aide de la ressource de calcul de l’espace de travail Machine Learning Service.
+ Effectuez une itération sur la conception de votre modèle en modifiant l’expérience et en l’exécutant à nouveau.
+ Lorsque vous êtes prêt, convertissez votre **expérience de formation** en **expérience prédictive**.
+ [Déployez](#deployment) l’expérience prédictive sous forme de service web afin que votre modèle soit accessible par d’autres.

## <a name="experiment"></a>Expérience

Créez une expérience à partir de zéro, ou utilisez un exemple d’expérience existante comme modèle.  Chaque fois que vous exécutez une expérience, des artefacts sont stockés dans votre espace de travail.

Une expérience se compose de jeux de données et de modules d’analyse que vous connectez ensemble pour construire un modèle. En particulier, une expérience valide a les caractéristiques suivantes :

* Il est possible de connecter des jeux de données uniquement à des modules.
* Les modules peuvent être connectés à des jeux de données ou à d’autres modules.
* Tous les ports d’entrée des modules doivent comporter une connexion au flux de données.
* Tous les paramètres obligatoires de chaque module doivent être configurés.


Pour plus d'informations sur la prise en main de l'interface visuelle, consultez [Tutoriel : Prédire le prix de voitures à l’aide de l’interface visuelle](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Jeu de données

Un jeu de données sont des données chargées sur l’interface visuelle à utiliser dans le processus de modélisation. Divers exemples de jeux de données utilisables pour vos expériences sont fournis ; vous pouvez télécharger vers le serveur d’autres jeux de données si vous en avez besoin.

## <a name="module"></a>Module

Un module est un algorithme que vous appliquez à vos données. L’interface visuelle comporte divers modules, allant de fonctions de saisie des données à des procédures de formation, de notation et de validation.

Un module peut comporter un ensemble de paramètres utilisables pour configurer les algorithmes internes du module. Quand vous sélectionnez un module dans le canevas, ses paramètres sont affichés dans le volet Propriétés à droite du canevas. Vous pouvez modifier les paramètres figurant dans ce volet pour affiner votre modèle.

![Propriétés du module](media/ui-concept-visual-interface/properties.png)

Pour obtenir de l’aide relative à la navigation dans la bibliothèque d’algorithmes d’apprentissage automatique disponibles, consultez [Vue d’ensemble des informations de référence relatives aux algorithmes et aux modules](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> Ressources de calcul

Utilisation des ressources de calcul à partir de votre espace de travail pour exécuter votre expérience ou héberger vos modèles déployés en tant que services web. Les cibles de calcul prises en charge sont les suivantes :


| Cible de calcul | Entrainement | Déploiement |
| ---- |:----:|:----:|
| Capacité de calcul Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Les cibles de calcul sont associées à votre [espace de travail](concept-workspace.md) de Machine Learning. Vous gérez vos cibles de calcul de votre espace de travail dans le [Portail Azure](https://portal.azure.com) ou dans la [page d’arrivée de votre espace de travail (préversion)](https://ml.azure.com).

## <a name="deployment"></a>Déploiement

Une fois votre modèle d’analyse prédictive prêt, vous pouvez le déployer comme un service web directement à partir de l’interface visuelle.

Les services web fournissent une interface entre une application et votre modèle de notation. Une application externe peut communiquer en temps réel avec le modèle de scoring. Un appel à un service web renvoie les résultats de prédiction à une application externe. Pour générer cet appel, vous transmettez une clé API créée au moment du déploiement du service web. Le service web s’appuie sur l’architecture REST, souvent choisie pour les projets de programmation web.

Pour savoir comment déployer votre modèle, consultez [Tutoriel : Déployer un modèle Machine Learning avec l’interface visuelle](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les principes fondamentaux de l’analytique prédictive et du Machine Learning à l’aide du [Tutoriel : Prédire le prix de voitures à l’aide de l’interface visuelle](ui-tutorial-automobile-price-train-score.md)
* Utilisez un des exemples et modifiez-le selon vos besoins :
    * [Exemple 1 - Régression : Prédire le prix](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [Exemple 2 - Régression : Algorithmes de prédiction de prix et de comparaison](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Exemple 3 - Classification : prédire le risque de crédit](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [Exemple 4 - Classification : prédire le risque de crédit (sensible au coût)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Exemple 5 - Classification : Prédire l’évolution, l’appétence et la vente incitative](how-to-ui-sample-classification-predict-churn.md)
