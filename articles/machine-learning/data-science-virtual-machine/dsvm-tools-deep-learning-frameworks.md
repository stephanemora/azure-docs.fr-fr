---
title: Frameworks Deep Learning et d’intelligence artificielle
titleSuffix: Azure Data Science Virtual Machine
description: Infrastructures et outils de Deep Learning disponibles sur Azure Data Science Virtual Machine.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: michalmar
ms.author: mimarusa
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: a757151d17456c7ee9646bc0730a51f34088b255
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729186"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Infrastructures de Deep learning et d’intelligence artificielle pour Azure Data Science Virtual Machine
Les Infrastructures de Deep learning sur Data Science Virtual Machine (DSVM) sont répertoriées ci-dessous.


## <a name="cuda-cudnn-nvidia-driver"></a>[Pilote CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit)

| Category | Valeur |
|--|--|
| Version(s) prise(s) en charge | 11 |
| Éditions DSVM prises en charge | Windows Server 2019<br>Ubuntu 18.04 |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ? | _nvidia-smi_ est disponible sur le chemin système. |
| Comment l’exécuter | Ouvrez une invite de commandes (sur Windows) ou un terminal (sur Linux), puis exécutez _nvidia-smi_. |
## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| Category | Valeur |
| ------------- | ------------- |
| Version(s) prise(s) en charge | 0.21.3|
| Éditions DSVM prises en charge      | Ubuntu 18.04 |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Horovod est installé dans l’environnement Python 3.5 |
| Comment l’exécuter      | Activez l’environnement approprié sur le terminal, puis exécutez Python. |


## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

| Category | Valeur |
|--|--|
| Version(s) prise(s) en charge |  |
| Éditions DSVM prises en charge | Windows Server 2019<br>Ubuntu 18.04 |
| Pour quoi faire ? | Outil NVIDIA pour interroger l’activité du GPU |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ? | `nvidia-smi` se trouve sur le chemin système. |
| Comment l’exécuter | Sur une machine virtuelle **comprenant des GPU**, ouvrez une invite de commandes (sur Windows) ou un terminal (sur Linux), puis exécutez `nvidia-smi`. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| Category | Valeur |
|--|--|
| Version(s) prise(s) en charge | 1.9.0 (Ubuntu 18.04, Windows 2019) |
| Éditions DSVM prises en charge | Windows Server 2019<br>Ubuntu 18.04 |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ? | Installé dans Python, environnements conda 'py38_default', 'py38_pytorch' |
| Comment l’exécuter | Terminal : Activez l’environnement approprié, puis exécutez Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine) : Connectez-vous, puis ouvrez le répertoire PyTorch pour obtenir des exemples. |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| Category | Valeur |
|--|--|
| Version(s) prise(s) en charge | 2.5 |
| Éditions DSVM prises en charge | Windows Server 2019<br>Ubuntu 18.04 |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ? | Installé dans Python, environnements conda 'py38_default', 'py38_tensorflow' |
| Comment l’exécuter | Terminal : Activez l’environnement approprié, puis exécutez Python. <br/> * Jupyter : Connectez-vous à [Jupyter](provision-vm.md) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), puis ouvrez le répertoire TensorFlow pour obtenir des exemples. |
