---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 03b40b1f334816f08d7e169f8ff78bdb7c06c4de
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348504"
---
Les entrées du mappage de document `inferenceconfig.json` correspondent aux paramètres de la classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py). Le tableau suivant décrit le mappage entre les entités dans le document JSON et les paramètres de la méthode :

| Entité JSON | Paramètre de méthode | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Chemin d’accès au fichier local contenant le code à exécuter pour l’image. |
| `runtime` | `runtime` | Runtime à utiliser pour l’image. Actuellement, les runtimes pris en charge sont « spark-py » et « python ». |
| `condaFile` | `conda_file` | facultatif. Chemin d’accès au fichier local contenant une définition de l’environnement conda à utiliser pour l’image. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | facultatif. Chemin d’accès au fichier local contenant des étapes Docker supplémentaires à exécuter lors de la configuration de l’image. |
| `sourceDirectory` | `source_directory` | facultatif. Chemin d’accès aux dossiers contenant tous les fichiers pour créer l’image. |
| `enableGpu` | `enable_gpu` | facultatif. Indique s’il faut activer la prise en charge du GPU dans l’image. L’image GPU doit être utilisée sur Microsoft Azure service. Par exemple, Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines, and Azure Kubernetes Service. Valeur par défaut False. |
| `baseImage` | `base_image` | facultatif. Image personnalisée à utiliser comme image de base. Si aucune image de base n'est proposée, l'image de base sera utilisée en fonction d'un paramètre d'exécution donné. |
| `baseImageRegistry` | `base_image_registry` | facultatif. Registre d’images contenant l’image de base. |
| `cudaVersion` | `cuda_version` | facultatif. Version de CUDA à installer pour les images nécessitant une prise en charge GPU. L’image GPU doit être utilisée sur Microsoft Azure Services. Par exemple, Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines, and Azure Kubernetes Service. Les versions prises en charge sont les versions 9.0, 9.1 et 10.0. Si « enable_gpu » est défini, la valeur par défaut est « 9.1 ». |
| `description` | `description` |  Description de cette image. |

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