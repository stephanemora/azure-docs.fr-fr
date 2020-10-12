---
title: Colocalisation de machines virtuelles Linux
description: En savoir plus sur la façon dont la colocalisation des ressources de machine virtuelle Azure pour Linux peut améliorer la latence.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 6bf01f4da6ce96b413c87b81c14a998603cebd6b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279721"
---
# <a name="co-locate-resources-for-improved-latency"></a>Colocalisation des ressources pour une latence améliorée

Lors du déploiement de votre application dans Azure, la répartition des instances sur différentes régions ou zones de disponibilité crée une latence réseau, ce qui peut avoir un impact sur les performances globales de votre application. 

## <a name="proximity-placement-groups"></a>Groupes de placements de proximité

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Étapes suivantes

Déployez une machine virtuelle [sur un groupe de placements de proximité](proximity-placement-groups.md) à l’aide d’Azure CLI.

Découvrez comment [tester le temps de réponse du réseau](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Découvrez comment [optimiser le débit du réseau](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Découvrez comment [utiliser des groupes de placement de proximité avec des applications SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
