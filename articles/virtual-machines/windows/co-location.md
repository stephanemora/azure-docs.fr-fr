---
title: Colocaliser des machines virtuelles pour améliorer la latence
description: En savoir plus sur la façon dont la colocalisation des ressources de machine virtuelle Azure peut améliorer la latence.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083170"
---
# <a name="co-locate-resource-for-improved-latency"></a>Colocalisation des ressources pour une latence améliorée

Lors du déploiement de votre application dans Azure, la répartition des instances sur différentes régions ou zones de disponibilité crée une latence réseau, ce qui peut avoir un impact sur les performances globales de votre application. 


## <a name="proximity-placement-groups"></a>Groupes de placements de proximité 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Étapes suivantes

Déployez une machine virtuelle [sur un groupe de placements de proximité](proximity-placement-groups.md) en utilisant Azure PowerShell.

Découvrez comment [tester le temps de réponse du réseau](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Découvrez comment [optimiser le débit du réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Découvrez comment [utiliser des groupes de placement de proximité avec des applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).