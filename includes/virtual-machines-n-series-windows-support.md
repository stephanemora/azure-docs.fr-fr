---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bab282fded7e4b30d2eca6ed51ceaecf22206869
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166710"
---
## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

### <a name="nvidia-tesla-cuda-drivers"></a>Pilotes Tesla NVIDIA (CUDA)

Les pilotes Tesla NVIDIA (CUDA) pour les machines virtuelles des séries NC, NCv2, NCv3, ND et NDv2 (facultatif pour la série NV) sont pris en charge uniquement dans les systèmes d’exploitation répertoriées dans le tableau suivant. Les liens de téléchargement des pilotes sont à jour au moment de la publication. Pour les pilotes les plus récents, visitez le site Web de [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Vous pouvez déployez une image de [machine virtuelle de science des données](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure, comme alternative à une installation manuelle du pilote CUDA sur une machine virtuelle Windows Server. Les éditions des machines virtuelles Science des données pour Windows Server 2016 préinstallent les pilotes NVIDIA CUDA, la bibliothèque de réseau neuronal profond et d’autres outils.


| OS | Pilote |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>Pilotes NVIDIA GRID

Microsoft redistribue les programmes d’installation du pilote GRID NVIDIA pour les machines virtuelles de série NV et NVv3 utilisées comme stations de travail virtuelles ou pour les applications virtuelles. Installez uniquement ces pilotes GRID sur des machines virtuelles Azure de série NV, et uniquement sur les systèmes d’exploitation répertoriés dans le tableau suivant. Ces pilotes incluent les licences des logiciels GRID Virtual GPU dans Azure. Vous n’avez pas besoin de configurer un serveur de licences logicielles NVIDIA vGPU.

Notez que l’extension Nvidia installe toujours le dernier pilote. Nous fournissons ici des liens vers la version précédente pour les clients qui dépendent d’une version antérieure.

| OS | Pilote |
| -------- |------------- |
| Windows Server 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 | [GRID 9.0 (431.02)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) <br/><br/> [GRID 8.0 (425,31)](https://download.microsoft.com/download/4/8/C/48C2D46E-EB64-460E-A8D9-0F55737D0D68/425.31_grid_win10_server2016_64bit_international.exe) (.exe)  |
| Windows Server 2012 R2<br/><br/>Windows Server 2008 R2<br/><br/>Windows 8<br/><br/>Windows 7 | [GRID 9.0 (431.02)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)<br/><br/> [GRID 8.0 (425,31)](https://download.microsoft.com/download/6/D/7/6D73C628-B5FB-4243-9520-DAEF363223CB/425.31_grid_win8_win7_server2012R2_server2008R2_64bit_international.exe) (.exe)  |
