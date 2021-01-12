---
title: Performances des disques et des machines virtuelles - Windows
description: Découvrez-en plus sur le fonctionnement combiné des machines virtuelles et des disques qui leur sont attachés pour optimiser les performances sur Windows.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584117"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Performances des disques et des machines virtuelles (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limites des machines virtuelles sans mise en cache et avec mise en cache
 Les machines virtuelles pour lesquelles le stockage Premium et la mise en cache du stockage Premium sont activés présentent deux limites différentes en matière de bande passante de stockage. Poursuivons en prenant comme exemple la machine virtuelle Standard_D8s_v3. Voici la documentation relative à la [série Dsv3](../dv3-dsv3-series.md) et, plus particulièrement, à la machine virtuelle Standard_D8s_v3 :

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Nous allons exécuter un test d’évaluation sur cette combinaison de machines virtuelles et de disques qui créera une activité d’E/S, et vous pouvez vous familiariser avec l’évaluation des E/S de stockage sur Azure [ici](disks-benchmarks.md). À partir de l’outil d’évaluation, vous pouvez voir que la combinaison de machines virtuelles et de disques est en mesure d’atteindre 22 800 E/S par seconde :

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]