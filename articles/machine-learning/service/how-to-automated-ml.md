---
title: Présentation du machine learning automatisé - Azure Machine Learning
description: Dans cet article, vous allez découvrir ce qu’est le machine learning automatisé. Le service Azure Machine Learning peut choisir automatiquement un algorithme pour vous, et générer un modèle à partir de celui-ci. Le machine learning automatisé vous aide à gagner du temps en utilisant les paramètres et les critères que vous fournissez pour sélectionner le meilleur algorithme pour votre modèle.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: krishnan
author: krishnaanumalasetty
ms.date: 9/24/2018
ms.openlocfilehash: 2a9c05b68d05102fab80b2aa8fb1c1dad8a367ea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960037"
---
# <a name="what-is-automated-machine-learning"></a>Qu’est-ce que le machine learning automatisé ?

Dans cet article, vous allez découvrir ce qu’est le machine learning automatisé. Le service Azure Machine Learning peut choisir automatiquement un algorithme pour vous, et générer un modèle à partir de celui-ci. Le machine learning automatisé vous aide à gagner du temps en utilisant les paramètres et les critères que vous fournissez pour sélectionner le meilleur algorithme pour votre modèle.

## <a name="how-it-works"></a>Fonctionnement

1. Vous configurez le type de problème de machine learning que vous voulez résoudre. Deux catégories d’apprentissage supervisé sont prises en charge :
   + Classification
   + Régression

   Consultez la [liste des modèles](how-to-configure-auto-train.md#select-your-experiment-type) qu’Azure Machine Learning peut essayer lors de l’entraînement.

1. Vous spécifiez la source et le format des données d’entraînement. Les données doivent être étiquetées, et peuvent être stockées sur votre environnement de développement ou dans Stockage Blob Azure. Si les données sont stockées sur votre environnement de développement, elles doivent se trouver dans le même répertoire que vos scripts d’entraînement. Ce répertoire est copié sur la cible de calcul que vous sélectionnez pour l’entraînement.

    Dans votre script d’entraînement, les données peuvent être lues dans des tableaux Numpy ou dans un dataframe pandas.

    Vous pouvez configurer des options de fractionnement pour la sélection des données d’entraînement et de validation, ou vous pouvez spécifier des jeux de données d’entraînement et de validation distincts.

1. Configurez la [cible de calcul](how-to-set-up-training-targets.md) qui sert à entraîner le modèle.

1. Définissez la configuration du machine learning automatisé. Cela contrôle les paramètres utilisés quand Azure Machine Learning itère différents modèles, les hyperparamètres, ainsi que les métriques à examiner lors de la détermination du meilleur modèle. 

1. Soumettez une exécution d’entraînement.

Pendant l’entraînement, le service Azure Machine Learning crée plusieurs pipelines qui essaient différents paramètres et algorithmes. Il s’arrête une fois qu’il a atteint la limite d’itération que vous avez spécifiée, ou quand il a atteint la valeur cible de la métrique que vous avez spécifiée.

[ ![Machine learning automatisé](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Vous pouvez inspecter les informations d’exécution consignées dans le journal, qui contient les métriques collectées pendant l’exécution. L’exécution d’entraînement génère également un objet sérialisé Python (fichier .pkl) contenant le prétraitement des données et du modèle.

## <a name="next-steps"></a>Étapes suivantes

Consultez les exemples et découvrez comment générer des modèles à l’aide du machine learning automatisé :

+ [Tutoriel : Entraîner automatiquement un modèle de classification avec le machine learning automatisé dans Azure Machine Learning](tutorial-auto-train-models.md)

+ [Guide pratique pour configurer les paramètres d’entraînement automatique](how-to-configure-auto-train.md)

+ [Guide pratique pour utiliser l’entraînement automatique sur une ressource distante](how-to-auto-train-remote.md) 
