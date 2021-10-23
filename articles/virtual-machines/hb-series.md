---
title: Série HB
description: Spécifications pour les machines virtuelles de la série HB.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 3cff97959a897d20acf693926c75f6c20774d361
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071451"
---
# <a name="hb-series"></a>Série HB

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les machines virtuelles de série HB sont optimisées pour les applications qui sont tributaires de la bande passante mémoire, comme la dynamique des fluides, l’analyse explicite par éléments finis et la modélisation météorologique. Les machines virtuelles HB disposent de 60 cœurs de processeur AMD EPYC 7551, de 4 Go de RAM par cœur de processeur, et d’aucun multithreading simultané. Une machine virtuelle HB fournit jusqu’à 260 Go/s de bande passante de mémoire.

Les machines virtuelles d’origine de la série HB sont dotées de la technologie Mellanox EDR InfiniBand à 100 Gb/s. Ces machines virtuelles sont connectées dans une arborescence FAT non bloquante pour des performances RDMA optimisées et cohérentes. Ces machines virtuelles prennent en charge le routage adaptatif et le transport connecté dynamique (DCT, en plus des transports RC et UD standard). Ces fonctionnalités améliorent les performances, la scalabilité et la cohérence des applications, et leur utilisation est recommandée.

[ACU](acu.md) : 199-216<br>
[Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Disques Ultra](disks-types.md#ultra-disks) : pris en charge ([En savoir plus](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) sur la disponibilité, l’utilisation et les performances) <br>
[Migration dynamique](maintenance-and-updates.md) : Non pris en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Non pris en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Génération 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : prises en charge ([En savoir plus](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) sur les performances et les problèmes potentiels) <br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge ([en préversion](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks))<br>
<br>

| Taille | Processeurs virtuels | Processeur | Mémoire (Gio) | Bande passante mémoire (Go/s) | Fréquence du processeur de base (GHz) | Fréquence de tous les cœurs (GHz, pic) | Fréquence d’un cœur (GHz, pic) | Performances RDMA (Gbit/s) | Prise en charge MPI | Stockage temporaire (Gio) | Disques de données max. | Cartes réseau virtuelles Ethernet max. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 228 | 263 | 2.0 | 2.55 | 2.55 | 100 | Tous | 700 | 4 | 8 |

En savoir plus sur :
- [Architecture et topologie de machine virtuelle](./workloads/hpc/hb-series-overview.md)
- [Pile logicielle](./workloads/hpc/hb-series-overview.md#software-specifications) prise en charge incluant le système d’exploitation
- [Performances](./workloads/hpc/hb-series-performance.md) attendues de la machine virtuelle série H

[!INCLUDE [hpc-include.md](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Autres tailles et informations

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

Calculatrice de prix : [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

Pour plus d’informations sur les types de disques, consultez [Quels sont les types de disque disponibles dans Azure ?](disks-types.md)


## <a name="next-steps"></a>Étapes suivantes

- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
