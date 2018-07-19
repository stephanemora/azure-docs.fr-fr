---
title: Tailles des machines virtuelles Linux Azure - GPU | Microsoft Docs
description: Répertorie les différentes tailles de GPU optimisées disponibles pour les machines virtuelles Linux dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 435dc3a4815d9fe554ccba57a49ed9a772b53529
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904077"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tailles de machine virtuelle à GPU optimisé

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Distributions et pilotes pris en charge

Pour tirer parti des fonctionnalités GPU de machines virtuelles de série N Azure sous Linux, installez des pilotes GPU NVIDIA. [L’extension du pilote GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) installe les pilotes CUDA ou GRID NVIDIA appropriés sur une machine virtuelle de série N. Installez ou gérez l’extension à l’aide du portail Azure ou d’outils tels qu’Azure CLI ou les modèles Azure Resource Manager. Consultez la [documentation sur l’extension du pilote GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) pour connaître les distributions prises en charge et les étapes de déploiement. Pour des informations générales sur les extensions de machine virtuelle, consultez [Extensions et fonctionnalités des machines virtuelles Azure](../extensions/overview.md).

Si vous choisissez d’installer manuellement les pilotes GPU NVIDIA, consultez [Configuration des pilotes GPU de série N pour Linux](n-series-driver-setup.md) pour connaître les distributions prises en charge, les pilotes et les étapes d’installation et de vérification.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Il est préférable de ne pas installer un serveur spécifique ou d’autres systèmes qui utilisent le pilote `Nouveau` sur les machines virtuelles Ubuntu NC. Avant d’installer les pilotes GPU NVIDIA, vous devez désactiver le pilote `Nouveau`.  

## <a name="other-sizes"></a>Autres tailles
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes
Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.