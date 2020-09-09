---
title: Soumettre une exécution de formation à une cible de calcul
titleSuffix: Azure Machine Learning
description: Effectuez l’apprentissage de votre modèle Machine Learning sur divers environnements de formation (cibles de calcul). Vous pouvez facilement basculer entre différents environnements d’entraînement. Commencer l’entraînement en local. Si un scale-out est nécessaire, basculez vers une cible de calcul basée sur le cloud.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: ca76ec5bef1d908ca3cea6ce0f58b1205c1676ca
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144088"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>Soumettre une exécution de formation à une cible de calcul

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous découvrirez comment utiliser différents environnements de formation ([cibles de calcul](concept-compute-target.md)) servant à effectuer l’apprentissage de vos modèles Machine Learning.

Lors de l’apprentissage, il est courant de commencer par exécuter le script d’apprentissage sur l’ordinateur local, avant de l’exécuter sur une autre cible de calcul. Avec Azure Machine Learning, vous pouvez exécuter votre script sur différentes cibles de calcul sans avoir à modifier votre script de formation.

Il vous suffit de définir l’environnement pour chaque cible de calcul dans une **configuration d’exécution de script**.  Ensuite, lorsque vous souhaitez exécuter votre expérience de formation sur une autre cible de calcul, spécifiez la configuration de série de tests pour celle-ci.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui
* Le [kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md), `ws`
* Une cible de calcul, `my_compute_target`.  Créez une cible de calcul avec :
  * [Kit de développement logiciel (SDK) Python](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Qu’est-ce qu’une configuration d’exécution de script ?

Vous soumettez votre expérience de formation à l’aide d’un objet [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py).  Cet objet inclut ce qui suit :

* **source_directory** : Répertoire source contenant votre script d’apprentissage
* **script** : Identifiez le script d’apprentissage
* **run_config** : [Configuration d’exécution](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) qui définit où aura lieu l’apprentissage. Dans le `run_config`, vous spécifiez la cible de calcul et l’environnement à utiliser lors de l’exécution du script de formation.  

## <a name="whats-an-environment"></a>Qu’est-ce qu’un environnement ?

Les [environnements](concept-environments.md) Azure Machine Learning sont une encapsulation de l’environnement dans lequel votre formation Machine Learning se produit. Ils spécifient les packages, variables d’environnement et paramètres logiciels Python autour de vos scripts d’apprentissage et de scoring. Ils spécifient également les temps d’exécution (Python, Spark ou Docker).  

Les environnements sont spécifiés dans l’objet `run_config` à l’intérieur d’un `ScriptRunConfig`.

## <a name="train-your-model"></a><a id="submit"></a>Effectuer l’apprentissage de votre modèle

Le modèle de code pour soumettre une série de tests d’apprentissage est le même pour tous les types de cibles de calcul :

1. Créer une expérience à exécuter
1. Créer un environnement dans lequel le script sera exécuté
1. Créer une configuration d’exécution de script, qui fait référence à la cible de calcul et à l’environnement
1. Soumettre l’exécution
1. Attendre la fin de l’exécution

Vous pouvez également :

* Soumettre l’expérience avec un objet `Estimator`, comme indiqué dans [Former des modèles ML avec des estimateurs](how-to-train-ml-models.md).
* Soumettre une série de tests HyperDrive pour un [réglage d’hyperparamètre](how-to-tune-hyperparameters.md).
* Soumettre une expérience via l’[extension VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Créer une expérience

Créez une expérience dans votre espace de travail.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>Créer un environnement

Les environnements organisés contiennent des collections de packages Python et sont disponibles dans votre espace de travail par défaut. Ces environnements s’appuient sur des images Docker mises en cache, ce qui réduit le coût de préparation de l’exécution. Pour une cible de calcul à distance, vous pouvez utiliser l’un de ces environnements organisés courants pour commencer :

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Pour plus d’informations et pour obtenir des détails sur les environnements, consultez [Créer et utiliser des environnements logiciels dans Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a>Cible de calcul locale

Si votre cible de calcul est votre **ordinateur local**, vous devez vous assurer que tous les packages nécessaires sont disponibles dans l’environnement Python où le script s’exécute.  Utilisez `python.user_managed_dependencies` pour utiliser votre environnement Python actuel (ou celui sous le chemin d’accès que vous spécifiez).

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>Créer une configuration d’exécution de script

Maintenant que vous avez une cible de calcul (`compute_target`) et un environnement (`my_environment`), créez une configuration d’exécution de script qui exécute votre script de formation (`train.py`) situé dans votre répertoire `project_folder` :

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

Vous pouvez également définir l’infrastructure pour votre exécution.

* Pour un cluster HDI :
    ```python
    src.run_config.framework = "pyspark"
    ```

* Pour une machine virtuelle distante :
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>Soumettre l’expérimentation

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> Quand vous soumettez la série de tests d’apprentissage, un instantané du répertoire contenant vos scripts d’apprentissage est créé et envoyé à la cible de calcul. Il est également stocké dans le cadre de l’expérience dans votre espace de travail. Si vous modifiez des fichiers et que vous soumettez à nouveau l’exécution, seuls les fichiers modifiés seront téléchargés.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Pour plus d’informations sur les instantanés, consultez [Instantanés](concept-azure-machine-learning-architecture.md#snapshots).


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Intégration et suivi Git

Lorsque vous lancez une exécution d’entraînement où le répertoire source est un répertoire Git local, les informations relatives au répertoire sont stockées dans l’historique des exécutions. Pour plus d’informations, consultez [Obtenir une intégration pour Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Exemples de notebooks

Pour des exemples d’apprentissage avec différentes cibles de calcul, voir les blocs-notes suivants :
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Former un modèle](tutorial-train-models-with-aml.md) utilise une cible de calcul gérée pour former un modèle.
* Découvrez comment [optimiser efficacement les hyperparamètres](how-to-tune-hyperparameters.md) afin de générer des modèles plus efficaces.
* Une fois le modèle formé, découvrez [comment et où déployer les modèles](how-to-deploy-and-where.md).
* Consultez la documentation de référence du Kit de développement logiciel (SDK) de la [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Utiliser Azure Machine Learning avec des réseaux virtuels Azure](how-to-enable-virtual-network.md)
