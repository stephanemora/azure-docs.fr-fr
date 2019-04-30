---
title: Sélection et optimisation de l’algorithme de machine learning automatisé
titleSuffix: Azure Machine Learning service
description: Découvrez comment le service Azure Machine Learning peut choisir automatiquement un algorithme pour vous et générer un modèle à partir de celui-ci pour vous permettre de gagner du temps en utilisant les paramètres et les critères que vous fournissez de façon à sélectionner le meilleur algorithme pour votre modèle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 620dbd22613df37fdc3c20e34906684446b2251f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821191"
---
# <a name="what-is-automated-machine-learning"></a>Qu’est-ce que le machine learning automatisé ?

Le machine learning automatisé est le processus consistant à prendre des données d’entraînement avec une caractéristique cible définie, et à boucler à travers des combinaisons d’algorithmes et de sélections de caractéristiques pour choisir automatiquement le meilleur modèle pour vos données en fonction des scores de l’entraînement. Le processus traditionnel de développement des modèles d’entraînement est très gourmand en ressources, et nécessite des investissements importants en temps et en connaissances pour exécuter et comparer les résultats de dizaines de modèles. Le machine learning automatisé simplifie ce processus en générant des modèles optimisés à partir des objectifs et des contraintes que vous avez définis pour votre expérience, comme la durée d’exécution de l’expérience ou les modèles à exclure.

## <a name="how-it-works"></a>Fonctionnement

1. Vous configurez le type de problème de machine learning que vous voulez résoudre. Les catégories d’apprentissage supervisé sont prises en charge :
   + classification ;
   + régression ;
   + Prévisions

   Si le machine learning automatisé est en disponibilité générale, **la fonctionnalité de prévision est encore en préversion publique.**

   Consultez la [liste des modèles](how-to-configure-auto-train.md#select-your-experiment-type) qu’Azure Machine Learning peut essayer lors de l’entraînement.

1. Vous spécifiez la source et le format des données d’entraînement. Les données doivent être étiquetées, et peuvent être stockées sur votre environnement de développement ou dans Stockage Blob Azure. Si les données sont stockées sur votre environnement de développement, elles doivent se trouver dans le même répertoire que vos scripts d’entraînement. Ce répertoire est copié sur la cible de calcul que vous sélectionnez pour l’entraînement.

    Dans votre script d’entraînement, les données peuvent être lues dans des tableaux Numpy ou dans un dataframe pandas.

    Vous pouvez configurer des options de fractionnement pour la sélection des données d’entraînement et de validation, ou vous pouvez spécifier des jeux de données d’entraînement et de validation distincts.

1. Configurez la [cible de calcul](how-to-set-up-training-targets.md) qui sert à entraîner le modèle.

1. Définissez la configuration du machine learning automatisé. Ceci contrôle les paramètres utilisés quand Azure Machine Learning effectue une itération sur différents modèles, les valeurs des hyperparamètres et les métriques à examiner lors de la détermination du meilleur modèle.

1. Soumettez une exécution d’entraînement.

Pendant l’entraînement, le service Azure Machine Learning crée plusieurs pipelines qui essaient différents paramètres et algorithmes. Il s’arrête une fois qu’il a atteint la limite d’itération que vous avez spécifiée, ou quand il a atteint la valeur cible de la métrique que vous avez spécifiée.

[![Apprentissage automatique](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Vous pouvez inspecter les informations d’exécution consignées dans le journal, qui contient les métriques collectées pendant l’exécution. L’exécution de l’entraînement produit aussi un objet sérialisé Python (fichier `.pkl`) contenant le modèle et le prétraitement des données.


## <a name="next-steps"></a>Étapes suivantes

Consultez les exemples et découvrez comment générer des modèles à l’aide du machine learning automatisé :

+ [Tutoriel : Entraîner automatiquement un modèle de classification avec le Machine Learning Azure automatisé](tutorial-auto-train-models.md).

+ [Exemples de bloc-notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

+ [Utiliser l’entraînement automatique sur une ressource distante](how-to-auto-train-remote.md)

+ [Configurer les paramètres pour l’entraînement automatique](how-to-configure-auto-train.md)
