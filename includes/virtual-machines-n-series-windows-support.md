---
title: fichier descriptif
description: Fichier Include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a188812751552532ae1536529b12e54d55a24a2b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056799"
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

Pour Windows Server 2019, Windows Server 2016, 1607, 1709 et Windows 10 (jusqu’à la build 21H1) :
- [GRID 13 (471.68)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRID 12.2 (462.31)](https://download.microsoft.com/download/0/0/1/001f0edf-d852-4297-9cb7-10b31b1abf45/462.31_grid_win10_server2016_server2019_64bit_azure_swl.exe) (.exe) 

Pour Windows Server 2012 R2 : 
- [GRID 13 (471.68)](https://download.microsoft.com/download/9/b/4/9b4d4f8d-7962-4a67-839b-37cc95756759/471.68_grid_winserver2012R2_64bit_azure_swl.exe) (.exe)
- [GRID 12.2 (462.31)](https://download.microsoft.com/download/1/2/0/120551f5-cc05-4911-bd29-88fb2747213c/462.31_grid_server2012R2_64bit_azure_swl.exe) (.exe) 


Pour obtenir la liste complète de tous les liens précédents du pilote Nvidia GRID, visitez [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
