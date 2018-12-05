---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f58d08629fcde791186d27ae09e7417453faf8ad
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52585693"
---
## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

### <a name="nvidia-tesla-cuda-drivers"></a>Pilotes Tesla NVIDIA (CUDA)

Les pilotes Tesla NVIDIA (CUDA) pour les machines virtuelles des séries NC, NCv2, NCv3, ND et NDv2 (facultatif pour la série NV) sont pris en charge uniquement dans les systèmes d’exploitation répertoriées dans le tableau suivant. Les liens de téléchargement des pilotes sont à jour au moment de la publication. Pour les pilotes les plus récents, visitez le site Web de [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Vous pouvez déployez une image de [machine virtuelle de science des données](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure, comme alternative à une installation manuelle du pilote CUDA sur une machine virtuelle Windows Server. Les éditions des machines virtuelles Science des données pour Windows Server 2016 préinstallent les pilotes NVIDIA CUDA, la bibliothèque de réseau neuronal profond et d’autres outils.


| SE | Pilote |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>Pilotes NVIDIA GRID

Microsoft redistribue les programmes d’installation du pilote GRID NVIDIA pour les machines virtuelles de série NV et NVv2 utilisées comme stations de travail virtuelles ou pour les applications virtuelles. Installez uniquement ces pilotes GRID sur des machines virtuelles Azure NV, et uniquement sur les systèmes d’exploitation répertoriés dans le tableau suivant. Ces pilotes incluent les licences des logiciels GRID Virtual GPU dans Azure. Vous n’avez pas besoin de configurer un serveur de licences logicielles NVIDIA vGPU.

| SE | Pilote |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 7 (411.81)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2 | [GRID 7 (411.81)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |
