---
title: Fichier include
description: Fichier include
services: machine-learning
author: shivanissambare
ms.service: machine-learning
ms.author: ssambare
ms.custom: include file
ms.topic: include
ms.date: 10/07/2021
ms.openlocfilehash: 32b18e9c98fe166d127e9fcad5f59794e8080751
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712078"
---
* Toutes les images Docker s’exécutent en tant qu’utilisateur non racine.
* Nous vous recommandons d’utiliser la balise `latest` pour les images docker. Les images docker prédéfinies pour l’inférence sont publiées dans le registre de conteneur Microsoft (MCR). Pour interroger la liste des balises disponibles, suivez les [instructions sur leur référentiel GitHub](https://github.com/microsoft/ContainerRegistry#browsing-mcr-content).

### <a name="tensorflow"></a>TensorFlow

Version du framework | UC/GPU | Packages préinstallés | Chemin MCR | Environnement organisé
 --- | --- | --- | --- | --- |
 1.15 | Processeur | pandas==0.25.1 </br> numpy=1.20.1 | `mcr.microsoft.com/azureml/tensorflow-1.15-ubuntu18.04-py37-cpu-inference:latest`  | AzureML-tensorflow-1.15-ubuntu18.04-py37-cpu-inference | 
2.4 | Processeur | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cpu-inference |
2.4 | GPU | numpy >= 1.16.0 </br> pandas~=1.1.x </br> CUDA==11.0.3 </br> CuDNN==8.0.5.39 | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference |

### <a name="pytorch"></a>PyTorch

Version du framework | UC/GPU | Packages préinstallés | Chemin MCR | Environnement organisé
 --- | --- | --- | --- | --- |
 1.6 | Processeur | numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.6-ubuntu18.04-py37-cpu-inference |
1.7 | Processeur | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/pytorch-1.7-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.7-ubuntu18.04-py37-cpu-inference |

### <a name="scikit-learn"></a>SciKit-Learn

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