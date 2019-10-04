---
title: Colocaliser des groupes de machines virtuelles identiques Azure | Microsoft Docs
description: Découvrez en quoi la colocalisation des ressources de groupes de machines virtuelles identiques Azure peut améliorer les performances.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/14/2019
ms.author: cynthn
ms.openlocfilehash: 555c4da18169ae9fd1e917fd0b8e3c4e98e87178
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850730"
---
# <a name="co-location"></a>Colocalisation

L’un des plus grands contributeurs à la latence entre les machines virtuelles est tout simplement la distance.

## <a name="preview-proximity-placement-groups"></a>Aperçu : Groupes de placements de proximité 

[!INCLUDE [virtual-machines-common-ppg-overview](../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Étapes suivantes

Créez un [groupe](proximity-placement-groups.md) de placements de proximité pour votre groupe identique.

