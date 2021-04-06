---
title: Série HBv3 – Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles de la série HBv3.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: a03eac3969e8918c8da20b90d0dcf8b60b39b8ee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800837"
---
# <a name="hbv3-series"></a>Série HBv3

Les machines virtuelles de la série HBv3 sont optimisées pour les applications HPC telles que la dynamique des fluides, l’analyse des éléments finis explicites et implicites, la modélisation de phénomènes météorologiques, le traitement de données sismiques, la simulation de réservoir et la simulation RTL. Les machines virtuelles HBv3 offrent jusqu’à 120 cœurs de processeurs AMD EPYC™ 7003 (Milan), 448 Go de RAM et aucun hyperthreading. Les machines virtuelles de la série HBv3 offrent également une bande passante mémoire de 350 Go/sec, jusqu’à 32 Mo de cache L3 par cœur, des performances de disque SSD allant jusqu’à 7 Go/s et des fréquences d’horloge allant jusqu’à 3,675 GHz. 

Toutes les machines virtuelles de la série HBv3 sont équipées d’InfiniBand HDR à 200 Gbit/s des solutions de mise en réseau NVIDIA pour permettre des charges de travail MPI à l’échelle d’un superordinateur. Ces machines virtuelles sont connectées dans une arborescence FAT non bloquante pour des performances RDMA optimisées et cohérentes. L’architecture InfiniBand HDR prend également en charge le routage adaptatif et le transport connecté dynamique (DCT, en plus des transports RC et UD standards). Ces fonctionnalités améliorent les performances, la scalabilité et la cohérence des applications, et leur utilisation est vivement recommandée.

[Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Non pris en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Non pris en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Génération 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Bientôt disponible<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Non pris en charge <br>
<br>

|Taille |Processeurs virtuels |Processeur |Mémoire (Gio) |Bande passante mémoire (Go/s) |Fréquence du processeur de base (GHz) |Fréquence de tous les cœurs (GHz, pic) |Fréquence d’un cœur (GHz, pic) |Performances RDMA (Gbit/s) |Prise en charge MPI |Stockage temporaire (Gio) |Disques de données max. |Cartes réseau virtuelles Ethernet max. |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Tous |2 * 960 |32 |8 |
|Standard_HB120-96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Tous |2 * 960 |32 |8 |
|Standard_HB120-64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Tous |2 * 960 |32 |8 |
|Standard_HB120-32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Tous |2 * 960 |32 |8 |
|Standard_HB120-16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Tous |2 * 960 |32 |8 |

En savoir plus sur :
- [Architecture et topologie de machine virtuelle](./workloads/hpc/hbv3-series-overview.md)
- [Pile logicielle](./workloads/hpc/hbv3-series-overview.md#software-specifications) prise en charge incluant le système d’exploitation
- [Performances](./workloads/hpc/hbv3-performance.md) attendues de la machine virtuelle HBv3

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
