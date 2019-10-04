---
title: Frameworks Deep Learning et d’intelligence artificielle
titleSuffix: Azure Data Science Virtual Machine
description: Frameworks et outils d’apprentissage profond (deep learning) disponibles sur Azure Data Science Virtual Machine, notamment TensorFlow, PyTorch, Keras, Caffe, MXNet, Horovod, Theano, Chainer, etc.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 08/11/2019
ms.openlocfilehash: d8d351659bcfdcdddcc09831b5f95f580bf38f1e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170867"
---
# <a name="deep-learning-and-ai-frameworks-for-azure-data-science-vm"></a>Frameworks d’apprentissage profond (deep learning) et d’intelligence artificielle pour Azure Data Science VM
[Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) prend en charge plusieurs frameworks d’apprentissage profond (deep learning) pour générer des applications d’intelligence artificielle (IA) avec des capacités cognitives et d’analytique prédictive comme la compréhension des images et la reconnaissance vocale.

Les frameworks d’apprentissage profond disponibles par le bias de DSVM sont les suivants :

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ MXNet Model Server
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Deep Water
+ NVIDIA DIGITS
+ nvidia-smi
+ TensorFlow Serving
+ TensorRT
+ Microsoft Cognitive Toolkit

|Outils&nbsp;d’apprentissage&nbsp;profond sur&nbsp;DSVM|Windows|Linux|Notes&nbsp;d’utilisation|
|---------|-------------------|------------------|-----|
|[TensorFlow](https://www.tensorflow.org/) | Oui (Windows 2016) | OUI |Installé dans Python 3.5 sur [Linux et Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), et dans Python 3.6 sur [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Des exemples de notebooks Jupyter sont inclus sur DSVM.<br/><br/>**Pour l’exécuter** :<br/>* Terminal : Activez l’environnement approprié, puis exécutez Python. <br/> * Jupyter : Connectez-vous à [Jupyter](provision-vm.md) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), puis ouvrez le répertoire TensorFlow pour obtenir des exemples.  |
|[PyTorch](https://pytorch.org/)| Non | OUI |Installé dans [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Des exemples de notebooks Jupyter sont inclus, et des exemples sont disponibles dans /dsvm/samples/pytorch.    <br/><br/>**Pour l’exécuter** :<br/>* Terminal : Activez l’environnement approprié, puis exécutez Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine) : Connectez-vous, puis ouvrez le répertoire PyTorch pour obtenir des exemples.  |
|[Keras](https://keras.io/)| OUI | OUI |L’API est installée dans Python 3.5 sur [Linux et Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), et dans Python 3.6 sur [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Consultez des exemples](https://github.com/fchollet/keras/tree/master/examples).<br/><br/>**Pour l’exécuter** :<br/>* Terminal : Activez l’environnement approprié, puis exécutez Python. <br/> * Jupyter : Téléchargez les exemples à partir de l’emplacement GitHub, connectez-vous à [Jupyter](provision-vm.md) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), puis ouvrez le répertoire d’exemples. |
|[Caffe](https://github.com/caffe2/caffe2) | Non |Oui (Ubuntu)|Caffe est installé dans `/opt/caffe`.   Des exemples sont disponibles dans `/opt/caffe/examples`. <br/><br/>**Pour l’exécuter**, utilisez X2Go pour vous connecter à votre machine virtuelle, puis démarrez un nouveau terminal et entrez ce qui suit :<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Une nouvelle fenêtre de navigateur s’ouvre avec les exemples de bloc-notes. Les fichiers binaires sont installés dans /opt/caffe/build/install/bin.<br/><br/>La version installée de Caffe nécessite Python 2.7 et ne fonctionne pas avec Python 3.5, activé par défaut. Pour basculer vers Python 2.7, exécutez `source activate root` afin de passer à l’environnement Anaconda.|
|[Caffe2](https://github.com/caffe2/caffe2) | Non |Oui (Ubuntu)|Caffe2 est installé dans [l’environnement conda Python 2.7 (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). La source se trouve ici : `/opt/caffe2`.<br/>Des exemples de bloc-notes sont inclus dans JupyterHub.<br/><br/>**Pour l’exécuter** :<br/>* Terminal : Activez l’[environnement Python root](dsvm-languages.md#python-linux-and-windows-server-2012-edition), démarrez Python, puis importez Caffe2. <br/> * JupyterHub : [Connectez-vous à JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), puis accédez au répertoire Caffe2 pour rechercher des exemples de notebooks. Certains bloc-notes exigent que la racine Caffe2 soit définie dans le code Python. Entrez /opt/caffe2. |
|[Torch](http://torch.ch/) | Non |Oui (Ubuntu)|Torch est installé dans `/dsvm/tools/torch`. PyTorch est installé dans les environnements Python 2.7 (_root_) et Python 3.5 (_py35_). Des exemples Torch sont disponibles dans `/dsvm/samples/torch` et des exemples PyTorch sont disponibles dans `/dsvm/samples/pytorch`. |
|[MXNet](https://mxnet.io/) | Oui (Windows 2016) | OUI|MXNet est installé dans `C:\dsvm\tools\mxnet` sur Windows et `/dsvm/tools/mxnet` sur Linux. Les liaisons Python sont installées en Python 3.5 sur [Linux et Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), et en Python 3.6 sur [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Les liaisons R sont également installées sur Ubuntu.<br/><br/>Des exemples de bloc-notes Jupyter sont inclus. <br/><br/>**Pour l’exécuter** :<br/>* Terminal : Activez l’environnement approprié, puis exécutez Python. <br/> * Jupyter : Connectez-vous à [Jupyter](provision-vm.md) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), puis ouvrez le répertoire mxnet pour obtenir des exemples.|
|[MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | Non | OUI |Un serveur pour créer des points de terminaison HTTP pour les modèles MXNet et ONNX. _mxnet-model-server_ est disponible sur le terminal. Des exemples sont disponibles dans la page [MXNet Model Server](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Non | Oui (Ubuntu) |Framework d’apprentissage profond distribué pour TensorFlow. Horovod est installé en Python 3.5 sur [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Consultez des exemples](https://github.com/uber/horovod/tree/master/examples).<br/><br/>**Pour l’exécuter**, activez l’environnement approprié sur le terminal, puis exécutez Python. |
|[Theano](https://github.com/Theano/Theano) | Non | Oui (Ubuntu) |Theano est installé dans les environnements Python 2.7 (_root_) et Python 3.5 (_py35_).<br/><br/>**Pour l’exécuter** : <br/>* Terminal : Activez la version de Python souhaitée (root ou py35), exécutez Python, puis importez Theano.<br/>* Jupyter : Sélectionnez le noyau Python 2.7 ou 3.5, puis importez Theano.  <br/>Pour contourner un bogue récent MKL (Math Kernel Library), vous devez d’abord définir la couche de threads MKL comme suit :<br/><br/>`export MKL_THREADING_LAYER=GNU`|
|[Chainer](https://chainer.org/) |Non | OUI |Le Chainer est installé en [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL et ChainerCV sont également installés. <br/><br/>Des exemples de bloc-notes sont inclus dans JupyterHub.<br/><br/>**Pour l’exécuter** : <br/>* Terminal : Activez l’environnement [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), exécutez _python_, puis importez chainer. <br/> * JupyterHub : [Connectez-vous à JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), puis accédez au répertoire Chainer pour rechercher des exemples de notebooks.|
|[NVidia Digits](https://github.com/NVIDIA/DIGITS) | Non | Oui (Ubuntu) |Système d’apprentissage profond de NVIDIA permettant un entraînement rapide des modèles d’apprentissage profond. DIGITS est installé dans `/dsvm/tools/DIGITS` et disponible en tant que service appelé _digits_.  <br/><br/>**Pour l’exécuter** : <br/>Connectez-vous à la machine virtuelle avec X2Go. Sur un terminal, démarrez le service en exécutant ```sudo systemctl start digits```. <br/><br/>Le démarrage du service prend environ une minute. Ouvrez un navigateur web et accédez à `http://localhost:5000`. Notez que DIGITS ne fournit pas de connexion sécurisée et ne doit pas être exposé en dehors de la machine virtuelle.|
|[Pilote CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit) |OUI | OUI | |
|nvidia-smi|OUI | OUI |Outil NVIDIA pour interroger l’activité GPU ; _nvidia-smi_ est disponible sur le chemin système. <br/><br/>Ouvrez une invite de commandes (sur Windows) ou un terminal (sur Linux), puis exécutez _nvidia-smi_.|
|[TensorFlow Serving](https://www.tensorflow.org/serving/) | Non | OUI |Serveur pour effectuer des inférences sur un modèle TensorFlow ; tensorflow_model_server est disponible sur le terminal. D’autres exemples sont disponibles [en ligne](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Non | Oui (Ubuntu) |Un serveur d’inférence d’apprentissage profond NVIDIA. TensorRT est installé comme un package _apt_. D’autres exemples sont disponibles [en ligne](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|OUI | OUI | Installé dans Python 3.5 sur [Linux et Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), et dans Python 3.6 sur [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Des exemples de notebooks Jupyter sont inclus sur DSVM. <br/><br/>**Pour l’exécuter** : <br/>Terminal : Activez l’environnement approprié, puis exécutez Python. <br/>Jupyter : Connectez-vous à [Jupyter](provision-vm.md) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), puis ouvrez le répertoire CNTK pour obtenir des exemples. |
|Deep Water|Non | Oui (Ubuntu) |Framework d’apprentissage profond pour H2O. Deep Water est installé dans [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) et également disponible dans `/dsvm/tools/deep_water`. Des exemples de bloc-notes sont inclus dans JupyterHub. Deep Water nécessite CUDA 8 avec cuDNN 5.1. Par défaut, ceux-ci ne se trouvent pas dans le chemin de la bibliothèque par défaut, car les autres frameworks d’apprentissage profond utilisent CUDA 9 et cuDNN 7. Pour l’utiliser, installez CUDA 8 + cuDNN 5.1 pour Deep Water :<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Pour utiliser Deep Water :<br/>* Terminal : Activez l’environnement [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), puis exécutez _python_. <br/>* JupyterHub : [Connectez-vous à JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), puis accédez au répertoire deep_water pour rechercher des exemples de notebooks.|
