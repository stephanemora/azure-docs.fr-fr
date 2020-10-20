---
title: Performances des disques et des machines virtuelles
description: Découvrir plus d’informations sur la façon dont les machines virtuelles et leurs disques attachés fonctionnent en combinaison pour les meilleures performances
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 3b849640bc7adb38fe51c3c7a9dda2d1d14f35fa
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017248"
---
# <a name="virtual-machine-and-disk-performance"></a>Performances des disques et des machines virtuelles
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limites des machines virtuelles sans mise en cache et avec mise en cache
Les machines virtuelles pour lesquelles à la fois le stockage Premium et la mise en cache du stockage Premium sont activés ont deux limites différentes quant à la bande passante du stockage. Poursuivons en examinant comme exemple la machine virtuelle Standard_D8s_v3. Voici la documentation sur la [série Dsv3](../dv3-dsv3-series.md) et en particulier de la machine virtuelle Standard_D8s_v3 :

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Nous allons exécuter un test d’évaluation sur cette combinaison de machines virtuelles et de disques qui créera une activité d’E/S, et vous pouvez vous familiariser avec l’évaluation des E/S de stockage sur Azure [ici](disks-benchmarks.md). À partir de l’outil d’évaluation, vous pouvez voir que la combinaison de machines virtuelles et de Disk est en mesure d’atteindre 22 800 E/S par seconde :

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]