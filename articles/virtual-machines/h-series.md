---
title: Série H - Machines virtuelles Azure
description: Spécifications pour les machines virtuelles de la série H.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: e9f876f3d20af01867283f550590b3af23dec662
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926618"
---
# <a name="h-series"></a>Série H

Les machines virtuelles de la série H sont optimisées pour les applications tributaires de fréquences de processeur élevées ou de besoins en mémoire important par cœur. Les machines virtuelles de la série H disposent de 8 ou 16 cœurs de processeur Intel Xeon E5 2667 v3, de 14 Go de RAM par cœur de processeur, et d’aucun hyperthreading. Les machines virtuelles de la série H disposent d’un Infiniband FDR Mellanox de 56 Go/s dans une configuration de gros arbres non bloquant pour des performances RDMA homogènes. Les machines virtuelles de la série H prennent en charge Intel MPI 5.x et MS-MPI.

ACU : 290-300

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Migration dynamique : Non pris en charge

Mises à jour avec préservation de la mémoire : Non pris en charge

| Taille | Processeurs virtuels | Processeur | Mémoire (Go) | Bande passante mémoire (Go/s) | Fréquence du processeur de base (GHz) | Fréquence de tous les cœurs (GHz, pic) | Fréquence d’un cœur (GHz, pic) | Performances RDMA (Gbit/s) | Prise en charge MPI | Stockage temporaire (Go) | Disques de données max. | Débit de disque max : E/S par seconde | Cartes réseau Ethernet max. |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1 000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1 000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand FDR.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

> [!NOTE]
> Parmi les [machines virtuelles compatibles RDMA](sizes-hpc.md#rdma-capable-instances), les séries H ne sont pas compatibles avec SR-IOV. Par conséquent, les [images de machine virtuelle](./workloads/hpc/configure.md#vm-images) prises en charge, les exigences de [pilote InfiniBand](./workloads/hpc/enable-infiniband.md) et les [bibliothèques MPI](./workloads/hpc/setup-mpi.md) prises en charge sont différentes de celles des machines virtuelles compatibles SR-IOV.

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [la configuration de vos machines virtuelles](./workloads/hpc/configure.md), [l’activation d’InfiniBand](./workloads/hpc/enable-infiniband.md), [la configuration de MPI](./workloads/hpc/setup-mpi.md) et l’optimisation des applications HPC pour Azure dans l’article relatif aux [charges de travail HPC](./workloads/hpc/overview.md).
- Découvrez des informations sur les dernières annonces et des exemples et des résultats HPC sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
