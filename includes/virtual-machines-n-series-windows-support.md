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
ms.openlocfilehash: 22d2ddc65616bbeb0c511afa6f60bc12c6ec67ef
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94482754"
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

Les pilotes GRID redistribués par Azure ne fonctionnent pas sur les machines virtuelles de série autre que NV, comme les machines virtuelles de série NC, NCv2, NCv3, ND et NDv2. La seule exception est la série de machines virtuelles NCas_T4_V3 dans laquelle les pilotes GRID activent des fonctionnalités graphiques similaires à celles de la série NV.

Notez que l’extension Nvidia installe toujours le dernier pilote. Nous fournissons ici des liens vers la version précédente pour les clients qui dépendent d’une version antérieure.

Pour Windows Server 2019, Windows Server 2016 et Windows 10 (jusqu'à la version 2004) :
- [GRID 11.1 (452.39)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRID 11.0 (451.48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

Pour Windows Server 2012 R2 : 
- [GRID 11.0 (451.48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 
- [GRID 10.1 (442.66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (.exe)  
