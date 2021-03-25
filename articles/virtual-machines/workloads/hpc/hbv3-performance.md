---
title: Performances de la taille de machine virtuelle de la série HBv3
description: En savoir plus sur les résultats des tests de performances pour les machines virtuelles de la série HBv3 dans Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 87c3e4e9b509589624a228ea2e1f4b68e86e3fa8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721142"
---
# <a name="hbv3-series-virtual-machine-performance"></a>Performances de la taille de machine virtuelle de la série HBv3

Les utilisateurs ayant un accès anticipé aux machines virtuelles HBv3 peuvent s’attendre aux performances suivantes pour les micro-benchmarks HPC courants

| Charge de travail                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 330-350 Go/s (environ 82-86 Go/s par NUMA)                                     |
| Linpack hautes performances (HPL)                  | 4 TF (Rpeak, FP64),8 TF (Rpeak, FP32) pour la taille de machine virtuelle 120 cœurs               |
| Latence et bande passante RDMA                        | 1,2 microseconde (1 octet), 192 Gbits/s (unidirectionnel)                                        |
| FIO sur des disques SSD NVMe locaux (RAID0)                  | 7 Go/s lectures, 3 Go/s écritures ; 186 000 IOPS lectures, 201 000 IOPS écritures |

## <a name="process-pinning"></a>Épinglage de processus

L’épinglage de processus fonctionne bien sur les machines virtuelles de série HBv3, car nous exposons le silicium sous-jacent tel quel à la machine virtuelle invitée. Nous recommandons vivement l’épinglage de processus (process pinning) à des fins de performances optimales.

## <a name="mpi-latency"></a>Latence MPI

Le test de latence MPI à partir de la suite de microtest OSU peut être exécuté comme indiqué ci-dessous. Les exemples de scripts se trouvent sur [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>Bande passante MPI
Le test de bande passante MPI à partir de la suite de microtest OSU peut être exécuté comme indiqué ci-dessous. Les exemples de scripts se trouvent sur [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox Perftest
Le [package Mellanox Peretpt](https://community.mellanox.com/s/article/perftest-package) comporte de nombreux tests InfiniBand tels que la latence (ib_send_lat) et la bande passante (ib_send_bw). Vous trouverez ci-dessous un exemple de commande. 
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur la [mise à l’échelle des applications MPI](compiling-scaling-applications.md).
- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).