---
title: Performances des disques et des machines virtuelles
description: Découvrez-en plus sur le fonctionnement combiné des machines virtuelles et des disques qui leur sont attachés pour optimiser les performances.
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 2e3bf032cad39887250c0d0a3cf148e24dc2397a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695849"
---
# <a name="virtual-machine-and-disk-performance"></a>Performances des disques et des machines virtuelles

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limites des machines virtuelles sans mise en cache et avec mise en cache
Les machines virtuelles pour lesquelles le stockage Premium et la mise en cache du stockage Premium sont activés présentent deux limites différentes en matière de bande passante de stockage. Examinons la machine virtuelle Standard_D8s_v3 à titre d’exemple. Voici la documentation relative à la [série Dsv3](dv3-dsv3-series.md) et à la machine virtuelle Standard_D8s_v3 :

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

