---
title: Environnements organisés
titleSuffix: Azure Machine Learning
description: En savoir plus sur les environnements organisés Azure Machine Learning, un ensemble d’environnements préconfigurés qui permettent de réduire les temps de préparation du déploiement et des expérimentations.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 07/08/2021
ms.openlocfilehash: cb78928badb067731f6e4fbed75a01346409b70a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451047"
---
# <a name="azure-machine-learning-curated-environments"></a>Environnements organisés Azure Machine Learning

Cet article dresse la liste des environnements organisés dans Azure Machine Learning. Les environnements organisés sont fournis par Azure Machine Learning et sont disponibles dans votre espace de travail par défaut. Ils sont sauvegardés par des images Docker mises en cache, qui utilisent la version la plus récente du kit SDK Azure Machine Learning, ce qui réduit le coût de préparation de l’exécution et permet un déploiement plus rapide. Utilisez ces environnements pour prendre rapidement en main les différents infrastructures d’apprentissage automatique.

> [!NOTE]
> Cette liste a été mise à jour en juillet 2021. Utilisez le [SDK Python](how-to-use-environments.md), la [CLI](/cli/azure/ml/environment?view=azure-cli-latest&preserve-view=true#az_ml_environment_list) ou Azure Machine Learning [Studio](how-to-manage-environments-in-studio.md) pour obtenir la plus récente des environnements et de leurs dépendances. Pour plus d’informations, consultez l’[article sur les environnements](how-to-use-environments.md#use-a-curated-environment). Après la publication de ce nouvel ensemble, les environnements organisés précédents sont masqués, mais ils peuvent toujours être utilisés. 

## <a name="pytorch"></a>PyTorch

**Nom** - AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu  
**Description** - Environnement de Deep Learning avec PyTorch contenant le kit SDK Python AzureML et des packages Python supplémentaires.  
**Configuration Dockerfile** - Le Dockerfile suivant peut être personnalisé en fonction de vos workflows personnels :

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210615.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/pytorch-1.7

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 \
    pip=20.2.4 \
    pytorch=1.7.1 \
    torchvision=0.8.2 \
    torchaudio=0.7.2 \
    cudatoolkit=11.0 \
    nvidia-apex=0.1.0 \
    -c anaconda -c pytorch -c conda-forge

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_PYTORCH=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[pytorch]==0.21.3' \
                'future==0.17.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="lightgbm"></a>LightGBM

**Name** - AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu  
**Description** - Environnement de Machine Learning avec Scikit-learn, LightGBM, XGBoost, Dask contenant le kit SDK Python AzureML et des packages supplémentaires.  
**Configuration Dockerfile** - Le Dockerfile suivant peut être personnalisé en fonction de vos workflows personnels :

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210615.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/lightgbm

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'numpy>=1.10,<1.20' \
                'scipy~=1.5.0' \
                'scikit-learn~=0.24.1' \
                'xgboost~=1.4.0' \
                'lightgbm~=3.2.0' \
                'dask~=2021.6.0' \
                'distributed~=2021.6.0' \
                'dask-ml~=1.9.0' \
                'adlfs~=0.7.0' \
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="sklearn"></a>SKLearn
**Name** - AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu  
**Description** - Environnement pour des tâches telles que la régression, le clustering et la classification avec Scikit-learn. Contient le kit SDK Python AzureML et des packages Python supplémentaires.  
**Configuration Dockerfile** - Le Dockerfile suivant peut être personnalisé en fonction de vos workflows personnels :

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210615.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/sklearn-0.24.1

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0' \
                'scikit-learn==0.24.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="tensorflow"></a>TensorFlow

**Name** - AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu  
**Description** - Environnement de Deep Learning avec Tensorflow contenant le kit SDK Python AzureML et des packages Python supplémentaires.  
**Configuration Dockerfile** - Le Dockerfile suivant peut être personnalisé en fonction de vos workflows personnels :

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210615.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/tensorflow-2.4

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[tensorflow-gpu]==0.21.3'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="automated-ml-automl"></a>ML automatisé (AutoML)

Les workflows de formation du pipeline Azure ML qui utilisent AutoML sélectionnent automatiquement un environnement organisé en fonction du type de calcul et de l’activation de DNN. AutoML fournit les environnements organisés suivants :

| Name | Type de calcul | DNN activé |
| --- | --- | --- |
|AzureML-AutoML | UC | Non |
|AzureML-AutoML-DNN | UC | Oui |
| AzureML-AutoML-GPU | GPU | No |
| AzureML-AutoML-DNN-GPU | GPU | Yes |

Pour plus d’informations sur AutoML et sur les pipelines Azure ML, consultez [Utiliser le ML automatisé dans un pipeline Azure Machine Learning dans Python](how-to-use-automlstep-in-pipelines.md).

## <a name="inference-only-curated-environments-and-prebuilt-docker-images"></a>Environnements organisés d’inférence uniquement et images Docker prédéfinies

En savoir plus sur l’inférence des environnements organisés uniquement et le chemin MCR pour les images docker prégénérés, consultez [images Docker prégénérées pour l’inférence](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference).
