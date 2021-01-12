---
title: Performances des disques et des machines virtuelles - Linux
description: Découvrez-en plus sur le fonctionnement combiné des machines virtuelles et des disques qui leur sont attachés pour optimiser les performances sur Linux.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591852"
---
# <a name="virtual-machine-and-disk-performance-linux"></a>Performances des disques et des machines virtuelles (Linux)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limites des machines virtuelles sans mise en cache et avec mise en cache
Les machines virtuelles pour lesquelles le stockage Premium et la mise en cache du stockage Premium sont activés présentent deux limites différentes en matière de bande passante de stockage. Examinons la machine virtuelle Standard_D8s_v3 à titre d’exemple. Voici la documentation relative à la [série Dsv3](../dv3-dsv3-series.md) et à la machine virtuelle Standard_D8s_v3 :

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Exécutons un test d’évaluation sur cette combinaison de machine virtuelle et de disque qui crée une activité d’E/S. Pour savoir comment créer un benchmark des E/S de stockage sur Azure, consultez [Créer un benchmark de votre application sur Stockage sur disque Azure](disks-benchmarks.md). À partir de l’outil d’évaluation, vous pouvez voir que la combinaison de machines virtuelles et de disques peut atteindre 22 800 IOPS :

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
