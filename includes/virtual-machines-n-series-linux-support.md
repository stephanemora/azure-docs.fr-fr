---
title: Fichier Include
description: Fichier Include
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3d4c58cd9e7ee0674f42d776c8df6e96faba1ab1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799488"
---
## <a name="supported-distributions-and-drivers"></a>Distributions et pilotes pris en charge

### <a name="nvidia-cuda-drivers"></a>Pilotes CUDA NVIDIA

Les pilotes CUDA NVIDIA pour les machines virtuelles des séries NC, NCv2, NCv3, ND et NDv2 (facultatif pour la série NV) sont pris en charge uniquement dans les distributions Linux figurant dans le tableau suivant. Les informations relatives aux pilotes CUDA sont à jour au moment de la publication. Pour les pilotes CUDA les plus récents, visitez le site web de [NVIDIA](https://developer.nvidia.com/cuda-zone). Assurez-vous que vous installez ou mettez à niveau en utilisant les derniers pilotes CUDA pour votre distribution. 

> [!TIP]
> Vous pouvez déployez une image de [machine virtuelle de science des données](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure, comme alternative à une installation manuelle du pilote CUDA sur une machine virtuelle Linux. Les éditions des machines virtuelles Science des données pour Ubuntu 16.04 LTS ou CentOS 7.4 préinstallent les pilotes NVIDIA CUDA, la bibliothèque de réseau neuronal profond et d’autres outils.

| Distribution | Pilote |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> HPC basé sur CentOS 7.3, 7.4, 7.5, 7.6, HPC basé sur CentOS 7.4 | NVIDIA CUDA 10.1, branche pilote R418 |

### <a name="nvidia-grid-drivers"></a>Pilotes NVIDIA GRID

Microsoft redistribue les programmes d’installation du pilote GRID NVIDIA pour les machines virtuelles de série NV et NVv2 utilisées comme stations de travail virtuelles ou pour les applications virtuelles. Installez uniquement ces pilotes GRID sur des machines virtuelles Azure NV, et uniquement sur les systèmes d’exploitation répertoriés dans le tableau suivant. Ces pilotes incluent les licences des logiciels GRID Virtual GPU dans Azure. Vous n’avez pas besoin de configurer un serveur de licences logicielles NVIDIA vGPU.

| Distribution | Pilote |
| --- | -- |
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/>CentOS 7.3, 7.4, 7.5, 7.6 | NVIDIA GRID 7.1, branche pilote R410|

> [!WARNING] 
> L’installation de logiciels tiers sur des produits Red Hat peut affecter les conditions de prise en charge de Red Hat. Consultez l’[article de la Base de connaissances Red Hat](https://access.redhat.com/articles/1067).
>
