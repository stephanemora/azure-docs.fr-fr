---
title: Vue d’ensemble des machines virtuelles série HC - Machine virtuelles Azure | Microsoft Docs
description: En savoir plus sur la prise en charge de la préversion de la taille de machine virtuelle série HC dans Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906746"
---
# <a name="hc-series-virtual-machine-overview"></a>Vue d’ensemble des machines virtuelles série HC

L’optimisation des performances des applications HPC sur les processeurs scalable Intel Xeon nécessite une approche bien pensée pour traiter le placement dans cette nouvelle architecture. Ici, nous en décrivons l’implémentation sur des machines virtuelles série HC Azure pour les applications HPC. Nous utiliserons le terme « pNUMA » pour faire référence à un domaine NUMA physique et « vNUMA » pour faire référence à un domaine NUMA virtualisé. De même, nous utiliserons le terme « pCore » pour faire référence à des cœurs de processeur physiques et « vCore » pour faire référence à des cœurs de processeur virtualisés.

Physiquement, un serveur HC correspond à 2 UC Intel Xeon Platinum 8168 à 24 cœurs pour un total de 48 cœurs physiques. Chaque UC est un domaine pNUMA unique et a un accès unifié à six canaux de DRAM. Les UC Intel Xeon Platinum disposent d’un cache L2 4 fois supérieur à celui des générations précédentes (256 Ko/cœur -> 1 Mo/cœur), tout en réduisant le cache L3 par rapport aux UC Intel précédentes (2,5 Mo/cœur -> 1,375 Mo/cœur).

La topologie ci-dessus s’applique aussi à la configuration de l’hyperviseur série HC. Pour permettre à l’hyperviseur Azure de fonctionner sans interférer avec la machine virtuelle, nous réservons les pCores 0-1 et 24-25 (autrement dit, les 2 premiers pCores sur chaque socket). Nous affectons ensuite des domaines pNUMA à tous les cœurs restants sur la machine virtuelle. Par conséquent, la machine virtuelle verra :

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` cœurs par machine virtuelle

La machine virtuelle ne sait pas que les pCores 0-1 et 24-25 ne lui ont pas été attribués. Par conséquent, elle expose chaque vNUMA comme si elle avait 22 cœurs en mode natif.

Les UC Intel Xeon Platinum, Gold et Silver inaugurent également un réseau maillé 2D on-die pour la communication interne et externe au socket de l’UC. Nous recommandons vivement l’épinglage de processus (process pinning) à des fins de performances optimales. Le brochage de processus fonctionne sur les machines virtuelles série HC, car le silicium sous-jacent est exposé comme tel à la machine virtuelle invitée. Pour plus d’informations, consultez [Architecture Intel Xeon SP](https://bit.ly/2RCYkiE).

Le diagramme suivant illustre la répartition des cœurs réservés pour l’hyperviseur Azure et la machine virtuelle série HC.

![Répartition des cœurs réservés pour l’hyperviseur Azure et la machine virtuelle série HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Spécifications matérielles

| Spécifications matérielles          | Machine virtuelle série HC                     |
|----------------------------------|----------------------------------|
| Cœurs                            | 44 (HT désactivé)                 |
| UC                              | Intel Xeon Platinum 8168*        |
| Fréquence de l’UC (non AVX)          | 3,7 GHz (simple cœur) 2,7-3,4 GHz (tous les cœurs) |
| Mémoire                           | 8 Go/cœur (352 total)            |
| Disque local                       | 700 Go NVMe                      |
| Infiniband                       | 100 Gbits EDR Mellanox ConnectX-5** |
| Réseau                          | 50 Gbits Ethernet (40 Gbits utilisables) SmartNIC*** Azure 2ème gén. |

## <a name="software-specifications"></a>Spécifications logicielles

| Spécifications logicielles     | Machine virtuelle série HC          |
|-----------------------------|-----------------------|
| Taille de travail MPI max            | 13200 cœurs (300 machines virtuelles dans un seul VMSS avec singlePlacementGroup=true) |
| Prise en charge MPI                 | MVAPICH2, OpenMPI, MPICH, Platform MPI, Intel MPI  |
| Frameworks supplémentaires       | Unified Communication X, libfabric, PGAS |
| Prise en charge de Stockage Azure       | Std + Premium (4 disques max) |
| Prise en charge du système d’exploitation pour SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Prise en charge d’Azure CycleCloud    | Oui                         |
| Prise en charge d’Azure Batch         | Oui                         |

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les tailles de machines virtuelles HPC pour [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) et [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) dans Azure.

* En savoir plus sur [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) dans Azure.
