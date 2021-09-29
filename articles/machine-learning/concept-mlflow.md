---
title: MLflow et Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez MLflow avec Azure Machine Learning pour enregistrer les métriques et les artefacts de modèles de ML, et déployez vos modèles ML en tant que service web.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 05/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a4ade0567f28cef23fe0752a1aa6ec018464ad01
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124792065"
---
# <a name="mlflow-and-azure-machine-learning"></a>MLflow et Azure Machine Learning

[MLflow](https://www.mlflow.org) est une bibliothèque open source permettant de gérer le cycle de vie de vos expériences de Machine Learning.  L’API de journalisation et l’URI de suivi de MLFlow, collectivement Suivi MLflow, forment un composant de MLflow qui journalise et suit vos artefacts de modèle et métriques d’exécution d’apprentissage, quel que soit l’environnement de votre expérience, localement sur votre ordinateur, sur une cible de calcul distante, sur une machine virtuelle ou sur un [cluster Azure Databricks](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api). 

Ensemble, le Suivi MLflow et Azure Machine Learning vous permettent de suivre les métriques d’exécution d’une expérience et de stocker les artefacts du modèle dans votre espace de travail Azure Machine Learning. Cette expérience aurait pu être exécutée localement sur votre ordinateur, sur une cible de calcul distante, une machine virtuelle ou un [cluster Azure Databricks](how-to-use-mlflow-azure-databricks.md). 

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparer les clients MLflow et Azure Machine Learning

 Le tableau suivant récapitule les différents clients pouvant utiliser Azure Machine Learning et leurs fonctionnalités respectives.

 MLflow Tracking offre des fonctionnalités de stockage de journalisation de métriques et d’artefact qui ne sont autrement disponibles que via le [Kit de développement logiciel (SDK) Python Azure Machine Learning](/python/api/overview/azure/ml/intro).

| Fonctionnalité | Suivi et déploiement MLflow | SDK Python Azure Machine Learning |  Interface CLI Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gérer l'espace de travail |   | ✓ | ✓ | ✓ |
| Utiliser des magasins de données  |   | ✓ | ✓ | |
| Journaliser les métriques      | ✓ | ✓ |   | |
| Charger les artefacts | ✓ | ✓ |   | |
| Afficher les mesures     | ✓ | ✓ | ✓ | ✓ |
| Gérer le calcul   |   | ✓ | ✓ | ✓ |
| Déployer des modèles    | ✓ | ✓ | ✓ | ✓ |
|Surveiller les performances d'un modèle||✓|  |   |
| Détecter une dérive de données |   | ✓ |   | ✓ |


## <a name="track-experiments"></a>Suivre les expériences

Avec le suivi MLflow, vous pouvez connecter Azure Machine Learning comme backend de vos expériences MLflow. En faisant cela, vous pouvez effectuer les tâches suivantes,

+ Suivre et journaliser les métriques et les artefacts des expériences dans votre [espace de travail Azure Machine Learning](./concept-azure-machine-learning-architecture.md#workspace). Si vous utilisez déjà MLflow Tracking pour vos expériences, l’espace de travail fournit un emplacement centralisé, sécurisé et évolutif pour stocker les métriques et les modèles d’entraînement. 

+ Suivre et gérer des modèles dans MLflow et un registre de modèles Azure Machine Learning.

+ [Suivre les exécutions de formation Azure Databricks](how-to-use-mlflow-azure-databricks.md).

Pour plus d’informations, consultez [Suivre des modèles ML avec MLflow et Azure Machine Learning](how-to-use-mlflow.md). 

## <a name="train-mlflow-projects"></a>Effectuer l’apprentissage de projets MLflow

Vous pouvez utiliser l’URI de suivi et l’API de journalisation de MLflow, collectivement appelés MLflow Tracking, pour envoyer des travaux d’entraînement avec la prise en charge back-end de [MLflow Projects](https://www.mlflow.org/docs/latest/projects.html) et Azure Machine Learning (préversion). Vous pouvez envoyer des tâches en local en utilisant le suivi Azure Machine Learning ou migrer vos exécutions vers le cloud, par exemple, via une [capacité de calcul Azure Machine Learning](./how-to-create-attach-compute-cluster.md)de calcul.

Pour en savoir plus, consultez [Entraîner des modèles ML avec MLflow Projects et Azure Machine Learning](how-to-train-mlflow-projects.md).


## <a name="deploy-mlflow-experiments"></a>Déployer des expériences MLflow

Vous pouvez [déployer votre modèle MLflow en tant que service web Azure](how-to-deploy-mlflow-models.md) afin d’exploiter les capacités d’Azure Machine Learning en matière de gestion de modèles et de détection des dérives de données et de les appliquer à vos modèles de production.

## <a name="next-steps"></a>Étapes suivantes
* [Suivre des modèles ML avec MLflow et Azure Machine Learning.](how-to-use-mlflow.md) 
* [Entraîner des modèles ML avec MLflow Projects et Azure Machine Learning](how-to-train-mlflow-projects.md).
* [Suivez les exécutions d’Azure Databricks avec MLflow](how-to-use-mlflow-azure-databricks.md).
* [Déployer des modèles avec MLflow](how-to-deploy-mlflow-models.md).


