---
title: Formation avec MLflow Projects
titleSuffix: Azure Machine Learning
description: Configurer MLflow avec Azure Machine Learning pour journaliser des métriques et des artefacts à partir de modèles ML
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 05/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 912ad0b1cbb7814774a06cf890e3618ee06b2c0a
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382570"
---
# <a name="train-ml-models-with-mlflow-projects-and-azure-machine-learning-preview"></a>Entraîner des modèles ML avec MLflow Projects et Azure Machine Learning (préversion)

Dans cet article, découvrez comment activer l’URI de suivi et l’API de journalisation de MLflow, collectivement appelés [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), pour envoyer des travaux d’entraînement avec la prise en charge back-end de [MLflow Projects](https://www.mlflow.org/docs/latest/projects.html) et Azure Machine Learning (préversion). Vous pouvez envoyer des tâches en local en utilisant le suivi Azure Machine Learning ou migrer vos exécutions vers le cloud, par exemple, via une [capacité de calcul Azure Machine Learning](./how-to-create-attach-compute-cluster.md)de calcul.

Grâce à [MLflow Projects](https://mlflow.org/docs/latest/projects.html), vous pouvez organiser et décrire votre code pour que d’autres scientifiques de données (ou des outils automatisés) puissent l’exécuter. MLflow Projects avec Azure Machine Learning vous permet de suivre et de gérer vos exécutions d’entraînement dans votre espace de travail.

[MLflow](https://www.mlflow.org) est une bibliothèque open source permettant de gérer le cycle de vie de vos expériences de Machine Learning. MLFlow Tracking est un composant de MLflow qui journalise et suit vos artefacts de modèle et métriques d’exécution d’apprentissage, quel que soit l’environnement de votre expérience, localement sur votre ordinateur, sur une cible de calcul distante, sur une machine virtuelle ou sur un [cluster Azure Databricks](how-to-use-mlflow-azure-databricks.md).

[Découvrez plus en détail l’intégration de MLflow et d’Azure Machine Learning.](how-to-use-mlflow.md)

>[!NOTE]
> En tant que bibliothèque open source, MLflow change fréquemment. Ainsi, les fonctionnalités mises à disposition via l’intégration d’Azure Machine Learning et de MLflow doivent être considérées comme une préversion et non entièrement prises en charge par Microsoft.

> [!TIP]
> Les informations contenues dans ce document sont principalement destinées aux scientifiques des données et aux développeurs qui veulent superviser le processus d’entraînement du modèle. Si vous êtes un administrateur intéressé par la supervision de l’utilisation de ressources et d’événements d’Azure Machine Learning, comme des quotas, des cycles de formation accomplis ou des déploiements de modèles effectués, voir [Supervision d’Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Prérequis

* Installez le package `azureml-mlflow`.
    * Ce package intègre automatiquement `azureml-core` du [Kit de développement logiciel (SDK) Python Azure Machine Learning](/python/api/overview/azure/ml/install), qui fournit la connectivité nécessaire pour que MLflow accède à votre espace de travail.
* [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md).
    * Découvrez les [autorisations d’accès nécessaires pour effectuer vos opérations MLflow avec votre espace de travail](how-to-assign-roles.md#mlflow-operations).

## <a name="train-mlflow-projects-on-local-compute"></a>Entraîner MLflow Projects sur une capacité de calcul locale

Cet exemple montre comment soumettre des projets MLflow en local en utilisant le suivi Azure Machine Learning.

Installez le package `azureml-mlflow` pour utiliser MLflow Tracking avec Azure Machine Learning sur vos expériences exécutées en local. Vos expériences peuvent s’exécuter à l’aide d’un notebook Jupyter ou d’un éditeur de code.

```shell
pip install azureml-mlflow
```

Importez les classes `mlflow` et [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) pour accéder à l’URI de suivi de MLflow et configurer votre espace de travail.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Définissez le nom de l’expérience MLflow avec `set_experiment()` et commencez votre cycle d’apprentissage avec `start_run()`. Utilisez ensuite `log_metric()` pour activer l’API de journalisation de MLflow et commencer la journalisation des métriques de votre cycle de formation.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Créez l’objet de configuration principal pour stocker les informations nécessaires à l’intégration, telles que la cible de calcul et le type d’environnement géré à utiliser.

```python
backend_config = {"USE_CONDA": False}
```

Ajoutez le package `azureml-mlflow` en tant que dépendance PIP à votre fichier de configuration d’environnement afin d’effectuer le suivi des métriques et des artefacts principaux dans votre espace de travail. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```

Soumettez l’exécution locale et veillez à définir le paramètre `backend = "azureml" `. Ce paramètre vous permet de soumettre des exécutions en local et bénéficier de la prise en charge supplémentaire du suivi automatique de la sortie, des fichiers journaux, des instantanés et des erreurs imprimées dans votre espace de travail.

Affichez vos exécutions et métriques dans [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="train-mlflow-projects-with-remote-compute"></a>Entraîner des projets MLflow avec une capacité de calcul distante

Cet exemple montre comment envoyer des projets MLflow sur une capacité de calcul distante avec le suivi Azure Machine Learning.

Installez le package `azureml-mlflow` pour utiliser MLflow Tracking avec Azure Machine Learning sur vos expériences exécutées en local. Vos expériences peuvent s’exécuter à l’aide d’un notebook Jupyter ou d’un éditeur de code.

```shell
pip install azureml-mlflow
```

Importez les classes `mlflow` et [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) pour accéder à l’URI de suivi de MLflow et configurer votre espace de travail.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Définissez le nom de l’expérience MLflow avec `set_experiment()` et commencez votre cycle d’apprentissage avec `start_run()`. Utilisez ensuite `log_metric()` pour activer l’API de journalisation de MLflow et commencer la journalisation des métriques de votre cycle de formation.

```Python
experiment_name = 'train-mlflow-project-amlcompute'
mlflow.set_experiment(experiment_name)
```

Créez l’objet de configuration principal pour stocker les informations nécessaires à l’intégration, telles que la cible de calcul et le type d’environnement géré à utiliser.

L’intégration accepte « COMPUTE » et « USE_CONDA » comme paramètres, « COMPUTE » correspondant au nom de votre cluster de calcul distant et « USE_CONDA » créant un environnement pour le projet à partir du fichier de configuration d’environnement. Si « COMPUTE » est présent dans l’objet, le projet est automatiquement envoyé à la capacité de calcul distante et ignore « USE_CONDA ». MLflow accepte un objet dictionnaire ou un fichier JSON.

```python
# dictionary
backend_config = {"COMPUTE": "cpu-cluster", "USE_CONDA": False}
```

Ajoutez le package `azureml-mlflow` en tant que dépendance PIP à votre fichier de configuration d’environnement afin d’effectuer le suivi des métriques et des artefacts principaux dans votre espace de travail. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```

Envoyez l’exécution du projet mlflow et veillez à définir le paramètre `backend = "azureml" `. Ce paramètre vous permet d’envoyer votre exécution à votre capacité de calcul distante et de bénéficier de la prise en charge supplémentaire du suivi de sortie automatique, des fichiers journaux, des instantanés et des erreurs imprimées dans votre espace de travail.

Affichez vos exécutions et métriques dans [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

```python
remote_mlflow_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    backend_config = backend_config, 
                                    )

```


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas d’utiliser les métriques et artefacts enregistrés dans votre espace de travail, la possibilité de les supprimer individuellement est actuellement indisponible. Au lieu de cela, supprimez le groupe de ressources contenant le compte de stockage et l’espace de travail afin d’éviter des frais supplémentaires :

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche.

   ![Supprimer dans le portail Azure](./media/how-to-use-mlflow/delete-resources.png)

1. À partir de la liste, sélectionnez le groupe de ressources créé.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez le nom du groupe de ressources. Puis sélectionnez **Supprimer**.

## <a name="example-notebooks"></a>Exemples de notebooks

Les [notebooks MLflow avec Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) illustrent et développent les concepts abordés dans cet article.

  * [Entraîner un projet MLflow sur une capacité de calcul locale](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow/train-projects-local/train-projects-local.ipynb)
  * [Entraînez un projet MLflow sur une capacité de calcul distante](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow/train-projects-remote/train-projects-remote.ipynb).

> [!NOTE]
> Vous trouverez un référentiel communautaire d’exemples utilisant mlflow sur https://github.com/Azure/azureml-examples.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des modèles avec MLflow](how-to-deploy-mlflow-models.md).
* Surveillez la [dérive des données](./how-to-enable-data-collection.md) de vos modèles de production.
* [Suivez les exécutions d’Azure Databricks avec MLflow](how-to-use-mlflow-azure-databricks.md).
* [Gérez vos modèles](concept-model-management-and-deployment.md).
