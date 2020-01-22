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
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 93a70bf0d9189368135b8007e95627fc64064c51
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932183"
---
# <a name="reuse-environments-for-training--deployment-with-azure-machine-learning"></a>Réutilisez des environnements pour l’entraînement et le déploiement avec Azure Machine Learning.
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, découvrez comment créer et gérer des [environnements](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning afin de pouvoir suivre et reproduire les dépendances logicielles de vos projets à mesure qu’elles évoluent.

La gestion des dépendances logicielles est une tâche courante pour les développeurs. Vous souhaitez être en mesure de garantir que les builds sont reproductibles sans beaucoup de configuration logicielle manuelle. Avec des solutions pour le développement local, comme pip et Conda par exemple, la classe Environments d’Azure Machine Learning fournit une solution pour le développement cloud local et distribué.

Les exemples présentés de cet article montrent comment :

* Créer un environnement et spécifier des dépendances de package
* Récupérer et mettre à jour des environnements
* Utiliser l’environnement pour l’entraînement
* Utiliser l’environnement pour le déploiement de services web

Pour une vue d’ensemble du fonctionnement des environnements dans Azure Machine Learning, consultez l’[article conceptuel](concept-environments.md).

## <a name="prerequisites"></a>Conditions préalables requises

* SDK Azure Machine Learning pour Python [installé](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-an-environment"></a>Créer un environnement

Il existe plusieurs façons de créer un environnement pour vos expériences.

### <a name="use-curated-environment"></a>Utiliser un environnement organisé

Vous pouvez sélectionner l’un des environnements organisés avec lequel commencer. 

* L’environnement __AzureML-Minimal__ contient un ensemble minimal de packages pour permettre le suivi des exécutions et le chargement des ressources. Vous pouvez l’utiliser comme point de départ pour votre propre environnement.

* L’environnement __AzureML-Tutorial__ contient des packages de science des données courants, tels que Scikit-Learn, Pandas et Matplotlib, et un ensemble plus large de packages azureml-sdk.

Les environnements organisés s’appuient sur des images Docker mises en cache, ce qui réduit le coût de préparation de l’exécution.

Utilisez la méthode __Environment.get__ pour sélectionner l’un des environnements organisés :

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Vous pouvez répertorier les environnements organisés et leurs packages à l’aide du code suivant :
```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  N’utilisez pas le préfixe _AzureML_ devant le nom de votre propre environnement. Il est réservé aux environnements organisés.

### <a name="instantiate-an-environment-object"></a>Instancier un objet d’environnement

Pour créer manuellement un environnement, importez la classe Environment à partir du Kit de développement logiciel (SDK) et instanciez un objet environnement avec le code suivant.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Fichiers de spécifications Conda et pip

Vous pouvez également créer un environnement à partir d’une spécification Conda ou d’un fichier d’exigences pip.
Utilisez la méthode [from_conda_specification()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) ou [from_pip_requirements()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-), et incluez le nom de votre environnement et le chemin du fichier souhaité dans l’argument de la méthode.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>Environnement Conda existant

Si vous disposez d’un environnement Conda existant sur votre ordinateur local, le service offre une solution pour créer un objet d’environnement à partir de celui-ci. De cette façon, vous pouvez réutiliser votre environnement interactif local sur des exécutions à distance.

L’exemple suivant crée un objet environnement à partir de l’environnement Conda `mycondaenv` existant avec la méthode [from_existing_conda_environment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-).

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>Créer automatiquement des environnements

Créez automatiquement un environnement en soumettant une exécution d’entraînement avec la méthode submit(). Quand vous soumettez une exécution d’entraînement, la génération d’un nouvel environnement peut prendre quelques minutes en fonction de la taille des dépendances nécessaires. 

Si vous ne spécifiez pas d’environnement dans votre configuration d’exécution avant de soumettre l’exécution, un environnement par défaut est automatiquement créé.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

De même, si vous utilisez un objet [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) pour l’apprentissage, vous pouvez soumettre l’instance de l’estimateur directement en tant qu’exécution sans devoir spécifier un environnement. L’objet `Estimator` encapsule déjà l’environnement et la cible de calcul.


## <a name="add-packages-to-an-environment"></a>Ajouter des packages à un environnement

Ajoutez des packages à un environnement avec des fichiers Conda, pip ou de roue privés. Spécifiez chaque dépendance de package à l’aide de la [classe CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py), puis ajoutez-la à la classe PythonSection de l’environnement.

### <a name="conda-and-pip-packages"></a>Packages Conda et pip

Si un package est disponible dans un référentiel de packages Conda, il est recommandé d’utiliser Conda sur l’installation pip. En effet, les packages Conda sont généralement fournis avec des fichiers binaires prédéfinis qui rendent l’installation plus fiable.

L’exemple suivant ajoute `scikit-learn`, en particulier la version 0.21.3, et le package `pillow` à l’environnement, `myenv`, avec les méthodes [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) et [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-), respectivement.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Fichiers de roues privés

Vous pouvez utiliser un fichier de roue pip privé en le chargeant tout d’abord dans le stockage de votre espace de travail à l’aide d’une méthode [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) statique, puis en capturant l’URL de stockage et en la passant à la méthode `add_pip_package()`.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Gérer les environnements

Gérez les environnements pour pouvoir les mettre à jour, les suivre et les réutiliser dans des cibles de calcul et avec d’autres utilisateurs de l’espace de travail.

### <a name="register-environments"></a>Inscrire des environnements

L’environnement est automatiquement inscrit auprès de votre espace de travail quand vous soumettez une exécution ou que vous déployez un service web. Vous pouvez également inscrire manuellement l’environnement à l’aide de la méthode [register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-). Cette opération transforme l’environnement en une entité qui est suivie, dont les versions sont gérées dans le cloud et qui peut être partagée entre les utilisateurs de l’espace de travail.

Le code suivant inscrit l’environnement `myenv` auprès de l’espace de travail `ws`.

```python
myenv.register(workspace=ws)
```

Quand il est utilisé pour la première fois, lors de l’entraînement ou du déploiement, l’environnement est inscrit auprès de l’espace de travail, généré et déployé sur la cible de calcul. Les environnements sont mis en cache par le service. La réutilisation d’un environnement mis en cache prend beaucoup moins de temps que d’utiliser un nouveau service ou un service qui a été mis à jour.

### <a name="get-existing-environments"></a>Obtenir des environnements existants

La classe Environment offre des méthodes qui vous permettent de récupérer des environnements existants dans votre espace de travail par nom, comme une liste, ou par une exécution d’entraînement spécifique à des fins de dépannage, d’audit ou de reproductibilité.

#### <a name="view-list-of-environments"></a>Afficher la liste des environnements

Affichez les environnements de votre espace de travail avec [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-), puis sélectionnez-en un à réutiliser.

#### <a name="get-environment-by-name"></a>Obtenir un environnement par nom

Vous pouvez également obtenir un environnement spécifique par son nom et sa version.
Le code suivant utilise la méthode [get()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) pour récupérer la version `1` de l’environnement, `myenv`, sur l’espace de travail `ws`.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Environnement spécifique d’exécution d’entraînement

Pour obtenir l’environnement utilisé pour une exécution spécifique une fois l’entraînement terminé, utilisez la méthode [get_environment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) dans la classe Run.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Mettre à jour un environnement existant

Si vous apportez des changements à un environnement existant, comme l’ajout d’un package Python, une nouvelle version de l’environnement est créée lors de la soumission d’exécution, du déploiement du modèle ou de l’inscription manuelle de l’environnement. La gestion des versions vous permet d’afficher les changements apportés à l’environnement dans le temps.

Pour mettre à jour une version de package Python d’un environnement existant, spécifiez le numéro de version exact de ce package. Sinon, Azure Machine Learning réutilise l’environnement existant avec les versions de package en cours lors de la création de l’environnement.

### <a name="debug-the-image-build"></a>Déboguer la génération d’image

Cet exemple utilise la méthode [build()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) pour créer manuellement un environnement comme image Docker et supervise les journaux de sortie à partir de la création d’image à l’aide de [wait_for_completion()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). L’image générée apparaît alors sous l’espace de travail Azure Container Registry, ce qui est utile pour le débogage.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker et environnements

 La classe [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) de la classe `Environments` d’Azure Machine Learning vous permet de personnaliser et de contrôler en détail le système d’exploitation invité dans lequel l’exécution d’entraînement s’exécute.

Quand vous activez (`enable`) Docker, le service génère une image Docker et crée un environnement Python avec vos spécifications dans ce conteneur Docker. Cela fournit un niveau d’isolation supplémentaire et la reproductibilité pour vos exécutions d’entraînement.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Une fois générée, l’image Docker s’affiche par défaut dans le service Azure Container Registry qui est associé à l’espace de travail.  Le nom du référentiel se présente sous la forme *azureml/azureml_\<uuid\>* . La partie identificateur unique (*uuid*) correspond à un hachage calculé à partir de la configuration de l’environnement. Cela permet au service de déterminer si une image correspondant à l’environnement donné existe déjà pour être réutilisée.

En outre, le service utilise automatiquement l’une des [images de base](https://github.com/Azure/AzureML-Containers) basées sur Ubuntu Linux et installe les packages Python spécifiés. L’image de base comprend des versions UC et GPU. Azure Machine Learning détecte automatiquement la version à utiliser.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

> [!NOTE]
> Si vous spécifiez `environment.python.user_managed_dependencies=False` lors de l’utilisation d’une image Docker personnalisée, le service génère un environnement Conda dans l’image et effectue l’exécution dans cet environnement, au lieu d’utiliser les bibliothèques Python que vous avez peut-être installées sur l’image de base. Définissez le paramètre sur `True` pour utiliser vos propres packages installés.

## <a name="using-environments-for-training"></a>Utiliser des environnements pour l’entraînement

Pour soumettre une exécution d’entraînement, vous devez combiner votre environnement, la [cible de calcul](concept-compute-target.md) et le script Python d’entraînement dans une configuration d’exécution, qui est un objet wrapper utilisé pour soumettre des exécutions.

Quand vous soumettez une exécution d’entraînement, la génération d’un nouvel environnement peut prendre quelques minutes en fonction de la taille des dépendances nécessaires. Les environnements sont mis en cache par le service. Par conséquent, tant que la définition de l’environnement reste inchangée, le temps de configuration total n’est supporté qu’une seule fois.

Voici un exemple d’exécution de script local dans lequel vous utilisez [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) comme votre objet wrapper.

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
> Pour désactiver l’historique des exécutions ou les captures instantanées d’exécution, utilisez le paramètre se trouvant sous `ScriptRunConfig.run_config.history`.

Si vous ne spécifiez pas l’environnement dans votre configuration d’exécution, le service crée un environnement par défaut quand vous soumettez votre exécution.

### <a name="train-with-an-estimator"></a>Entraîner avec un estimateur

Si vous utilisez un [estimateur](how-to-train-ml-models.md) pour l’entraînement, vous pouvez simplement soumettre l’instance de l’estimateur directement, car elle encapsule déjà l’environnement et la cible de calcul.

Le code suivant utilise un estimateur pour une exécution d’apprentissage à nœud unique sur une capacité de calcul distante pour un modèle scikit-learn. Il suppose l’existence d’un objet cible de calcul créé précédemment, `compute_target`, et d’un objet magasin de données, `ds`.

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

## <a name="using-environments-for-web-service-deployment"></a>Utilisation d’environnements pour le déploiement de services web

Vous pouvez utiliser des environnements lors du déploiement de votre modèle en tant que service web. Cela permet d’exploiter un workflow connecté et reproductible dans lequel vous pouvez entraîner, tester et déployer votre modèle en utilisant exactement les mêmes bibliothèques à la fois dans vos calculs d’entraînement et d’inférence.

Pour déployer un service web, combinez l’environnement, le calcul d’inférence, le script de scoring et le modèle inscrit dans votre objet de déploiement, [deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Apprenez-en davantage sur le [déploiement de services web](how-to-deploy-and-where.md).

Dans cet exemple, supposons que vous avez effectué une exécution d’entraînement et que vous voulez déployer ce modèle sur une instance de conteneur Azure. Lors de la génération du service web, les fichiers de modèle et de scoring sont montés sur l’image, à laquelle la pile d’inférence Azure Machine Learning est ajoutée.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Exemples de notebooks

Cet [exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) approfondit les concepts et méthodes présentés dans cet article.

## <a name="create-and-manage-environments-with-the-cli"></a>Créer et gérer des environnements avec la CLI

La [CLI Azure Machine Learning](reference-azure-machine-learning-cli.md) reflète la majorité des fonctionnalités du Kit de développement logiciel (SDK) Python et peut être utilisée pour la création et la gestion d’environnement. Les commandes suivantes illustrent les fonctionnalités de base.

La commande suivante génère la structure des fichiers pour une définition d’environnement par défaut dans le répertoire spécifié. Ces fichiers sont des fichiers JSON de fonction similaire à celle de la classe correspondante dans le Kit de développement logiciel (SDK), et peuvent être utilisés pour créer de nouveaux environnements avec des paramètres personnalisés. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Exécutez la commande suivante pour inscrire un environnement à partir d’un répertoire spécifié.

```azurecli-interactive
az ml environment register -d myenvdir
```

L’exécution de la commande suivante répertorie tous les environnements inscrits.

```azurecli-interactive
az ml environment list
```

Téléchargez un environnement inscrit avec la commande suivante.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Former un modèle](tutorial-train-models-with-aml.md) utilise une cible de calcul gérée pour former un modèle.
* Une fois le modèle formé, découvrez [comment et où déployer les modèles](how-to-deploy-and-where.md).
* Consultez les informations de référence sur le SDK de la [classe Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
