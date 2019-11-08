---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: ab31d45808a8c77c53b895643eec63952201d9e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799818"
---
Les entrées du mappage de document `inferenceconfig.json` correspondent aux paramètres de la classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py). Le tableau suivant décrit le mappage entre les entités dans le document JSON et les paramètres de la méthode :

| Entité JSON | Paramètre de méthode | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Chemin vers un fichier local contenant le code à exécuter pour l’image. |
| `runtime` | `runtime` | Runtime à utiliser pour l’image. Actuellement, les runtimes pris en charge sont `spark-py` et `python`. |
| `condaFile` | `conda_file` | facultatif. Chemin vers un fichier local contenant une définition d’environnement Conda à utiliser pour l’image. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | facultatif. Chemin vers un fichier local contenant des étapes Docker supplémentaires à exécuter lors de la configuration de l’image. |
| `sourceDirectory` | `source_directory` | facultatif. Chemin vers des dossiers contenant tous les fichiers pour créer l’image. |
| `enableGpu` | `enable_gpu` | facultatif. Indique s’il faut activer la prise en charge du GPU dans l’image. L’image GPU doit être utilisée sur un service Azure comme Azure Container Instances, Capacité de calcul Azure Machine Learning, Machines virtuelles Azure ou Azure Kubernetes Service. La valeur par défaut est False. |
| `baseImage` | `base_image` | facultatif. Image personnalisée à utiliser comme image de base. Si aucune image de base n’est fournie, l’image sera basée sur le paramètre de runtime fourni. |
| `baseImageRegistry` | `base_image_registry` | facultatif. Registre d’images contenant l’image de base. |
| `cudaVersion` | `cuda_version` | facultatif. Version de CUDA à installer pour les images nécessitant une prise en charge GPU. L’image GPU doit être utilisée sur un service Azure comme Azure Container Instances, Capacité de calcul Azure Machine Learning, Machines virtuelles Azure ou Azure Kubernetes Service. Les versions prises en charge sont les versions 9.0, 9.1 et 10.0. Si `enable_gpu` est défini, la valeur par défaut est 9.1. |
| `description` | `description` | Description de l’image. |

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