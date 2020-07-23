---
title: Vue d’ensemble des machines virtuelles série HB - Machines virtuelles Azure | Microsoft Docs
description: En savoir plus sur la prise en charge de la préversion de la taille de machine virtuelle série HB dans Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: fed5606da84d8311785752cc8319b7a3c642c1f5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86508030"
---
# <a name="hb-series-virtual-machines-overview"></a>Vue d’ensemble des machines virtuelles série HB

L’optimisation des performances des applications de calcul haute performance (HPC)sur AMD EPYC nécessite une approche bien pensée de l’emplacement de la mémoire et du placement du processus. Nous décrivons ci-dessous l’architecture AMD EPYC et son implémentation sur Azure pour les applications HPC. Nous utiliserons le terme « pNUMA » pour faire référence à un domaine NUMA physique et « vNUMA » pour faire référence à un domaine NUMA virtualisé.

Physiquement, une série HP a 2 UC EPYC 7551 de 32 cœurs, soit un total de 64 cœurs physiques. Ces 64 cœurs sont divisées en 16 domaines pNUMA (8 par socket), chacun d’eux comprenant quatre cœurs et étant appelé « Complexe UC » (ou « CCX »). Chaque CCX a son propre cache L3, qui représente la limite pNUMA/vNUMA du système d’exploitation. Une paire d’accès de CCXs adjacents se partage l’accès à deux canaux de DRAM physique (32 Go de DRAM dans les serveurs de la série HB).

Pour permettre à l’hyperviseur Azure de fonctionner sans interférer avec la machine virtuelle, nous réservons le domaine pNUMA physique 0 (le premier CCX). Ensuite, nous attribuons les domaines pNUMA 1 à 15 (les unités CCX restantes) à la machine virtuelle. La machine virtuelle verra :

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` cœurs par machine virtuelle

La machine virtuelle proprement dite ne sait pas que pNUMA 0 ne le lui a pas été donné. La machine virtuelle comprend pNUMA 1 à 15 comme vNUMA 0 à 14, avec 7 vNUMA sur vSocket 0 et 8 vNUMA sur vSocket 1. Bien que cela soit asymétrique, votre système d’exploitation doit démarrer et fonctionner normalement. Plus loin dans ce guide, nous indiquons comment exécuter au mieux des applications MPI sur cette disposition NUMA asymétrique.

L’épinglage de processus fonctionne sur les machines virtuelles série HB, car nous exposons le silicium sous-jacent tel quel à la machine virtuelle invitée. Nous recommandons vivement l’épinglage de processus (process pinning) à des fins de performances optimales.

Pour en savoir plus, consultez [Architecture AMD EPYC](https://bit.ly/2Epv3kC) et [Architectures multiprocesseur](https://bit.ly/2GpQIMb) sur LinkedIn. Pour plus d’informations, consultez le [Guide d’optimisation HPC pour les processeurs AMD EPYC](https://bit.ly/2T3AWZ9).

Le diagramme suivant illustre la répartition des cœurs réservés pour l’hyperviseur Azure et la machine virtuelle série HB.

![Répartition des cœurs réservés pour l’hyperviseur Azure et la machine virtuelle série HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Spécifications matérielles

| Spécifications matérielles                | Machine virtuelle série HB                     |
|----------------------------------|----------------------------------|
| Cœurs                            | 60 (SMT désactivé)                |
| UC                              | AMD EPYC 7551*                   |
| Fréquence de l’UC (non AVX)          | ~2,55 GHz (simple + tous les cœurs)   |
| Mémoire                           | 4 Go/cœur (240 total)            |
| Disque local                       | 700 Go NVMe                      |
| Infiniband                       | 100 Gbits EDR Mellanox ConnectX-5** |
| Réseau                          | 50 Gbits Ethernet (40 Gbits utilisables) SmartNIC*** Azure 2ème gén. |

## <a name="software-specifications"></a>Spécifications logicielles

| Spécifications logicielles           |Machine virtuelle série HB           |
|-----------------------------|-----------------------|
| Taille de travail MPI max            | 6 000 cœurs (100 groupes de machines virtuelles identiques), 12 000 cœurs (200 groupes de machines virtuelles identiques)  |
| Prise en charge MPI                 | MVAPICH2, OpenMPI, MPICH, Platform MPI, Intel MPI  |
| Frameworks supplémentaires       | Unified Communication X, libfabric, PGAS |
| Prise en charge de Stockage Azure       | Std + Premium (4 disques max) |
| Prise en charge du système d’exploitation pour SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Prise en charge d’Azure CycleCloud    | Oui                         |
| Prise en charge d’Azure Batch         | Oui                         |

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les tailles de machines virtuelles HPC pour [Linux](../../sizes-hpc.md) et [Windows](../../sizes-hpc.md) dans Azure.

* En savoir plus sur [HPC](/azure/architecture/topics/high-performance-computing/) dans Azure.
