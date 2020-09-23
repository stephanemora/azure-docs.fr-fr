---
title: Former un modèle à l’aide d’une image Docker personnalisée
titleSuffix: Azure Machine Learning
description: Apprenez à former des modèles avec des images Docker personnalisées dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: d90b56366cb22e80162983c982e861de608e4e9e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90893107"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>Former un modèle à l’aide d’une image Docker personnalisée

Découvrez comment utiliser une image Docker personnalisée lors du déploiement de modèles formés avec Azure Machine Learning. 

Les exemples de scripts de cet article sont utilisés pour classer les images de PET en créant un réseau neuronal de convolution. 

Même si Azure Machine Learning fournit une image Docker de base par défaut, vous pouvez également utiliser des environnements Azure Machine Learning pour spécifier une image de base spécifique, telle que l’un des ensembles d’images de base [Azure ML](https://github.com/Azure/AzureML-Containers) gérées ou votre propre [image personnalisée](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Les images de base personnalisées vous permettent de gérer précisément vos dépendances et d’exercer un contrôle rigoureux des versions des composants lors de l’exécution des travaux de formation. 

## <a name="prerequisites"></a>Prérequis 
Exécutez ce code sur l’un de ces environnements :
* Instance de calcul Azure Machine Learning : pas de téléchargement ni d’installation nécessaire
    * Suivre le [Tutoriel : Configurer l’environnement et l’espace de travail](tutorial-1st-experiment-sdk-setup.md) pour créer un serveur de notebook dédié préchargé avec le kit SDK et l’exemple de dépôt.
    * Dans le [référentiel d’exemples](https://github.com/Azure/azureml-examples) d’Azure Machine Learning, recherchez un bloc-notes terminé en accédant à ce répertoire : **notebooks > fastai > train-pets-resnet34.ipynb** 

* Votre propre serveur de notebooks Jupyter
    * Créer un [fichier de configuration d’espace de travail](how-to-configure-environment.md#workspace).
    * Le [Kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
    * Registre [Azure Container Registry](/azure/container-registry) ou autre registre Docker accessible sur Internet.

## <a name="set-up-the-experiment"></a>Configurer l’expérience 
Cette section configure l’expérience de formation en initialisant un espace de travail, en créant une expérience et en chargeant des données et des scripts de formation.

### <a name="initialize-a-workspace"></a>Initialiser un espace de travail
L’[espace de travail Azure Machine Learning](concept-workspace.md) est la ressource de niveau supérieur du service. Il vous fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez. Dans le kit de développement logiciel (SDK) Python, vous pouvez accéder aux artefacts de l’espace de travail en créant un objet [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true).

Créez un objet d’espace de travail à partir du fichier `config.json` créé dans la [section Conditions préalables](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Préparer les scripts
Dans ce tutoriel, le script de formation, **train.py**, est fourni [ici](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py). En pratique, vous pouvez prendre n’importe quel script d’entraînement tel quel et l’exécuter avec Azure Machine Learning.

### <a name="define-your-environment"></a>Définir votre environnement
Créez un objet d’environnement et activez Docker. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Cette image de base spécifiée prend en charge la bibliothèque fast.ai, qui permet d’obtenir des fonctionnalités de Deep Learning distribuées. Pour en savoir plus, visitez le [hub Docker](https://hub.docker.com/u/fastdotai). 

Lorsque vous utilisez votre image Docker personnalisée, vous avez peut-être déjà configuré votre environnement Python correctement. Dans ce cas, affectez la valeur true à l’indicateur `user_managed_dependencies` pour tirer parti de l’environnement Python intégré à votre image personnalisée. Par défaut, Azure Machine Learning génère un environnement Conda avec les dépendances que vous spécifiées, puis effectue l’exécution dans cet environnement au lieu d’utiliser des bibliothèques Python que vous avez installées sur l’image de base.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Pour utiliser une image d’un registre de conteneurs privé qui ne se trouve pas dans votre espace de travail, vous devez utiliser `docker.base_image_registry` pour spécifier l’adresse du dépôt ainsi qu’un nom d’utilisateur et un mot de passe :

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

Vous pouvez aussi utiliser un fichier Docker personnalisé. Utilisez cette approche si vous devez installer des packages non-Python en tant que dépendances. N’oubliez pas de définir l’image de base sur None (Aucune).

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

### <a name="create-or-attach-existing-amlcompute"></a>Créer ou attacher un service AMlCompute existant
Vous devez créer une [cible de calcul](concept-azure-machine-learning-architecture.md#compute-targets) pour la formation de votre modèle. Dans ce tutoriel, vous allez créer un service AmlCompute comme ressource de calcul de formation.

La création d’AmlCompute prend environ cinq minutes. Si un calcul AmlCompute portant ce nom est déjà dans votre espace de travail, ce code ignore le processus de création.

À l’instar d’autres services Azure, certaines ressources associées au service Azure Machine Learning (par exemple, AmlCompute) présentent des limites. Veuillez lire [cet article](how-to-manage-quotas.md) sur les limites par défaut et comme demander un quota plus élevé. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>Créer un ScriptRunConfig
Ce ScriptRunConfig configurera votre tâche pour exécution sur la [cible de calcul](how-to-set-up-training-targets.md) de votre choix.

```python
from azureml.core import ScriptRunConfig

fastai_config = ScriptRunConfig(source_directory='fastai-example', script='train.py')
fastai_config.run_config.environment = fastai_env
fastai_config.run_config.target = compute_target
```

### <a name="submit-your-run"></a>Envoyer votre exécution
Quand une exécution de formation est soumise à l’aide d’un objet ScriptRunConfig, la méthode Submit retourne un objet de type ScriptRun. L’objet ScriptRun retourné vous donne un accès programmatique aux informations sur l’exécution de la formation. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(fastai_config)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning exécute des scripts d’apprentissage en copiant l’intégralité du répertoire source. Si vous avez des données sensibles que vous ne souhaitez pas charger, utilisez un [fichier .ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou ne l’incluez pas dans le répertoire source. À la place, accédez à vos données à l’aide d’un [magasin de données](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true).

Pour plus d’informations sur la personnalisation de votre environnement Python, consultez [Créer et utiliser des environnements logiciels](how-to-use-environments.md). 

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez formé un modèle à l’aide d’une image Docker personnalisée. Consultez ces autres articles pour en savoir plus sur Azure Machine Learning.
* [Suivre les métriques d’exécution ](how-to-track-experiments.md) durant la formation
* [Déployer un modèle](how-to-deploy-custom-docker-image.md) à l’aide d’une image Docker personnalisée.
