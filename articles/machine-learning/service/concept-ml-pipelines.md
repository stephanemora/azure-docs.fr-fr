---
title: 'Pipelines : optimiser les workflows Machine Learning'
titleSuffix: Azure Machine Learning service
description: Dans cet article, découvrez des informations sur les pipelines de machine learning que vous pouvez créer avec le SDK Azure Machine Learning pour Python et les avantages de l’utilisation des pipelines. Les pipelines Machine Learning sont utilisés par les scientifiques des données pour créer, optimiser et gérer leurs flux de travail de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 12/4/2018
ms.custom: seodec18
ms.openlocfilehash: 3f1dd0921153d6b65bdc257f91019483adbb18fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821080"
---
# <a name="build-machine-learning-pipelines-with-the-azure-machine-learning-service"></a>Créer des pipelines de Machine Learning avec Azure Machine Learning service

Cet article présente les pipelines de Machine Learning que l’on peut créer avec le kit SDK Azure Machine Learning pour Python, ainsi que les avantages des pipelines.

## <a name="what-are-machine-learning-pipelines"></a>Présentation des pipelines de machine learning

À l’aide de pipelines Machine Learning (ML), les scientifiques des données, ingénieurs des données et professionnels de l’informatique peuvent collaborer sur les étapes suivantes :
+ Préparation des données, comme les normalisations et les transformations
+ Apprentissage du modèle
+ Évaluation de modèle
+ Déploiement 

Le diagramme suivant montre un exemple de pipeline :

![Pipelines de Machine Learning dans Azure Machine Learning service](./media/concept-ml-pipelines/pipelines.png)

### <a name="which-azure-pipeline-technology-should-i-use"></a>Quelle technologie de pipeline Azure dois-je utiliser ?

Le cloud Azure offre plusieurs autres pipelines, chacun avec un objectif différent. Le tableau suivant répertorie les pipelines différents et qu’ils sont utilisés pour :

| Pipeline | Résultat | Canal canonique |
| ---- | ---- | ---- |
| Azure Machine Learning pipelines | Définit des flux de travail qui peut être utilisé en tant que modèle pour vos scénarios d’apprentissage d’apprentissage réutilisable. | Données -> modèle |
| [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Le déplacement des données de groupes, de transformation et de contrôler les activités nécessaires pour effectuer une tâche.  | Données -> données |
| [Pipelines Azure](https://azure.microsoft.com/services/devops/pipelines/) | Intégration et livraison de votre application à tout any/plateforme cloud  | Code -> application/service |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Pourquoi créer des pipelines dans Azure Machine Learning ?

Le [Kit SDK Azure Machine Learning pour Python](#the-python-sdk-for-pipelines) permet de créer des pipelines ML, ainsi que de soumettre et suivre chacune des exécutions des différents pipelines.

Avec les pipelines, vous pouvez optimiser votre flux de travail en profitant de ces avantages : simplicité, rapidité, portabilité et réutilisation. Le fait de créer des pipelines avec Azure Machine Learning vous permet de vous concentrer sur votre domaine d’expertise, le Machine Learning, plutôt que sur l’infrastructure.

Les étapes étant distinctes les unes des autres, vous pouvez réexécuter uniquement les étapes nécessaires lorsque vous modifiez et testez votre workflow. Une étape est une unité de calcul dans le pipeline. Comme l’illustre le diagramme précédent, la tâche de préparation des données peut comporter de nombreuses étapes, Ces étapes incluent, mais vous n’êtes pas limités à la normalisation, de transformation, de validation et de personnalisation de la. Les sources de données et données intermédiaires sont réutilisées dans le pipeline, ce qui permet d'économiser du temps de calcul et des ressources. 

Une fois conçu, le pipeline fait souvent l’objet de réglages précis au cours du cycle d’entraînement du pipeline. Quand vous réexécutez un pipeline, l’exécution passe directement aux étapes qui doivent être réexécutées, par exemple un script d’entraînement mis à jour, et ignore ce qui n’a pas changé. Le même paradigme s’applique aux scripts inchangés utilisés pour l’exécution de l’étape. 

Avec Azure Machine Learning, vous pouvez utiliser différentes boîtes à outils et infrastructures, telles que PyTorch ou TensorFlow, pour chaque étape dans votre pipeline. Azure effectue la coordination entre les différentes [cibles de calcul](concept-azure-machine-learning-architecture.md) utilisées, afin que les données intermédiaires soient faciles à partager avec les cibles de calcul en aval. 

Vous pouvez [suivre les mesures de vos expérimentations de pipeline](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) directement dans le portail Microsoft Azure. 

## <a name="key-advantages"></a>Principaux avantages

Voici les principaux avantages qu’il y a à créer des pipelines pour des workflows de Machine Learning :

|Avantage principal|Description|
|:-------:|-----------|
|**Exécutions sans&nbsp; assistance**|Planifiez quelques étapes à exécuter en parallèle ou en séquence de façon fiable et sans assistance. La préparation et la modélisation des données peuvent prendre des jours ou des semaines. Vous pouvez donc vous concentrer à présent sur d’autres tâches pendant l’exécution de votre pipeline. |
|**Capacités de calcul mixtes et variées**|Utilisez plusieurs pipelines qui sont coordonnés de façon fiable entre des capacités de calcul et des stockages hétérogènes et évolutifs. Vous pouvez exécuter les étapes d’un pipeline une par une sur les différentes cibles de calcul, comme HDInsight, les machines virtuelles Data Science VM sur GPU et Databricks, de façon à utiliser efficacement les options de calcul disponibles.|
|**Réutilisabilité**|Vous pouvez transformer des pipelines en modèles pour des scénarios précis, comme le réentraînement et le scoring par lots. Déclenchez-les à partir de systèmes externes par de simples appels REST.|
|**Suivi et gestion de version**|Au lieu de suivre manuellement les données et les chemins d’accès aux résultats au fil des itérations, utilisez le kit SDK Pipelines pour nommer vos sources de données, vos entrées et vos sorties et en contrôler les versions. Vous pouvez aussi gérer séparément les scripts et les données pour augmenter la productivité.|

## <a name="the-python-sdk-for-pipelines"></a>Le SDK Python pour les pipelines

Utilisez Python pour créer vos pipelines ML. Le SDK Azure Machine Learning offre les constructions nécessaires pour séquencer et paralléliser les étapes de vos pipelines quand aucune dépendance de données n’est présente. Vous pouvez interagir avec lui dans des notebooks Jupyter ou dans l’environnement de développement intégré de votre choix. 

Grâce aux dépendances de données déclaratives, vous pouvez optimiser vos tâches. Le kit SDK comporte un framework de modules prédéfinis pour les tâches courantes, comme le transfert de données et la publication de modèles. Vous pouvez étendre ce framework de façon à modéliser vos propres conventions en implémentant des étapes personnalisées et réutilisables d’un pipeline à l’autre. Les cibles de calcul et ressources de stockage sont par ailleurs directement gérables dans le kit SDK.

Vous pouvez enregistrer les pipelines sous forme de modèles et les déployer sur un point de terminaison REST pour pouvoir planifier des tâches de scoring par lots ou de réentraînement.

Pour savoir comment créer votre propre pipeline, voir [Documents de référence du kit SDK Python pour les pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) et le notebook de la section suivante.

## <a name="example-notebooks"></a>Exemples de notebooks
 
Les notebooks suivants illustrent des pipelines avec Azure Machine Learning : [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer votre premier pipeline](how-to-create-your-first-pipeline.md).
