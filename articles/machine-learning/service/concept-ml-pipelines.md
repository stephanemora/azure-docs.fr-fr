---
title: Présentation des pipelines ML
titleSuffix: Azure Machine Learning service
description: Dans cet article, découvrez des informations sur les pipelines de machine learning que vous pouvez créer avec le SDK Azure Machine Learning pour Python et les avantages de l’utilisation des pipelines. Les pipelines Machine Learning sont utilisés par les scientifiques des données pour créer, optimiser et gérer leurs flux de travail de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 22d1da4c194b392993b37b16ab20673120c3362e
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951792"
---
# <a name="what-are-ml-pipelines-in-azure-machine-learning-service"></a>Que sont les pipelines ML dans Azure Machine Learning service ?

Découvrez les pipelines de Machine Learning que vous pouvez créer et gérer avec Azure Machine Learning service. 

À l’aide de pipelines Machine Learning (ML), les scientifiques des données, ingénieurs des données et professionnels de l’informatique peuvent collaborer sur les étapes suivantes :
+ Préparation des données, comme les normalisations et les transformations
+ Apprentissage du modèle
+ Évaluation de modèle
+ Déploiement

Découvrez comment [créer votre premier pipeline](how-to-create-your-first-pipeline.md).

![Pipelines de Machine Learning dans Azure Machine Learning service](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Quelle technologie de pipeline Azure dois-je utiliser ?

Le cloud Azure offre plusieurs autres pipelines, chacun ayant un objectif différent. Le tableau suivant liste les différents pipelines et une explication de leur utilisation :

| Pipeline | Résultat | Canal canonique |
| ---- | ---- | ---- |
| Pipelines Azure Machine Learning | Définit des workflows Machine Learning réutilisables qui peuvent être utilisés comme modèle pour vos scénarios de Machine Learning. | Données -> modèle |
| [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Regroupe les activités de déplacement, de transformation et de contrôle des données nécessaires pour effectuer une tâche.  | Données -> données |
| [Pipelines Azure](https://azure.microsoft.com/services/devops/pipelines/) | Intégration et livraison continues de votre application à n’importe quelle plateforme/n’importe quel cloud  | Code -> application/service |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Pourquoi créer des pipelines dans Azure Machine Learning ?

Les pipelines de Machine Learning optimisent votre workflow en termes de vitesse, de portabilité et de réutilisation pour que vous puissiez vous concentrer sur votre domaine d’expertise, le Machine Learning, plutôt que sur l’infrastructure et l’automatisation.

Les pipelines sont construits à partir de plusieurs **étapes**, qui sont des unités de calcul distinctes dans le pipeline. Chaque étape peut s’exécuter indépendamment et utiliser des ressources de calcul isolées. Cela permet à plusieurs scientifiques de données de travailler sur le même pipeline en même temps sans surcharge des ressources de calcul, et cela facilite également l’utilisation de différents types/tailles de calcul pour chaque étape.

Une fois conçu, le pipeline fait souvent l’objet de réglages précis au cours du cycle d’entraînement du pipeline. Lorsque vous réexécutez un pipeline, l’exécution passe directement aux différentes étapes qui doivent être réexécutées, par exemple un script de formation mis à jour, et ignore ce qui n’a pas changé. Le même paradigme s’applique aux scripts inchangés utilisés pour l’exécution de l’étape. Cette fonctionnalité permet d’éviter l’exécution d’étapes coûteuses et longues, telles que l’ingestion et la transformation de données si les données sous-jacentes n’ont pas changé.

Avec Azure Machine Learning, vous pouvez utiliser différents kits de ressources et frameworks, comme PyTorch ou TensorFlow, à chaque étape de votre pipeline. Azure effectue la coordination entre les différentes [cibles de calcul](concept-azure-machine-learning-architecture.md) utilisées, afin que les données intermédiaires soient faciles à partager avec les cibles de calcul en aval.

Vous pouvez [suivre les mesures de vos expérimentations de pipeline](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) directement dans le portail Microsoft Azure. Après la publication d’un pipeline, vous pouvez configurer un point de terminaison REST qui vous permet de réexécuter le pipeline à partir de n’importe quelle plateforme ou pile.

## <a name="key-advantages"></a>Principaux avantages

Voici les principaux avantages de l’utilisation des pipelines pour des workflows de Machine Learning :

|Avantage principal|Description|
|:-------:|-----------|
|**Exécutions sans&nbsp; assistance**|Planifiez des étapes à exécuter en parallèle ou en séquence de façon fiable et sans assistance. La préparation et la modélisation des données peuvent prendre des jours voire des semaines, et les pipelines vous permettent de vous concentrer sur d’autres tâches pendant l’exécution du processus. |
|**Calcul hétérogène**|Utilisez plusieurs pipelines qui sont coordonnés de façon fiable entre des ressources de calcul et des emplacements de stockage hétérogènes et évolutifs. Exécutez les étapes d’un pipeline une par une sur les différentes cibles de calcul, comme HDInsight, les machines virtuelles Data Science VM sur GPU et Databricks, de façon à utiliser efficacement les options de calcul disponibles.|
|**Réutilisabilité**|Créez des modèles de pipeline pour des scénarios précis, comme le réentraînement et le scoring par lots. Déclenchez des pipelines publiés à partir de systèmes externes par de simples appels REST.|
|**Suivi et gestion de version**|Au lieu de suivre manuellement les données et les chemins d’accès aux résultats au fil des itérations, utilisez le kit SDK Pipelines pour nommer vos sources de données, vos entrées et vos sorties et en contrôler les versions. Vous pouvez aussi gérer séparément les scripts et les données pour augmenter la productivité.|
|**Collaboration**|Les pipelines permettent aux scientifiques des données de collaborer dans toutes les phases du processus de conception du Machine Learning tout en travaillant simultanément sur les étapes de pipeline.|

## <a name="the-python-sdk-for-pipelines"></a>Le SDK Python pour les pipelines

[Utilisez le Kit de développement logiciel (SDK) Python](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) pour créer vos pipelines ML dans vos notebooks IDE ou Jupyter préférés. Le SDK Azure Machine Learning offre les constructions nécessaires pour séquencer et paralléliser les étapes de vos pipelines quand aucune dépendance de données n’est présente. 

Grâce aux dépendances de données déclaratives, vous pouvez optimiser vos tâches. Le kit SDK comporte un framework de modules prédéfinis pour les tâches courantes, comme le transfert de données et la publication de modèles. Vous pouvez étendre cette infrastructure de façon à modéliser vos propres conventions en implémentant des étapes personnalisées et réutilisables d’un pipeline à l’autre. Les cibles de calcul et ressources de stockage sont par ailleurs directement gérables dans le kit SDK.

Enregistrez vos pipelines sous forme de modèles et déployez-les sur un point de terminaison REST pour le scoring par lots ou le réentraînement.

Il existe deux packages Python pour les pipelines avec Azure Machine Learning : [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) et [azureml-pipeline-steps](https://docs.microsoft.com/en-us/python/api/azureml-pipeline-steps/?view=azure-ml-py).

## <a name="next-steps"></a>Étapes suivantes

+ Découvrez comment [créer votre premier pipeline](how-to-create-your-first-pipeline.md).

+ Découvrez comment [exécuter des prédictions par lots sur de grandes quantités de données](how-to-run-batch-predictions.md).

+ Lisez les [documents de référence sur le Kit de développement logiciel (SDK) pour les pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)

+ Testez les exemples de notebooks Jupyter illustrant des [pipelines Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Découvrez comment [exécuter des notebooks pour explorer ce service](samples-notebooks.md).
