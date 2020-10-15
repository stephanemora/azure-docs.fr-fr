---
title: Colocalisation de machines virtuelles Linux
description: En savoir plus sur la façon dont la colocalisation des ressources de machine virtuelle Azure pour Linux peut améliorer la latence.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 822d67211982526bdd7a36cb5922f33b448eb7c7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973374"
---
# <a name="co-locate-resources-for-improved-latency"></a>Colocalisation des ressources pour une latence améliorée

Lors du déploiement de votre application dans Azure, la répartition des instances sur différentes régions ou zones de disponibilité crée une latence réseau, ce qui peut avoir un impact sur les performances globales de votre application. 

## <a name="proximity-placement-groups"></a>Groupes de placements de proximité

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Étapes suivantes

Déployez une machine virtuelle [sur un groupe de placements de proximité](proximity-placement-groups.md) à l’aide d’Azure CLI.

Découvrez comment [tester le temps de réponse du réseau](../../virtual-network/virtual-network-test-latency.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json).

Découvrez comment [optimiser le débit du réseau](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Découvrez comment [utiliser des groupes de placement de proximité avec des applications SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).