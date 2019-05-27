---
title: Interface visuelle
titleSuffix: Azure Machine Learning service
description: En savoir plus sur les termes du contrat, les concepts et les flux de travail qui composent l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 0b158a1d713e0de8f3d1b2672aed442fce66e724
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65917159"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Qu’est l’interface visuelle pour le service Azure Machine Learning ? 

L’interface visuelle (version préliminaire) pour le service Azure Machine Learning vous permet de préparation des données, former, tester, déployer, gérer et suivre les modèles d’apprentissage automatique sans écrire de code.

Il n’existe aucune programmation, vous vous connectez visuellement [jeux de données](#dataset) et [modules](#module) pour construire votre modèle.

L’interface visuelle utilise votre service Azure Machine Learning [espace de travail](concept-workspace.md) à :

+ Écrire des artefacts de [faire des essais](#experiment) s’exécute dans l’espace de travail.
+ Accès [jeux de données](#dataset).
+ Utilisez le [des ressources de calcul](#compute) dans l’espace de travail pour exécuter l’expérience. 
+ Inscrire [modèles](concept-azure-machine-learning-architecture.md#model).
+ [Déployer](#deployment) modèles comme services web sur les ressources dans l’espace de travail de calcul.

![Vue d’ensemble de l’interface visuelle](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Workflow

L’interface visuelle vous donne un canevas interactif, visual pour rapidement générer, tester et effectuer une itération sur un modèle. 

+ Vous faites glisser-déplacer [modules](#module) sur le canevas.
+ Connecter les modules ensemble pour former un [faire des essais](#experiment).
+ Exécutez l’expérience à l’aide de la ressource de calcul de l’espace de travail du Service Machine Learning.
+ Effectuer une itération sur la conception de votre modèle en modifiant l’expérience et l’exécuter à nouveau.
+ Lorsque vous êtes prêt, convertir votre **expérience de formation** à un **expérience prédictive**.
+ [Déployer](#deployment) l’expérience prédictive comme un site web de service afin que votre modèle est accessible par d’autres utilisateurs.

## <a name="experiment"></a>Expérience

Création d’une expérience à partir de zéro, ou utiliser un exemple d’expérience existante comme modèle.  Chaque fois que vous exécutez une expérience, les artefacts sont stockés dans votre espace de travail.

Une expérience se compose de jeux de données et les modules d’analyse que vous connectez ensemble pour construire un modèle. En particulier, une expérience valide a les caractéristiques suivantes :

* Jeux de données peut-être être connectés uniquement à des modules.
* Modules peuvent être connectés à des jeux de données ou d’autres modules.
* Tous les ports d’entrée pour les modules doivent comporter une connexion au flux de données.
* Tous les requis paramètres pour chaque module doivent être définis.

Pour obtenir un exemple d’une expérience simple, consultez [Guide de démarrage rapide : Préparer et visualiser les données sans écrire de code dans Azure Machine Learning](ui-quickstart-run-experiment.md).

Pour obtenir une description plus complète d’une solution d’analytique prédictive, consultez [didacticiel : Prédire le prix des véhicules automobiles avec l’interface visuelle](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Jeu de données

Un jeu de données est des données qui ont été téléchargées vers l’interface visuelle à utiliser dans le processus de modélisation. Un certain nombre de jeux de données exemple sont inclus pour vous permettent d’expérimenter, et vous pouvez charger plusieurs jeux de données comme vous en avez besoin.

## <a name="module"></a>Module

Un module est un algorithme que vous appliquez à vos données. L’interface visuelle a un nombre de modules, allant des fonctions d’entrée de données pour la formation, notation et les processus de validation.

Un module peut comporter un ensemble de paramètres utilisables pour configurer les algorithmes internes du module. Lorsque vous sélectionnez un module sur le canevas, ses paramètres sont affichés dans le volet de propriétés à droite de la zone de dessin. Vous pouvez modifier les paramètres figurant dans ce volet pour affiner votre modèle.

![Propriétés du module](media/ui-concept-visual-interface/properties.png)

Pour une aide pour la navigation via la bibliothèque d’algorithmes d’apprentissage automatique disponibles, consultez [algorithmes et les modules de vue d’ensemble de référence](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> Ressources de calcul

Utilisation des ressources à partir de votre espace de travail pour exécuter votre expérience ou un hôte vos modèles déployés en tant que services web de calcul. Les cibles de calcul prises en charge sont les suivantes :


| Cible de calcul | Formation | Déploiement |
| ---- |:----:|:----:|
| Capacité de calcul Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Calcul des cibles sont attachés à votre Machine Learning [espace de travail](concept-workspace.md). Vous gérez vos cibles de calcul dans votre espace de travail dans le [Azure portal](https://portal.azure.com).

## <a name="deployment"></a>Déploiement

Une fois que votre modèle d’analytique prédictive est prêt, déployez-la comme un service web directement à partir de l’interface visuelle.

Les services web fournissent une interface entre une application et votre modèle de notation. Une application externe peut communiquer avec le modèle de score en temps réel. Un appel à un service web renvoie les résultats de prédiction à une application externe. Pour générer cet appel, vous transmettez une clé API créée au moment du déploiement du service web. Le service web est basé sur REST, un choix d’architecture populaires pour les projets de programmation web.

Pour savoir comment déployer votre modèle, consultez [didacticiel : Déployer un modèle d’apprentissage automatique avec l’interface visuelle](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les principes fondamentaux de l’analytique prédictive et apprentissage automatique avec [Guide de démarrage rapide : Préparer et visualiser les données sans écrire de code dans Azure Machine Learning](ui-quickstart-run-experiment.md).
* Utiliser un des exemples et les modifier à la suite de vos besoins :
    * [Exemple 1 : régression : Prédire le prix](ui-sample-regression-predict-automobile-price-basic.md)
    * [Exemple 2 - régression : Prédire le prix et comparer des algorithmes](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Exemple 3 - Classification : Prédire le risque de crédit](ui-sample-classification-predict-credit-risk-basic.md)
    * [Exemple 4 : Classification : Prédire le risque de crédit (coût sensible)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Exemple 5 - Classification : Prédire l’évolution, appétence et la vente incitative](ui-sample-classification-predict-churn.md)
