---
title: Débit réseau des machines virtuelles Azure | Microsoft Docs
description: Découvrez plus d’informations sur le débit réseau des machines virtuelles Azure.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: kumud,steveesp, mareat
ms.openlocfilehash: 9d74e53c754367ecfa63642514db93354fcadf25
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153732"
---
# <a name="virtual-machine-network-bandwidth"></a>Bande passante réseau des machines virtuelles

Azure offre une variété de tailles et de types de machines virtuelles, qui présentent une combinaison différente de capacités de performances. L’une de ces capacités est la bande passante réseau (ou débit réseau), mesurée en mégabits par seconde (Mbits/s). Étant donné que les machines virtuelles sont hébergées sur du matériel partagé, la capacité du réseau doit être répartie équitablement entre les machines virtuelles partageant le même matériel. Les machines virtuelles plus volumineuses bénéficient d’une plus grande bande passante que les machines plus petites.
 
La bande passante réseau allouée à chaque machine virtuelle est mesurée en fonction du trafic sortant de la machine virtuelle. L’ensemble du trafic réseau quittant la machine virtuelle est mesuré en fonction de la limite d’allocation, quelle que soit la destination. Par exemple, si une machine virtuelle se voit allouer une limite de 1 000 Mbits/s, cette limite s’applique de toute façon, que le trafic sortant soit destiné à une machine virtuelle du réseau virtuel ou à une machine en dehors d’Azure.
 
Le trafic en entrée n’est pas compté ou limité directement. Toutefois, il existe d’autres facteurs, tels que les limites en termes de processeur et de stockage, susceptibles d’affecter la capacité d’une machine virtuelle à traiter les données entrantes.

La mise en réseau accélérée est une fonctionnalité conçue pour améliorer les performances du réseau, notamment la latence, le débit et le taux d’utilisation des processeurs. La mise en réseau accélérée peut améliorer le débit de la machine virtuelle, mais en deçà de la limite de bande passante allouée à la machine virtuelle. Pour en savoir plus sur la mise en réseau accélérée, consultez « Mise en réseau accélérée pour les machines virtuelles [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux](create-vm-accelerated-networking-cli.md) ».
 
Les machines virtuelles Azure doivent être associées à une interface réseau minimum. La bande passante allouée à une machine virtuelle représente l’intégralité du trafic sortant sur l’ensemble des interfaces réseau associées à une machine virtuelle. En d’autres termes, la bande passante est allouée machine virtuelle par machine virtuelle, quel que soit le nombre d’interfaces réseau associées à cette machine. Pour savoir combien d’interfaces réseau les machines virtuelles Azure de différentes tailles peuvent prendre en charge, consultez les informations sur les tailles de machines virtuelles [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="expected-network-throughput"></a>Débit réseau attendu

Le débit sortant attendu et le nombre d’interfaces réseau prises en charge par chaque taille de machine virtuelle sont détaillés dans les spécifications des tailles de machine virtuelle Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Sélectionnez un type, comme le type d’usage général, puis choisissez une série de tailles dans la page qui en résulte, par exemple « Dv2-series ». Chaque série comporte une table, dont la dernière colonne, intitulée **Nombre max de cartes réseau/Performance réseau attendue (Mbits/s)**, détaille les spécifications de mise en réseau. 

Cette limite de débit s’applique à la machine virtuelle. Le débit n’est pas affecté par les facteurs suivants :
- **Nombre d’interfaces réseau**: La limite de bande passante est cumulative de tout le trafic sortant à partir de la machine virtuelle.
- **Mise en réseau accélérée**: Toutefois, la fonctionnalité peut être utile pour atteindre la limite publiée, elle ne modifie pas la limite.
- **Destination du trafic**: Toutes les destinations sont pris en compte la limite de sortie.
- **Protocole** : Tout le trafic sortant sur tous les protocoles est comptabilisée dans la limite.

## <a name="network-flow-limits"></a>Limites de flux de réseau

En plus de bande passante, le nombre de connexions de réseau présents sur une machine virtuelle à un moment donné peut affecter ses performances réseau. La pile de mise en réseau Azure gère l’état pour chaque direction d’une connexion TCP/UDP dans des structures de données appelée « flux ». Une connexion TCP/UDP par défaut aura 2 flux créés, un pour le trafic entrant et l’autre pour la direction sortante. 

Transfert de données entre les points de terminaison nécessite la création de plusieurs flux en plus de ceux qui effectuent le transfert de données. Certains exemples sont des flux créés pour la résolution DNS et les flux créés pour les sondes d’intégrité équilibreur de charge. Notez que le réseau virtuels appliances virtuelles telles que les passerelles, les proxys, les pare-feu, consultez également les flux en cours de création pour les connexions arrêté au niveau de l’appliance et provenant de l’appliance. 

![Nombre de flux pour les conversations TCP via une appliance de transfert](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limites de flux et des recommandations

Aujourd'hui, la pile de mise en réseau Azure prend en charge 250K réseau total flux avec des performances optimales pour les machines virtuelles avec plus de 8 cœurs de processeur et de 100 k total des flux avec des performances optimales pour les machines virtuelles avec moins de 8 cœurs de processeur. Au-delà de ce réseau limite les performances se dégradent normalement pour des flux supplémentaires jusqu'à une limite inconditionnelle de 1 million nombre total de flux, 500 K entrant et 500 K sortant, après les flux supplémentaires sont supprimées.

||Machines virtuelles avec < 8 cœurs de processeur|Machines virtuelles avec des cœurs de processeur 8 +|
|---|---|---|
|<b>Bonnes performances</b>|Flux de 100 Ko |Flux de 250K|
|<b>Dégradation des performances</b>|Au-dessus de 100k transite|Supérieure à 250K transite|
|<b>Limite de flux</b>|Flux de 1M|Flux de 1M|

Métriques sont disponibles dans [Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) pour suivre le nombre de flux de réseau et le taux de création de flux sur vos instances de machine virtuelle ou VMSS.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Taux d’établissement et la fin de connexion peuvent également affecter les performances du réseau en tant que connexion établissement et la fin partages processeur avec des routines de traitement de paquet. Nous vous recommandons d’évaluer charges de travail sur les modèles de trafic attendue et de mise à l’échelle les charges de travail de manière appropriée pour correspondre à vos besoins en performances. 

## <a name="next-steps"></a>Étapes suivantes

- [Optimiser le débit réseau d’un système d’exploitation de machine virtuelle](virtual-network-optimize-network-bandwidth.md)
- [Tester le débit réseau](virtual-network-bandwidth-testing.md) d’une machine virtuelle
