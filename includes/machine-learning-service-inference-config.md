---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926990"
---
Les entrées du mappage de document `inferenceconfig.json` correspondent aux paramètres de la classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py). Le tableau suivant décrit le mappage entre les entités dans le document JSON et les paramètres de la méthode :

| Entité JSON | Paramètre de méthode | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Chemin vers un fichier local contenant le code à exécuter pour l’image. |
| `runtime` | `runtime` | facultatif. Runtime à utiliser pour l’image. Actuellement, les runtimes pris en charge sont `spark-py` et `python`. Si `environment` est défini, cette valeur est ignorée. |
| `condaFile` | `conda_file` | facultatif. Chemin vers un fichier local contenant une définition d’environnement Conda à utiliser pour l’image.  Si `environment` est défini, cette valeur est ignorée. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | facultatif. Chemin vers un fichier local contenant des étapes Docker supplémentaires à exécuter lors de la configuration de l’image.  Si `environment` est défini, cette valeur est ignorée.|
| `sourceDirectory` | `source_directory` | facultatif. Chemin vers des dossiers contenant tous les fichiers pour créer l’image. |
| `enableGpu` | `enable_gpu` | facultatif. Indique s’il faut activer la prise en charge du GPU dans l’image. L’image GPU doit être utilisée sur un service Azure comme Azure Container Instances, Capacité de calcul Azure Machine Learning, Machines virtuelles Azure ou Azure Kubernetes Service. La valeur par défaut est False. Si `environment` est défini, cette valeur est ignorée.|
| `baseImage` | `base_image` | facultatif. Image personnalisée à utiliser comme image de base. Si aucune image de base n’est fournie, l’image sera basée sur le paramètre de runtime fourni. Si `environment` est défini, cette valeur est ignorée. |
| `baseImageRegistry` | `base_image_registry` | facultatif. Registre d’images contenant l’image de base. Si `environment` est défini, cette valeur est ignorée.|
| `cudaVersion` | `cuda_version` | facultatif. Version de CUDA à installer pour les images nécessitant une prise en charge GPU. L’image GPU doit être utilisée sur un service Azure comme Azure Container Instances, Capacité de calcul Azure Machine Learning, Machines virtuelles Azure ou Azure Kubernetes Service. Les versions prises en charge sont les versions 9.0, 9.1 et 10.0. Si `enable_gpu` est défini, la valeur par défaut est 9.1. Si `environment` est défini, cette valeur est ignorée. |
| `description` | `description` | Description de l’image. Si `environment` est défini, cette valeur est ignorée.  |
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

Le code JSON suivant est un exemple de configuration d’inférence qui utilise un [environnement](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning existant avec une version spécifique à utiliser avec l’interface CLI :

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

Le code JSON suivant est un exemple de configuration d’inférence qui utilise un [environnement](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning existant avec la dernière version à utiliser avec l’interface CLI :

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
