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
ms.date: 4/2/2021
ms.openlocfilehash: 16d5cbc1d48b4559ac34314582b9c01b7c6bb58c
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166646"
---
# <a name="azure-machine-learning-curated-environments"></a>Environnements organisés Azure Machine Learning

Cet article dresse la liste des environnements organisés dans Azure Machine Learning. Les environnements organisés sont fournis par Azure Machine Learning et sont disponibles dans votre espace de travail par défaut. Ils sont sauvegardés par des images Docker mises en cache, qui utilisent la version la plus récente du kit SDK Azure Machine Learning, ce qui réduit le coût de préparation de l’exécution et permet un déploiement plus rapide. Utilisez ces environnements pour prendre rapidement en main les différents infrastructures d’apprentissage automatique.

> [!NOTE]
> Cette liste a été mise à jour en avril 2021. Utilisez l’interface CLI ou le kit SDK Python pour obtenir la liste la plus récente des environnements et leurs dépendances. Pour plus d’informations, consultez l’[article sur les environnements](./how-to-use-environments.md#use-a-curated-environment). Après la publication de ce nouvel ensemble, les environnements organisés précédents sont masqués, mais ils peuvent toujours être utilisés. 

## <a name="pytorch"></a>PyTorch
- AzureML-Pytorch1.7-Cuda11-OpenMpi4.1.0-py36
     - Environnement de Deep Learning avec PyTorch contenant le kit SDK Azure ML et des packages Python supplémentaires.
     - Version de PyTorch : 1.7
     - Version de Python : 3.6.9
     - Image de base : mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - Version de CUDA : 11.0.3
     - OpenMPI : 4.1.0

## <a name="scikit"></a>Scikit
- AzureML-Scikit-learn0.20.4-Cuda11-OpenMpi4.1.0-py36
     - Environnement pour des tâches telles que la régression, le clustering et la classification avec Scikit-learn. Contient le kit SDK Azure ML et des packages Python supplémentaires.
     - Version de Scikit-learn : 20.4
     - Version de Python : 3.6.9
     - Image de base : mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - Version de CUDA : 11.0.3
     - OpenMPI : 4.1.0

## <a name="tensorflow"></a>TensorFlow
- AzureML-TensorFlow2.4-Cuda11-OpenMpi4.1.0-py36
     - Environnement de Deep Learning avec TensorFlow contenant le kit SDK Azure ML et des packages Python supplémentaires.
     - Version de TensorFlow : 2.4
     - Version de Python : 3.6.9
     - Image de base : mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - Version de CUDA : 11.0.3
     - OpenMPI : 4.1.0
