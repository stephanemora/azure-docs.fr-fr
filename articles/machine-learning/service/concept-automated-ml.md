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
ms.openlocfilehash: bf010e33a5ef77fcfde2506bfef9760a09667a9d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867250"
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

## <a name="model-explainability"></a>Explicabilité du modèle

Un piège courant du machine learning automatisé est une incapacité à voir le processus de bout en bout. Azure Machine Learning vous permet de voir des informations détaillées sur les modèles, de façon à rendre plus transparent ce qui se passe sur le back-end. Certains modèles, comme la régression linéaire, sont considérés comme assez simples, et donc faciles à comprendre. Cependant, comme nous ajoutons de nombreuses fonctionnalités et que nous utilisons des modèles d’apprentissage de plus en plus complexes, leur compréhension devient de plus en plus difficile. La transparence du machine learning revêt deux aspects :

1. La connaissance du pipeline de machine learning et de toutes les étapes impliquées, notamment le prétraitement et la caractérisation des données, et les valeurs des hyperparamètres.
1. La compréhension de la relation entre les variables d’entrée (également appelées « caractéristiques ») et la sortie du modèle.  Le fait de connaître à la fois l’importance et la nature de l’impact de chaque caractéristique sur la valeur prédite aide à mieux comprendre et expliquer le modèle. On parle de « l’importance des caractéristiques ».

Vous pouvez activer la formation de la demande post importance de fonctionnalité globale pour le pipeline de votre choix, ou activer pour tous les pipelines d’apprentissage machine automatisés dans le cadre. Dans les secteurs fortement réglementés, comme les soins de santé et des informations bancaires, cela est essentiel pour se conformer aux réglementations et les meilleures pratiques.  Voici quelques scénarios réels pour illustrer :

1. Une société de fabrication à l’aide de machine learning pour prédire l’échec de l’instrument futures, par conséquent, ils peuvent effectuer de manière proactive activité de maintenance. Une fois que vous connaissez qu'un instrument est sur le point d’échec, ce qui est la cause la plus probable passe afin que la maintenance préventive peut être effectuée rapidement ?
1. Une institution financière à l’aide de machine learning pour l’emprunt de processus ou applications de carte de crédit. Comment savoir si le modèle fait l’attitude, et si un client demande pour plus d’informations sur pourquoi leur application a été rejetée, comment vous répondra leur ?
1. Un détaillant en ligne ou un fournisseur de logiciels indépendants à l’aide de machine learning pour prédire l’ATTRITION des clients. Quels sont les principaux contributeurs au client d’évolution, et comment empêcher les clients à partir du produit ?

Il s’agit d’une fonctionnalité en préversion et nous continuerons à investir dans la fourniture des informations plus détaillées pour vous aider à mieux comprendre vos modèles machine learning. Suivez cet [exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/model-explanation/auto-ml-model-explanation.ipynb) pour expérimenter les explications des modèles dans Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

Consultez les exemples et découvrez comment générer des modèles à l’aide du machine learning automatisé :
+ [Exemples : Utiliser des notebooks Jupyter pour explorer Azure Machine Learning service](samples-notebooks.md#automated-ml-setup)

+ [Tutoriel : Entraîner automatiquement un modèle de classification avec le Machine Learning Azure automatisé](tutorial-auto-train-models.md).

+ [Utiliser l’entraînement automatique sur une ressource distante](how-to-auto-train-remote.md)

+ [Configurer les paramètres pour l’entraînement automatique](how-to-configure-auto-train.md)
