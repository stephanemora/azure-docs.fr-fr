---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845149"
---
Les entrées du mappage de document `inferenceconfig.json` correspondent aux paramètres de la classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py). Le tableau suivant décrit le mappage entre les entités dans le document JSON et les paramètres de la méthode :

| Entité JSON | Paramètre de méthode | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Chemin vers un fichier local contenant le code à exécuter pour l’image. |
| `runtime` | `runtime` | facultatif. Runtime à utiliser pour l’image. Les runtimes pris en charge sont `spark-py` et `python`. Si `environment` est défini, cette entrée est ignorée. |
| `condaFile` | `conda_file` | facultatif. Chemin vers un fichier local contenant une définition d’environnement Conda à utiliser pour l’image.  Si `environment` est défini, cette entrée est ignorée. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | facultatif. Chemin vers un fichier local contenant des étapes Docker supplémentaires à exécuter lors de la configuration de l’image.  Si `environment` est défini, cette entrée est ignorée.|
| `sourceDirectory` | `source_directory` | facultatif. Chemin vers des dossiers contenant tous les fichiers pour créer l’image, ce qui facilite l’accès à tous les fichiers de ce dossier ou sous-dossier. Vous pouvez charger un dossier entier à partir de votre ordinateur local en tant que dépendances pour le service web. Remarque : vos chemins d'accès entry_script, conda_file et extra_docker_file_steps sont des chemins d'accès relatifs au chemin source_directory. |
| `enableGpu` | `enable_gpu` | facultatif. Indique s’il faut activer la prise en charge du GPU dans l’image. L’image GPU doit être utilisée sur un service Azure, comme Azure Container Instances. Par exemple, Capacité de calcul Azure Machine Learning, Machines virtuelles Azure et Azure Kubernetes Service. La valeur par défaut est False. Si `environment` est défini, cette entrée est ignorée.|
| `baseImage` | `base_image` | facultatif. Image personnalisée à utiliser comme image de base. Si aucune image de base n’est fournie, l’image sera basée sur le paramètre de runtime fourni. Si `environment` est défini, cette entrée est ignorée. |
| `baseImageRegistry` | `base_image_registry` | facultatif. Registre d’images contenant l’image de base. Si `environment` est défini, cette entrée est ignorée.|
| `cudaVersion` | `cuda_version` | facultatif. Version de CUDA à installer pour les images nécessitant une prise en charge GPU. L’image GPU doit être utilisée sur un service Azure. Par exemple, Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines, and Azure Kubernetes Service. Les versions prises en charge sont les versions 9.0, 9.1 et 10.0. Si `enable_gpu` est défini, la valeur par défaut est 9.1. Si `environment` est défini, cette entrée est ignorée. |
| `description` | `description` | Description de l’image. Si `environment` est défini, cette entrée est ignorée.  |
| `environment` | `environment` | facultatif.  [Environnement](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning.|

Le code JSON suivant est un exemple de configuration d’inférence à utiliser avec l’interface CLI :

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

Vous pouvez inclure les spécifications complètes d’un [environnement](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning dans le fichier de configuration d’inférence. Si cet environnement n’existe pas dans votre espace de travail, Azure Machine Learning le crée. Dans le cas contraire, Azure Machine Learning met à jour l’environnement, si nécessaire. L’extrait JSON ci-dessous est un exemple :

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Vous pouvez également utiliser un environnement Azure Machine Learning [existant](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) dans des paramètres CLI distincts et supprimer la clé d'environnement du fichier de configuration d’inférence. Utilisez -e pour le nom de l’environnement, et --ev pour la version de l’environnement. À défaut d'indiquer --ev, la version la plus récente est utilisée. L'exemple suivant illustre un fichier de configuration d'inférence :

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

La commande suivante montre comment déployer un modèle à l’aide du fichier de configuration d’inférence précédent (nommé myInferenceConfig.json). 

Elle utilise également la version la plus récente d’un environnement Azure Machine Learning [existant](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (nommé AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
