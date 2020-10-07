---
title: Performances des disques et des machines virtuelles
description: Découvrir plus d’informations sur la façon dont les machines virtuelles et leurs disques attachés fonctionnent en combinaison pour les meilleures performances
author: albecker1
ms.author: albecker
ms.date: 09/25/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: c248549fae9e5a0b80f70223a2a1a1f9c7ac9c81
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541524"
---
# <a name="virtual-machine-and-disk-performance"></a>Performances des disques et des machines virtuelles
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limites des machines virtuelles sans mise en cache et avec mise en cache
Les machines virtuelles pour lesquelles à la fois le stockage Premium et la mise en cache du stockage Premium sont activés ont deux limites différentes quant à la bande passante du stockage. Poursuivons en examinant comme exemple la machine virtuelle Standard_D8s_v3. Voici la documentation sur la [série Dsv3](../dv3-dsv3-series.md) et en particulier de la machine virtuelle Standard_D8s_v3 :

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]
