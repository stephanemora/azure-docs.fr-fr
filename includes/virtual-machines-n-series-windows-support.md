---
title: Fichier include
description: Fichier Include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5a48dd8158ed08aee3da459b4373fb0e4b36fb52
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364878"
---
## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

### <a name="nvidia-tesla-cuda-drivers"></a>Pilotes Tesla NVIDIA (CUDA)

Les pilotes Tesla NVIDIA (CUDA) pour les machines virtuelles des séries NC, NCv2, NCv3, NCasT4_v3, ND et NDv2 (facultatif pour la série NV) sont pris en charge uniquement dans les systèmes d’exploitation répertoriés dans le tableau suivant. Les liens de téléchargement des pilotes sont à jour au moment de la publication. Pour les pilotes les plus récents, visitez le site Web de [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Vous pouvez déployez une image de [machine virtuelle de science des données](../articles/machine-learning/data-science-virtual-machine/overview.md) Azure, comme alternative à une installation manuelle du pilote CUDA sur une machine virtuelle Windows Server. Les éditions des machines virtuelles Science des données pour Windows Server 2016 préinstallent les pilotes NVIDIA CUDA, la bibliothèque de réseau neuronal profond et d’autres outils.


| Système d''exploitation | Pilote |
| -------- |------------- |
| Windows Server 2019 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (.exe) |
| Windows Server 2016 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>Pilotes NVIDIA GRID

Microsoft redistribue les programmes d’installation du pilote GRID NVIDIA pour les machines virtuelles de série NV et NVv3 utilisées comme stations de travail virtuelles ou pour les applications virtuelles. Installez uniquement ces pilotes GRID sur des machines virtuelles Azure de série NV, et uniquement sur les systèmes d’exploitation répertoriés dans le tableau suivant. Ces pilotes incluent les licences des logiciels GRID Virtual GPU dans Azure. Vous n’avez pas besoin de configurer un serveur de licences logicielles NVIDIA vGPU.

Les pilotes GRID redistribués par Azure ne fonctionnent pas sur les machines virtuelles de série autre que NV, comme les machines virtuelles de série NCv2, NCv3, ND et NDv2. La seule exception est la série de machines virtuelles NCas_T4_V3 dans laquelle les pilotes GRID activent des fonctionnalités graphiques similaires à celles de la série NV.

La série NC avec des GPU NVIDIA K80 ne prend pas en charge les applications graphiques/GRID.  

Notez que l’extension Nvidia installe toujours le dernier pilote. Nous fournissons ici des liens vers la version précédente pour les clients qui dépendent d’une version antérieure.

Pour Windows Server 2019, Windows Server 2016 1607, 1709 et Windows 10 (jusqu'à la version 20H2) :
- [GRID 12.2 (462.31)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRID 12.1 (461.33)](https://download.microsoft.com/download/9/7/e/97e1be73-d24b-410b-9c08-cc98c7becfa3/461.33_grid_win10_server2016_server2019_64bit_azure_swl.exe) (.exe) 

Pour Windows Server 2012 R2 : 
- [GRID 12.2 (462.31)](https://download.microsoft.com/download/1/2/0/120551f5-cc05-4911-bd29-88fb2747213c/462.31_grid_server2012R2_64bit_azure_swl.exe) (.exe)
- [GRID 12.1 (461.33)](https://download.microsoft.com/download/9/9/c/99caf5c6-af9f-48b2-bcb0-af5ec64b8592/461.33_grid_server2012R2_64bit_azure_swl.exe) (.exe) 


Pour obtenir la liste complète de tous les liens précédents du pilote Nvidia GRID, visitez [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
