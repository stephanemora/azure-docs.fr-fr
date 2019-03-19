---
title: Frameworks d’apprentissage profond et d’intelligence artificielle - Azure | Microsoft Docs
description: Apprenez-en davantage sur les infrastructures et les outils Deep Learning pris en charge par la Data Science Virtual Machine.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 59f88d54d3542738f1a500c8c476995eb1535ecf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006500"
---
# <a name="deep-learning-and-ai-frameworks"></a>Frameworks d’apprentissage profond et d’intelligence artificielle
La machine virtuelle [DSVM](https://aka.ms/dsvm) (Data Science Virtual Machine) et la machine virtuelle [DLVM](https://aka.ms/dsvm/deeplearning) (Deep Learning Virtual Machine) prennent en charge plusieurs frameworks d’apprentissage profond pour créer des applications d’intelligence artificielle (IA) avec des fonctionnalités cognitives et d’analytique prédictive comme la compréhension d’image et de langue.

Voici les détails de tous les frameworks d’apprentissage profond disponibles sur la machine virtuelle DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Microsoft Cognitive Toolkit (CNTK) est installé en Python 3.5 sur [Linux et Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), et en Python 3.6 sur [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter sont inclus.     |
| Outils connexes sur la machine virtuelle DSVM      | Keras      |
| Comment l’utiliser/l’exécuter ?    | * Sur un terminal : activez l’environnement approprié, puis exécutez le code Python. <br/> * Dans Jupyter : Connectez-vous à [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), puis ouvrez le répertoire CNTK pour obtenir des exemples. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | TensorFlow est installé en Python 3.5 sur [Linux et Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), et en Python 3.6 sur [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter sont inclus.     |
| Outils connexes sur la machine virtuelle DSVM      | Keras      |
| Comment l’utiliser/l’exécuter ?    | * Sur un terminal : activez l’environnement approprié, puis exécutez le code Python. <br/> * Dans Jupyter : Connectez-vous à [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), puis ouvrez le répertoire TensorFlow pour obtenir des exemples.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond distribué pour TensorFlow      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Horovod est installé en Python 3.5 sur [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Liens vers des exemples      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Outils connexes sur la machine virtuelle DSVM      | TensorFlow      |
| Comment l’utiliser/l’exécuter ?    | Sur un terminal : activez l’environnement approprié, puis exécutez le code Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | API d’apprentissage profond de haut niveau      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | TensorFlow est installé en Python 3.5 sur [Linux et Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), et en Python 3.6 sur [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Liens vers des exemples      | https://github.com/fchollet/keras/tree/master/examples      |
| Outils connexes sur la machine virtuelle DSVM      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Comment l’utiliser/l’exécuter ?    | * Sur un terminal : activez l’environnement approprié, puis exécutez le code Python. <br/> * Dans Jupyter : Téléchargez les exemples à partir de l’emplacement GitHub, connectez-vous à [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), puis ouvrez le répertoire d’exemples. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Caffe est installé dans `/opt/caffe`.    |
| Comment basculer vers Python 2.7 ? | Exécutez `source activate root` |
| Liens vers des exemples      | Des exemples sont inclus dans `/opt/caffe/examples`.      |
| Outils connexes sur la machine virtuelle DSVM      | Caffe2      |

### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?

Utilisez X2Go pour vous connecter à votre machine virtuelle, puis démarrez un nouveau terminal et entrez

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Une nouvelle fenêtre de navigateur s’ouvre avec les exemples de bloc-notes.

Les fichiers binaires sont installés dans /opt/caffe/build/install/bin.

La version installée de Caffe nécessite Python 2.7 et ne fonctionnera avec 3.5 Python activé par défaut. Exécutez `source activate root` pour basculer vers l’environnement Anaconda.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Caffe2 est installé dans [l’environnement conda Python 2.7 (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). La source se trouve ici : `/opt/caffe2`. |
| Liens vers des exemples      | Des exemples de bloc-notes sont inclus dans JupyterHub. |
| Outils connexes sur la machine virtuelle DSVM      | Caffe      |
| Comment l’utiliser/l’exécuter ?    | * Au terminal : activez [l’environnement Python root](dsvm-languages.md#python-linux-and-windows-server-2012-edition), démarrez Python, puis importez caffe2. <br/> * Dans JupyterHub : [connectez-vous à JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), puis accédez au répertoire Caffe2 pour rechercher des exemples de bloc-notes. Certains bloc-notes exigent que la racine Caffe2 soit définie dans le code Python. Entrez /opt/caffe2. |
| Notes de build | Caffe2 est construit depuis sa source sur Linux et comprend CUDA, cuDNN et Intel MKL. La validation actuelle est 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, qui a été choisie pour sa stabilité sur tous les processeurs graphiques et échantillons testés. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Le Chainer est installé en [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL et ChainerCV sont également installés.   |
| Liens vers des exemples      | Des exemples de bloc-notes sont inclus dans JupyterHub. |
| Outils connexes sur la machine virtuelle DSVM      | Caffe      |
| Comment l’utiliser/l’exécuter ?  | * Sur un terminal : activez l’environnement [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), exécutez le code _python_, puis importez le Chainer. <br/> * Dans JupyterHub : [connectez-vous à JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), puis accédez au répertoire Chainer pour rechercher des exemples de bloc-notes.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond pour H2O      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Deep Water est installé en [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) et est également disponible dans `/dsvm/tools/deep_water`.   |
| Liens vers des exemples      | Des exemples de bloc-notes sont inclus dans JupyterHub.      |
| Outils connexes sur la machine virtuelle DSVM      | H2O, Sparkling Water      |

### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?

Deep Water nécessite CUDA 8 avec cuDNN 5.1. Ceux-ci ne se trouvent pas dans le chemin de la bibliothèque par défaut, car les autres frameworks d’apprentissage profond utilisent CUDA 9 et cuDNN 7. Pour utiliser CUDA 8 + cuDNN 5.1 avec Deep Water :

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Pour utiliser Deep Water :
* Sur un terminal : activez l’environnement [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), puis exécutez le code _python_. <br/>
* Dans JupyterHub : [connectez-vous à JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), puis accédez au répertoire deep_water pour rechercher des exemples de bloc-notes.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | MXNet est installé dans `C:\dsvm\tools\mxnet` sur Windows et `/dsvm/tools/mxnet` sur Linux. Les liaisons Python sont installées en Python 3.5 sur [Linux et Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), et en Python 3.6 sur [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Les liaisons R sont également installées sur Ubuntu.   |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter sont inclus.    |
| Outils connexes sur la machine virtuelle DSVM      | Keras      |
| Comment l’utiliser/l’exécuter ?    | * Sur un terminal : activez l’environnement approprié, puis exécutez le code Python. <br/> * Dans Jupyter : Connectez-vous à [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), puis ouvrez le répertoire mxnet pour obtenir des exemples.  |
 | Notes de build | MXNet est construit depuis sa source sur Linux. Cette build comprend CUDA, cuDNN, NCCL et MKL. |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Système d’apprentissage profond de NVIDIA permettant une formation rapide des modèles d’apprentissage profond.      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | DIGITS est installé dans `/dsvm/tools/DIGITS` et disponible en tant que service appelé _digits_.   |

### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?

Connectez-vous à la machine virtuelle avec X2Go. Sur un terminal, démarrez le service :

    sudo systemctl start digits

Le démarrage du service prend environ une minute. Démarrez un navigateur web et accédez à `http://localhost:5000`. Notez que DIGITS ne fournit pas de connexion sécurisée et ne doit pas être exposé en dehors de la machine virtuelle.



## <a name="nvidia-smi"></a>nvidia-smi

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil NVIDIA pour interroger l’activité du GPU      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | _nvidia-smi_ est disponible sur le chemin système.   |
| Comment l’utiliser/l’exécuter ? | Démarrez une invite de commandes (sur Windows) ou un terminal (sur Linux), puis exécutez _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Theano est installé dans les environnements Python 2.7 (_root_) et Python 3.5 (_py35_).   |
| Outils connexes sur la machine virtuelle DSVM      | Keras      |
| Comment l’utiliser/l’exécuter ?    | * Sur un terminal, activez la version de Python souhaitée (root ou py35), exécutez python, puis importez Theano. <br/> * Dans Jupyter, sélectionnez le noyau Python 2.7 ou 3.5, puis importez Theano.  <br/>Pour contourner un bogue MKL récent, vous devez d’abord définir la couche de threading MKL :<br/><br/>_export MKL_THREADING_LAYER=GNU_|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Torch est installé dans `/dsvm/tools/torch`. PyTorch est installé dans les environnements Python 2.7 (_root_) et Python 3.5 (_py35_).   |
| Liens vers des exemples      | Des exemples Torch se trouvent dans `/dsvm/samples/torch`. Des exemples PyTorch se trouvent dans `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | PyTorch est installé en [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter sont inclus, et vous trouverez aussi des exemples dans /dsvm/samples/pytorch.      |
| Outils connexes sur la machine virtuelle DSVM      | Torch      |
| Comment l’utiliser/l’exécuter ? |* Sur un terminal : activez l’environnement approprié, puis exécutez le code Python. <br/> * Dans Jupyter : Connectez-vous à [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), puis ouvrez le répertoire PyTorch pour obtenir des exemples.  |

## <a name="mxnet-model-server"></a>MXNet Model Server

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Un serveur pour créer des points de terminaison HTTP pour les modèles MXNet et ONNX.      |
| Éditions DSVM prises en charge      | Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | _mxnet-model-server_ est disponible sur le terminal.   |
| Liens vers des exemples      | Recherchez des exemples mis à jour dans la page [MXNet Model Server](https://github.com/awslabs/mxnet-model-server).    |
| Outils connexes sur la machine virtuelle DSVM      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow Serving

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Un serveur pour exécuter l’inférence sur un modèle TensorFlow.      |
| Éditions DSVM prises en charge      | Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | _tensorflow_model_server_ est disponible sur le terminal.   |
| Liens vers des exemples      | D’autres exemples sont disponibles [en ligne](https://www.tensorflow.org/serving/).      |
| Outils connexes sur la machine virtuelle DSVM      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Un serveur d’inférence d’apprentissage profond NVIDIA. |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | TensorRT est installé comme un package _apt_.   |
| Liens vers des exemples      | D’autres exemples sont disponibles [en ligne](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Outils connexes sur la machine virtuelle DSVM      | TensorFlow Serving, MXNet Model Server  |



