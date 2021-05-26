---
title: Images Docker prédéfinies
titleSuffix: Azure Machine Learning
description: Images Docker prédéfinies pour l’inférence (scoring) dans Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 29e117ef7eb763ddcb4bd903ede84a0c11967a67
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382600"
---
# <a name="prebuilt-docker-images-for-inference-preview"></a>Images Docker prédéfinies pour l’inférence (préversion)

Les images conteneur Docker prédéfinies pour l’inférence [(préversion)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) sont utilisées durant le déploiement d’un modèle avec Azure Machine Learning.  Les images sont prédéfinies avec des frameworks de machine learning et des packages Python populaires. Vous pouvez également étendre les packages pour ajouter d’autres packages à l’aide de l’une des méthodes suivantes :

* [Ajoutez des Packages Python](how-to-prebuilt-docker-images-inference-python-extensibility.md).
* [Utilisez le package prédéfini comme base d’un nouveau Dockerfile](how-to-extend-prebuilt-docker-image-inference.md). Avec cette méthode, vous pouvez installer des **packages Python et des packages apt**.

## <a name="why-should-i-use-prebuilt-images"></a>Pourquoi utiliser des images prédéfinies ?

* Cela réduit la latence du déploiement de modèle.
* Cela améliore le taux de réussite du déploiement de modèle.
* Cela évite la génération d’image inutile pendant le déploiement du modèle.
* Cela limite les dépendances et droits d’accès à l’image/au conteneur à ce qui est nécessaire. 
* Le processus d’inférence dans le déploiement s’exécute de façon non-racine.

## <a name="how-can-i-use-prebuilt-images"></a>Comment utiliser des images prédéfinies ?

Reportez-vous à notre exemple de notebook.

## <a name="list-of-prebuilt-docker-images-for-inference"></a>Liste des images Docker prédéfinies pour l’inférence 

### <a name="tensorflow"></a>Tensorflow

Version du framework | UC/GPU | Packages préinstallés | Chemin MCR | Environnement organisé
 --- | --- | --- | --- | --- |
 1.15 | Processeur | pandas==0.25.1 </br> numpy=1.20.1 | `mcr.microsoft.com/azureml/tensorflow-1.15-ubuntu18.04-py37-cpu-inference:latest`  | AzureML-tensorflow-1.15-ubuntu18.04-py37-cpu-inference | 
2.4 | Processeur | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/xgboost-0.9-ubuntu18.04-py37-cpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cpu-inference |
2.4 | GPU | numpy >= 1.16.0 </br> pandas~=1.1.x </br> CUDA==11.0.3 </br> CuDNN==8.0.5.39 | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference |

### <a name="pytorch"></a>Pytorch

Version du framework | UC/GPU | Packages préinstallés | Chemin MCR | Environnement organisé
 --- | --- | --- | --- | --- |
 1.6 | Processeur | numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.6-ubuntu18.04-py37-cpu-inference |
1.7 | Processeur | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/pytorch-1.7-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.7-ubuntu18.04-py37-cpu-inference |

### <a name="scikit-learn"></a>Scikit-Learn

Version du framework | UC/GPU | Packages préinstallés | Chemin MCR | Environnement organisé
 --- | --- | --- | --- | --- |
0.24.1  | Processeur | scikit-learn==0.24.1 </br> numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/sklearn-0.24.1-ubuntu18.04-py37-cpu-inference:latest` | AzureML-sklearn-0.24.1-ubuntu18.04-py37-cpu-inference |

### <a name="onnx-runtime"></a>ONNX Runtime

Version du framework | UC/GPU | Packages préinstallés | Chemin MCR | Environnement organisé
 --- | --- | --- | --- | --- |
1.6 | Processeur | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/onnxruntime-1.6-ubuntu18.04-py37-cpu-inference:latest` |AzureML-onnxruntime-1.6-ubuntu18.04-py37-cpu-inference |

### <a name="xgboost"></a>XGBoost

Version du framework | UC/GPU | Packages préinstallés | Chemin MCR | Environnement organisé
 --- | --- | --- | --- | --- |
0.9 | Processeur | scikit-learn==0.23.2 </br> numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/xgboost-0.9-ubuntu18.04-py37-cpu-inference:latest` | AzureML-xgboost-0.9-ubuntu18.04-py37-cpu-inference | 

### <a name="no-framework"></a>Pas de framework

Version du framework | UC/GPU | Packages préinstallés | Chemin MCR | Environnement organisé
 --- | --- | --- | --- | --- |
N/D | Processeur | N/D | `mcr.microsoft.com/azureml/minimal-ubuntu18.04-py37-cpu-inference:latest` | AzureML-minimal-ubuntu18.04-py37-cpu-inference  |

## <a name="next-steps"></a>Étapes suivantes

* [Ajoutez des packages Python aux images prédéfinies](how-to-prebuilt-docker-images-inference-python-extensibility.md).
* [Utilisez un package prédéfini comme base d’un nouveau Dockerfile](how-to-extend-prebuilt-docker-image-inference.md).