---
title: Performances des disques et des machines virtuelles
description: Découvrir plus d’informations sur la façon dont les machines virtuelles et leurs disques attachés fonctionnent en combinaison pour les meilleures performances
author: albecker1
ms.author: albecker
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6621efe6d0b391ed38ab49f147d791a11db14301
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663217"
---
# <a name="virtual-machine-and-disk-performance"></a>Performances des disques et des machines virtuelles
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limites des machines virtuelles sans mise en cache et avec mise en cache
Les machines virtuelles pour lesquelles à la fois le stockage Premium et la mise en cache du stockage Premium sont activés ont deux limites différentes quant à la bande passante du stockage. Poursuivons en examinant comme exemple la machine virtuelle Standard_D8s_v3. Voici la documentation sur la [série Dsv3](../dv3-dsv3-series.md) et en particulier de la machine virtuelle Standard_D8s_v3 :

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]
