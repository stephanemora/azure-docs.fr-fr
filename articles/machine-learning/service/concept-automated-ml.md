---
title: Sélection et optimisation de l’algorithme de machine learning automatisé
titleSuffix: Azure Machine Learning service
description: Découvrez comment le service Azure Machine Learning peut choisir automatiquement un algorithme pour vous et générer un modèle à partir de celui-ci pour vous permettre de gagner du temps en utilisant les paramètres et les critères que vous fournissez de façon à sélectionner le meilleur algorithme pour votre modèle.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 7e55304fb02078c71b91b2904475a4bee4bcc018
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436957"
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

[ ![Machine learning automatisé](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Vous pouvez inspecter les informations d’exécution consignées dans le journal, qui contient les métriques collectées pendant l’exécution. L’exécution de l’entraînement produit aussi un objet sérialisé Python (fichier `.pkl`) contenant le modèle et le prétraitement des données.

## <a name="model-explainability"></a>Explicabilité du modèle

Un piège courant du machine learning automatisé est une incapacité à voir le processus de bout en bout. Azure Machine Learning vous permet de voir des informations détaillées sur les modèles, de façon à rendre plus transparent ce qui se passe sur le back-end. Certains modèles, comme la régression linéaire, sont considérés comme assez simples, et donc faciles à comprendre. Cependant, comme nous ajoutons de nombreuses fonctionnalités et que nous utilisons des modèles d’apprentissage de plus en plus complexes, leur compréhension devient de plus en plus difficile. La transparence du machine learning revêt deux aspects :

1. La connaissance du pipeline de machine learning et de toutes les étapes impliquées, notamment le prétraitement et la caractérisation des données, et les valeurs des hyperparamètres.
1. La compréhension de la relation entre les variables d’entrée (également appelées « caractéristiques ») et la sortie du modèle.  Le fait de connaître à la fois l’importance et la nature de l’impact de chaque caractéristique sur la valeur prédite aide à mieux comprendre et expliquer le modèle. On parle de « l’importance des caractéristiques ».

Vous pouvez activer l’importance des caractéristiques globale à la demande après l’entraînement pour le pipeline de votre choix, ou bien l’activer pour tous les pipelines dans le cadre de l’entraînement du machine learning automatisé.  Il s’agit d’une fonctionnalité en préversion : nous continuons à investir pour vous fournir des informations plus détaillées qui vous aident à mieux comprendre vos modèles ML.  

Suivez cet [exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/model-explanation/auto-ml-model-explanation.ipynb) pour expérimenter les explications des modèles dans Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

Consultez les exemples et découvrez comment générer des modèles à l’aide du machine learning automatisé :

+ [Tutoriel : Entraîner automatiquement un modèle de classification avec le Machine Learning Azure automatisé](tutorial-auto-train-models.md).

+ [Configurer les paramètres pour l’entraînement automatique](how-to-configure-auto-train.md)

+ [Utiliser l’entraînement automatique sur une ressource distante](how-to-auto-train-remote.md) 
