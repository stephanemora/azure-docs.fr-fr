---
title: Créer des pipelines de machine learning - Service Azure Machine Learning
description: Dans cet article, découvrez des informations sur les pipelines de machine learning que vous pouvez créer avec le SDK Azure Machine Learning pour Python et les avantages de l’utilisation des pipelines. Les pipelines Machine Learning sont utilisés par les scientifiques des données pour créer, optimiser et gérer leurs flux de travail de machine learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 09/24/2018
ms.openlocfilehash: 45aa954d2f85267b2c7c9aa2a7ba04e436765433
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023921"
---
# <a name="pipelines-and-azure-machine-learning"></a>Pipelines et Azure Machine Learning

Dans cet article, découvrez des informations sur les pipelines de machine learning que vous pouvez créer avec le SDK Azure Machine Learning pour Python et les avantages de l’utilisation des pipelines.

## <a name="what-are-machine-learning-pipelines"></a>Présentation des pipelines de machine learning

Les pipelines Machine Learning sont utilisés par les scientifiques des données pour créer, optimiser et gérer leurs flux de travail de machine learning. Un pipeline classique implique une séquence d’étapes qui couvrent les tâches suivantes :

+ Préparation des données, comme les normalisations et les transformations
+ Entraînement du modèle, comme l’optimisation et la validation des hyperparamètres
+ Déploiement et évaluation du modèle  

Le diagramme suivant montre un exemple de pipeline :

[ ![Pipelines Machine Learning dans le service Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png) ] (. / media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Pourquoi créer des pipelines dans Azure Machine Learning ?

Le [SDK Azure Machine Learning pour Python](#the-python-sdk-for-pipelines) peut être utilisé pour créer des pipelines ML, ainsi que pour soumettre et suivre des exécutions de pipelines individuels.

Avec les pipelines, vous pouvez optimiser votre flux de travail en profitant de ces avantages : simplicité, rapidité, portabilité et réutilisation. Lors de la création de pipelines avec Azure Machine Learning, vous pouvez vous concentrer sur ce que vous connaissez le mieux, c’est-à-dire le machine learning, et non pas sur l’infrastructure.

L’utilisation d’étapes distinctes rend possible la réexécution des seules étapes nécessaires dès lors que vous modifiez et que vous testez votre flux de travail. Une étape est une unité de calcul dans le pipeline. Comme illustré dans le diagramme ci-dessus, la tâche de préparation des données peut impliquer plusieurs étapes, y compris, mais pas seulement, la normalisation, la transformation, la validation et la caractérisation.

Une fois que le pipeline est conçu, des optimisations se font souvent au cours du cycle d’entraînement du pipeline. Quand vous réexécutez un pipeline, l’exécution passe directement aux étapes qui doivent être réexécutées, par exemple un script d’entraînement mis à jour, et ignore ce qui n’a pas changé. Le même paradigme s’applique aux scripts inchangés utilisés pour l’exécution de l’étape. 

Avec Azure Machine Learning, vous pouvez utiliser différentes boîtes à outils et infrastructures, comme Microsoft Cognitive Toolkit ou TensorFlow, pour chaque étape de votre pipeline. Azure effectue la coordination entre les différentes [cibles de calcul](concept-azure-machine-learning-architecture.md) que vous utilisez afin que vos données intermédiaires puissent être partagées facilement avec les cibles de calcul en aval. 

## <a name="key-advantages"></a>Principaux avantages

Les principaux avantages de la création de pipelines pour votre flux de travail de machine learning sont :

|Avantage principal|Description|
|:-------:|-----------|
|**Exécutions sans&nbsp; assistance**|Planifiez quelques étapes à exécuter en parallèle ou en séquence de façon fiable et sans assistance. Comme la préparation et la modélisation des données peut prendre des jours ou des semaines, vous pouvez maintenant vous concentrer sur d’autres tâches pendant que votre pipeline s’exécute. |
|**Capacités de calcul mixtes et variées**|Utilisez plusieurs pipelines qui sont coordonnés de façon fiable entre des capacités de calcul et des stockages hétérogènes et évolutifs. Des étapes individuelles d’un pipeline peuvent être exécutées sur le différentes cibles de calcul, comme HDInsight, des machines virtuelles DSVM sur GPU et Databricks, afin d’optimiser l’utilisation des options de calcul disponibles.|
|**Réutilisabilité**|Les pipelines peuvent être transformés en modèle pour des scénarios spécifiques, comme le réentraînement et le scoring par lots.  Ils peuvent être déclenchés depuis des systèmes externes via de simples appels REST.|
|**Suivi et gestion de version**|Au lieu de suivre manuellement les chemins des données et des résultats au fil des itérations, utilisez le SDK des pipelines pour nommer et gérer explicitement les versions des sources de données, des entrées et des sorties, et pour gérer les scripts et les données séparément, de façon à accroître votre productivité.|

## <a name="the-python-sdk-for-pipelines"></a>Le SDK Python pour les pipelines

Utilisez Python pour créer vos pipelines ML. Le SDK Azure Machine Learning offre les constructions nécessaires pour séquencer et paralléliser les étapes de vos pipelines quand aucune dépendance de données n’est présente. Vous pouvez interagir avec celui-ci dans des notebooks Jupyter ou dans un autre IDE. 

Grâce aux dépendances de données déclaratives, vous pouvez optimiser vos tâches. Le SDK inclut une infrastructure de modules prédéfinis pour les tâches courantes, comme le transfert de données, la création d’une cible de calcul et la publication de modèle. L’infrastructure peut être pour modéliser vos propres conventions en implémentant des étapes personnalisées qui sont réutilisables entre les pipelines.

Les pipelines peuvent être enregistrés en tant que modèles et peuvent être déployés sur un point de terminaison REST, ce qui vous permet de planifier des travaux de scoring par lots ou de réentraînement.

Consultez les [documents de référence du SDK Python pour les pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) et le notebook dans la section suivante pour voir comment créer votre propre pipeline.

## <a name="example-notebooks"></a>Exemples de notebooks
 
Le notebook suivant illustre l’utilisation des pipelines avec Azure Machine Learning : [pipeline/pipeline-batch-scoring.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb).
 
Consultez ce bloc-notes :
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
