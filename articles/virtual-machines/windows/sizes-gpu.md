---
title: Tailles des machines virtuelles Windows Azure - GPU
description: Répertorie les différentes tailles de GPU optimisées pour les machines virtuelles Windows dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 1f26ad56734dfec0dbf7fc8198b8a91a706a843b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371069"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tailles de machine virtuelle à GPU optimisé

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

Pour tirer parti des fonctionnalités GPU des machines virtuelles Azure de série N exécutant Windows, installez des pilotes GPU NVIDIA ou AMD. 

[L’extension du pilote GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) installe les pilotes CUDA ou GRID NVIDIA appropriés sur une machine virtuelle de série N. Installez ou gérez l’extension à l’aide du portail Azure ou d’outils tels qu’Azure PowerShell ou les modèles Azure Resource Manager. Consultez la [documentation sur l’extension du pilote GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) pour connaître les systèmes d’exploitation pris en charge et les étapes de déploiement. Pour des informations générales sur les extensions de machine virtuelle, consultez [Extensions et fonctionnalités des machines virtuelles Azure](../extensions/overview.md).

Si vous choisissez d’installer manuellement les pilotes GPU NVIDIA, consultez [Configuration des pilotes GPU de série N pour Windows](n-series-driver-setup.md) pour connaître les systèmes d’exploitation pris en charge, les pilotes et les étapes d’installation et de vérification.

Pour installer manuellement les pilotes GPU AMD, consultez [Configuration des pilotes GPU AMD de série N pour Windows](n-series-amd-driver-setup.md) afin de connaître les systèmes d'exploitation et pilotes pris en charge, ainsi que les étapes d'installation et de vérification.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>Autres tailles
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Calcul haute performance](sizes-hpc.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes
Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.

