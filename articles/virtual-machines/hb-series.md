---
title: Série HB
description: Spécifications pour les machines virtuelles de la série HB.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: e735b389b5e223e558736f5eaa3c393f8bc29004
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926261"
---
# <a name="hb-series"></a>Série HB

Les machines virtuelles de la série HB sont optimisées pour des applications tributaires de la bande passante mémoire, par exemple la dynamique des fluides, l’analyse explicite par éléments finis et la modélisation du climat. Les machines virtuelles HB disposent de 60 cœurs de processeur AMD EPYC 7551, de 4 Go de RAM par cœur de processeur, et d’aucun multithreading simultané. Une machine virtuelle HB fournit jusqu’à 260 Go/s de bande passante de mémoire.

ACU : 199-216

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

Migration dynamique : Non pris en charge

Mises à jour avec préservation de la mémoire : Non pris en charge

| Taille | Processeurs virtuels | Processeur | Mémoire (Go) | Bande passante mémoire (Go/s) | Fréquence du processeur de base (GHz) | Fréquence de tous les cœurs (GHz, pic) | Fréquence d’un cœur (GHz, pic) | Performances RDMA (Gbit/s) | Prise en charge MPI | Stockage temporaire (Go) | Disques de données max. | Cartes réseau Ethernet max. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2 | 2.55 | 2.55 | 100 | Tous | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [la configuration de vos machines virtuelles](./workloads/hpc/configure.md), [l’activation d’InfiniBand](./workloads/hpc/enable-infiniband.md), [la configuration de MPI](./workloads/hpc/setup-mpi.md) et l’optimisation des applications HPC pour Azure dans l’article relatifs aux [charges de travail HPC](./workloads/hpc/overview.md).
- Découvrez des informations sur les dernières annonces et des exemples et des résultats HPC sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
