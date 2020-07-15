---
title: Créer des environnements ML réutilisables
titleSuffix: Azure Machine Learning
description: Créez et gérez des environnements pour l’entraînement et le déploiement de modèles. Gérez des packages Python et d’autres paramètres pour l’environnement.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 03/18/2020
ms.custom: tracking-python
ms.openlocfilehash: 7cfcb42093aeb9e323527dea7def7a5b65d2dce2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84558420"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Réutiliser des environnements à des fins d'apprentissage et de déploiement à l'aide d'Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Cet article explique comment créer et gérer des [environnements](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning. Utilisez les environnements pour assurer le suivi et la reproduction des dépendances logicielles de vos projets au fil de leur évolution.

La gestion des dépendances logicielles est une tâche courante pour les développeurs. Vous souhaitez bénéficier de builds reproductibles et limiter les tâches manuelles de configuration logicielle. La classe Azure Machine Learning `Environment` s'applique aux solutions de développement local telles que pip et Conda, et fournit une solution pour le développement cloud local et distribué.

Les exemples présentés de cet article montrent comment :

* Créer un environnement et spécifier des dépendances de package
* Récupérer et mettre à jour des environnements
* Utiliser un environnement à des fins d'apprentissage
* Utiliser un environnement à des fins de déploiement de services web

Pour une vue d'ensemble du fonctionnement des environnements dans Azure Machine Learning, consultez [Présentation des environnements ML](concept-environments.md).

## <a name="prerequisites"></a>Prérequis

* Le [kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Créer un environnement

Les sections suivantes explorent les différentes façons de créer un environnement dédié à vos expériences.

### <a name="use-a-curated-environment"></a>Utiliser un environnement organisé

Pour commencer, vous pouvez sélectionner l'un des environnements organisés : 

* L’environnement _AzureML-Minimal_ contient un ensemble minimal de packages pour permettre le suivi des exécutions et le chargement des ressources. Vous pouvez l’utiliser comme point de départ pour votre propre environnement.

* L'environnement _AzureML-Tutorial_ contient des packages de science des données courants. Ces packages comprennent Scikit-Learn, Pandas, Matplotlib et un ensemble plus important de packages azureml-sdk.

Les environnements organisés s'appuient sur des images Docker mises en cache, ce qui réduit le coût de préparation de l'exécution.

Utilisez la méthode `Environment.get` pour sélectionner l'un des environnements organisés :

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Vous pouvez répertorier les environnements organisés et leurs packages à l'aide du code suivant :

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  N'utilisez pas le préfixe _AzureML_ devant le nom de votre propre environnement. Celui-ci est réservé aux environnements organisés.

### <a name="instantiate-an-environment-object"></a>Instancier un objet d’environnement

Pour créer manuellement un environnement, importez la classe `Environment` à partir du kit de développement logiciel (SDK). Puis utilisez le code suivant pour instancier un objet d'environnement.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Utiliser des fichiers de spécification Conda et pip

Vous pouvez également créer un environnement à partir d’une spécification Conda ou d’un fichier d’exigences pip. Utilisez la méthode [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) ou [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-). Dans l'argument de la méthode, indiquez le nom de votre environnement et le chemin d'accès du fichier souhaité.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Utiliser des environnements Conda existants

Si vous disposez déjà d'un environnement Conda sur votre ordinateur local, vous pouvez utiliser le service pour créer un objet d'environnement. Cette stratégie vous permettra de réutiliser votre environnement interactif local sur des exécutions à distance.

Le code suivant crée un objet d'environnement à partir de l'environnement Conda `mycondaenv`. Il utilise la méthode [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-).

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Créer automatiquement des environnements

Créez automatiquement un environnement en soumettant une exécution d'apprentissage. Soumettez l'exécution à l'aide de la méthode `submit()`. Lorsque vous soumettez une exécution d'apprentissage, la génération du nouvel environnement peut prendre quelques minutes. La durée de génération dépend de la taille des dépendances requises. 

Si vous ne spécifiez aucun environnement dans la configuration de votre exécution avant de soumettre celle-ci, un environnement par défaut est créé pour vous.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

De même, si vous utilisez un objet [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) pour l'apprentissage, vous pouvez directement soumettre l'instance de l'estimateur en tant qu'exécution sans spécifier d'environnement. L'objet `Estimator` encapsule déjà l'environnement et la cible de calcul.

## <a name="add-packages-to-an-environment"></a>Ajouter des packages à un environnement

Ajoutez des packages à un environnement à l'aide de fichiers Conda, pip ou de roues privés. Spécifiez chaque dépendance de package à l'aide de la classe [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py). Ajoutez-la à la section `PythonSection` de l'environnement.

### <a name="conda-and-pip-packages"></a>Packages Conda et pip

Si un package est disponible dans un référentiel de packages Conda, nous vous recommandons d'utiliser l'installation Conda plutôt que l'installation pip. Les packages Conda sont généralement fournis avec des fichiers binaires prédéfinis qui rendent l'installation plus fiable.

L'exemple suivant ajoute Il ajoute la version 1.17.0 de `numpy`. Il ajoute également le package `pillow`, `myenv`. L'exemple utilise respectivement les méthodes [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) et [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-).

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> Si vous utilisez la même définition d’environnement pour une autre exécution, le service Azure Machine Learning réutilise l’image mise en cache de votre environnement. Si vous créez un environnement avec une dépendance de package désépinglée, par exemple ```numpy```, cet environnement continuera à utiliser la version de package installée _au moment de la création de l’environnement_. En outre, tout environnement ultérieur avec une définition correspondante continuera à utiliser l’ancienne version. Pour plus d’informations, consultez [Création, mise en cache et réutilisation d’environnement](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse).

### <a name="private-wheel-files"></a>Fichiers de roues privés

Vous pouvez utiliser des fichiers de roues pip privés en commençant par les charger dans votre espace de travail. Pour les charger, utilisez une méthode [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) statique. Puis capturez l'URL de stockage et transmettez-la à la méthode `add_pip_package()`.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Gérer les environnements

Gérez les environnements pour pouvoir les mettre à jour, les suivre et les réutiliser dans des cibles de calcul et avec d'autres utilisateurs de l'espace de travail.

### <a name="register-environments"></a>Inscrire des environnements

L’environnement est automatiquement inscrit auprès de votre espace de travail quand vous soumettez une exécution ou que vous déployez un service web. Vous pouvez également inscrire manuellement l'environnement à l'aide de la méthode [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-). Cette opération transforme l'environnement en une entité qui est suivie et dont les versions sont gérées dans le cloud. L'entité peut être partagée entre les utilisateurs de l'espace de travail.

Le code suivant inscrit l'environnement `myenv` auprès de l'espace de travail `ws`.

```python
myenv.register(workspace=ws)
```

Lorsque vous utilisez l'environnement pour la première fois dans le cadre d'un apprentissage ou d'un déploiement, celui-ci est inscrit auprès de l'espace de travail. Il est ensuite généré et déployé sur la cible de calcul. Le service procède à la mise en cache des environnements. La réutilisation d’un environnement mis en cache prend beaucoup moins de temps que d’utiliser un nouveau service ou un service qui a été mis à jour.

### <a name="get-existing-environments"></a>Obtenir des environnements existants

La classe `Environment` propose des méthodes qui vous permettent de récupérer des environnements existants dans votre espace de travail. Vous pouvez récupérer des environnements par leur nom, sous forme de liste ou en fonction d'une exécution d'apprentissage spécifique. Ces informations peuvent s'avérer utiles à des fins de dépannage, d'audit et de reproductibilité.

#### <a name="view-a-list-of-environments"></a>Afficher une liste d'environnements

Affichez les environnements de votre espace de travail à l'aide de la classe [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-). Sélectionnez ensuite un environnement à réutiliser.

#### <a name="get-an-environment-by-name"></a>Obtenir un environnement par son nom

Vous pouvez également obtenir un environnement spécifique par son nom et sa version. Le code suivant utilise la méthode [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) pour récupérer la version `1` de l'environnement `myenv` sur l'espace de travail `ws`.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Effectuer l'apprentissage d'un environnement utilisé pour une exécution spécifique

Pour obtenir l'environnement utilisé dans le cadre d'une exécution spécifique après l'apprentissage, utilisez la méthode [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) dans la classe `Run`.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Mettre à jour un environnement existant

Supposons que vous apportiez des modifications à un environnement existant, par exemple en ajoutant un package Python. Une nouvelle version de l'environnement est alors créée lorsque vous soumettez une exécution, déployez un modèle ou inscrivez manuellement l'environnement. La gestion des versions vous permet de visualiser les changements apportés à l'environnement au fil du temps.

Pour mettre à jour la version d'un package Python dans un environnement existant, spécifiez le numéro de version de ce package. Si vous n'utilisez pas le numéro de version exact, Azure Machine Learning réutilisera l'environnement existant avec ses versions d'origine.

### <a name="debug-the-image-build"></a>Déboguer la génération d’image

L'exemple suivant utilise la méthode [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) pour créer manuellement un environnement sous forme d'image Docker. Il surveille les journaux de sortie de la génération de l'image à l'aide de [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). L'image générée apparaît alors dans l'instance Azure Container Registry de l'espace de travail. Ces informations sont utiles pour le débogage.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Activer Docker

 La section [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) de la classe `Environment` d'Azure Machine Learning vous permet de personnaliser et de contrôler en détail le système d'exploitation invité sur lequel vous exécutez votre apprentissage.

Lorsque vous activez Docker, le service génère une image Docker. Il crée également un environnement Python qui utilise vos spécifications au sein de ce conteneur Docker. Cette fonctionnalité fournit un niveau supplémentaire d'isolation et de reproductibilité pour vos exécutions d'apprentissage.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Par défaut, l'image Docker nouvellement créée apparaît dans le registre de conteneurs associé à l'espace de travail.  Le nom du référentiel est au format *azureml/azureml_\<uuid\>* . La partie identificateur unique (*uuid*) du nom correspond à un hachage qui est calculé à partir de la configuration de l'environnement. Cette correspondance permet au service de déterminer si une image de l'environnement donné existe déjà pour être réutilisée.

En outre, le service utilise automatiquement l'une des [images de base](https://github.com/Azure/AzureML-Containers) reposant sur Ubuntu Linux. Il installe les packages Python spécifiés. L'image de base comprend des versions UC et GPU. Azure Machine Learning détecte automatiquement la version à utiliser.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Vous pouvez également spécifier un fichier Dockerfile personnalisé. La méthode la plus simple consiste à commencer à partir d’une des images de base Azure Machine Learning en utilisant la commande Docker ```FROM```, puis à ajouter vos propres étapes personnalisées. Utilisez cette approche si vous devez installer des packages non-Python en tant que dépendances.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>Utiliser des dépendances gérées par l’utilisateur

Dans certains cas, votre image de base personnalisée peut déjà contenir un environnement Python avec les packages que vous voulez utiliser.

Par défaut, le service Azure Machine Learning génère un environnement Conda avec les dépendances que vous spécifiées, puis effectue l’exécution dans cet environnement au lieu d’utiliser des bibliothèques Python que vous avez installées sur l’image de base. 

Pour utiliser vos propres packages installés, définissez le paramètre sur `Environment.python.user_managed_dependencies = True`. Vérifiez que l’image de base contient un interpréteur Python et qu’elle dispose des packages dont votre script d’entraînement a besoin.

Par exemple, pour effectuer l’exécution dans un environnement Miniconda de base sur lequel le package NumPy est installé, commencez par spécifier un fichier Dockerfile avec une étape pour installer le package. Définissez ensuite les dépendances gérées par l’utilisateur sur `True`. 

Vous pouvez également spécifier un chemin vers un interpréteur Python spécifique dans l’image, en définissant la variable `Environment.python.interpreter_path`.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>Utiliser des environnements pour l'apprentissage

Pour soumettre une exécution d'apprentissage, vous devez combiner votre environnement, la [cible de calcul](concept-compute-target.md) et votre script Python d'apprentissage dans la configuration d'une exécution. Cette configuration est un objet wrapper utilisé pour soumettre des exécutions.

Lorsque vous soumettez une exécution d'apprentissage, la génération d'un nouvel environnement peut prendre quelques minutes. La durée dépend de la taille des dépendances requises. Les environnements sont mis en cache par le service. Ainsi, tant que la définition de l'environnement reste inchangée, vous ne subissez l'intégralité du temps d'installation qu'une seule fois.

Voici un exemple d'exécution de script local dans lequel vous utilisez [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) comme votre objet wrapper.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Pour désactiver l'historique ou les captures instantanées des exécutions, utilisez le paramètre situé sous `ScriptRunConfig.run_config.history`.

Si vous ne spécifiez aucun environnement dans la configuration de votre exécution, le service crée un environnement par défaut lorsque vous soumettez votre exécution.

### <a name="use-an-estimator-for-training"></a>Utiliser un estimateur pour l'apprentissage

Si vous utilisez un [estimateur](how-to-train-ml-models.md) pour l'apprentissage, vous pouvez directement soumettre l'instance de l'estimateur. Celle-ci encapsule déjà l'environnement et la cible de calcul.

Le code suivant utilise un estimateur pour une exécution d'apprentissage à nœud unique. L'exécution s'effectue sur un calcul distant pour un modèle `scikit-learn`. Le code part du principe que vous avez précédemment créé un objet de cible de calcul, `compute_target`, et un objet de magasin de données, `ds`.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>Utiliser des environnements pour le déploiement de services web

Vous pouvez utiliser des environnements lorsque vous déployez votre modèle en tant que service web. Cette fonctionnalité permet un flux de travail reproductible et connecté. Dans ce flux de travail, vous pouvez former, tester et déployer votre modèle en utilisant les mêmes bibliothèques dans votre calcul d'apprentissage et dans votre calcul d'inférence.

Pour déployer un service web, combinez l'environnement, le calcul d'inférence, le script de scoring et le modèle inscrit dans votre objet de déploiement, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Pour plus d’informations, consultez [Comment et où déployer des modèles ?](how-to-deploy-and-where.md).

Dans cet exemple, nous partons du principe que vous avez déjà procédé à une exécution d'apprentissage. Vous souhaitez maintenant déployer ce modèle sur Azure Container Instances. Lorsque vous générez le service web, les fichiers de modèle et de scoring sont montés sur l'image, à laquelle la pile d'inférence Azure Machine Learning est ajoutée.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Exemples de notebooks

Cet [exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) approfondit les concepts et méthodes présentés dans cet article.

La section [Déployer un modèle à l’aide d’une image de base Docker personnalisée](how-to-deploy-custom-docker-image.md) montre comment déployer un modèle à l’aide d’une image de base Docker personnalisée.

Cet [exemple de bloc-notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) montre comment déployer un modèle Spark en tant que service web.

## <a name="create-and-manage-environments-with-the-cli"></a>Créer et gérer des environnements avec la CLI

La [CLI Azure Machine Learning](reference-azure-machine-learning-cli.md) reflète la plupart des fonctionnalités du kit de développement logiciel (SDK) Python. Vous pouvez l'utiliser pour créer et gérer des environnements. Les commandes présentées dans cette section illustrent les fonctionnalités de base.

La commande suivante génère la structure des fichiers pour une définition d’environnement par défaut dans le répertoire spécifié. Il s'agit de fichiers JSON. Ceux-ci fonctionnent comme la classe correspondante dans le kit de développement logiciel (SDK). Vous pouvez utiliser les fichiers pour créer des environnements dotés de paramètres personnalisés. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Exécutez la commande suivante pour inscrire un environnement à partir d’un répertoire spécifié.

```azurecli-interactive
az ml environment register -d myenvdir
```

Exécutez la commande suivante pour dresser la liste de tous les environnements inscrits.

```azurecli-interactive
az ml environment list
```

Téléchargez un environnement inscrit à l'aide de la commande suivante.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser une cible de calcul gérée pour effectuer l'apprentissage d'un modèle, consultez [Tutoriel : Effectuer l'apprentissage d'un modèle](tutorial-train-models-with-aml.md).
* Au terme de l'apprentissage du modèle, découvrez [comment et où déployer des modèles](how-to-deploy-and-where.md).
* Consultez les informations de référence sur le SDK de la classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Pour plus d'informations sur les concepts et méthodes décrits dans cet article, consultez l'[exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
