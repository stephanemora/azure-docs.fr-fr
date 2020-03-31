---
title: Performances des machines virtuelles de série HC de différentes tailles - Machine virtuelles Azure | Microsoft Docs
description: Découvrez les résultats des tests de performances des machines virtuelles de série HC de différentes tailles dans Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: cea772f03d5e2838b44d50f3cf5e926d740be5f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707681"
---
# <a name="hc-series-virtual-machine-sizes"></a>Tailles des machines virtuelles de la série HC

Différents tests de performances ont été effectués sur les machines de série HC de différentes tailles. Voici certains des résultats obtenus.

| Charge de travail                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | Environ 190 Go/s (Intel MLC AVX-512)  |
| Linpack hautes performances (HPL)                  | Environ 3520 GigaFLOPS (Rpeak), ~2970 GigaFLOPS (Rmax) |
| Latence et bande passante RDMA                        | 1,80 microseconde, 96,3 Gbits/s   |
| FIO sur des disques SSD NVMe locaux                           | Environ 1,3 Go/s de lectures, environ 900 Mo/s d’écritures |  
| IOR sur 4 disques SSD Azure Premium (disques P30 managés, RAID0)**  | Environ 780 Mo/s de lectures, environ 780 Mo/s d’écritures |

## <a name="infiniband-send-latency"></a>Latence d’envoi InfiniBand

Mellanox Perftest.

```azure-cli
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

|  #bytes         | #iterations     | t_min[microseconde]     | t_max[microseconde]     | t_typical[microseconde] | t_avg[microseconde]     | t_stdev[microseconde]   |
|-----------------|-----------------|-----------------|-----------------|-----------------|-----------------|-----------------|
| 2               | 1 000            | 1.80            | 7.50            | 1.85            | 1.86            | 0.20            |
| 4               | 1 000            | 1.79            | 6,06            | 1.83            | 1.84            | 0.20            |
| 8               | 1 000            | 1.78            | 5.26            | 1.83            | 1.84            | 0.19            |
| 16              | 1 000            | 1.79            | 6.21            | 1.83            | 1.84            | 0,22            |
| 32              | 1 000            | 1.80            | 6.82            | 1.84            | 1.85            | 0.24            |
| 64              | 1 000            | 1.85            | 5.47            | 1.88            | 1.86            | 0.12            |
| 128             | 1 000            | 1.88            | 5.61            | 1.93            | 1.89            | 0,25            |
| 256             | 1 000            | 2.24            | 6.39            | 2.28            | 2.02            | 0,18            |
| 512             | 1 000            | 2.32            | 5.42            | 2,36            | 2.30            | 0.17            |
| 1 024            | 1 000            | 2,43            | 6.22            | 2.48            | 2,38            | 0,21            |
| 2 048            | 1 000            | 2.68            | 6.14            | 2.75            | 2.52            | 0.20            |
| 4096            | 1 000            | 3.17            | 7.02            | 3.26            | 2.81            | 0.24            |

## <a name="osu-mpi-latency-test"></a>Test de latence OSU MPI

OSU MPI Latency Test v5.4.3.

```azure-cli
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

| #bytes  | Latence [microseconde] (MPICH 3.3 + CH4) | Latence [microseconde] (OpenMPI 4.0.0) | Latence [microseconde] (MVAPICH2 2.3) |
|------|----------|----------|----------|
| 2    | 1.84     | 1.78     | 2.08     |
| 4    | 1.84     | 1.79     | 2.08     |
| 8    | 1.85     | 1.79     | 2.05     |
| 16   | 1.85     | 1.79     | 2.1      |
| 32   | 1,87     | 1.82     | 2.12     |
| 64   | 2        | 1.95     | 2.13     |
| 128  | 2.05     | 2        | 2.18     |
| 256  | 2.48     | 2.44     | 2.75     |
| 512  | 2.57     | 2.52     | 2.81     |
| 1 024 | 2.76     | 2.71     | 2,97     |
| 2 048 | 3.09     | 3.11     | 3.34     |
| 4096 | 3.72     | 3,91     | 4.44     |

## <a name="mpi-bandwidth"></a>Bande passante MPI

OSU MPI Bandwidth Test v5.4.3.

```azure-cli
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

| #Size   | Bande passante (Mo/s) | Bande passante (Gbits/s) |
|---------|------------------|------------------|
| 2       | 6.18             | 0.04944          |
| 4       | 13.27            | 0.10616          |
| 8       | 26.58            | 0.21264          |
| 16      | 53.51            | 0.42808          |
| 32      | 106.81           | 0.85448          |
| 64      | 211.24           | 1.68992          |
| 128     | 386.98           | 3.09584          |
| 256     | 756.32           | 6.05056          |
| 512     | 1434.2           | 11.4736          |
| 1 024    | 2663.8           | 21.3104          |
| 2 048    | 4396.99          | 35.17592         |
| 4096    | 6365.86          | 50.92688         |
| 8 192    | 8137.9           | 65.1032          |
| 16384   | 9218.29          | 73.74632         |
| 32 768   | 10564.61         | 84.51688         |
| 65536   | 11275.6          | 90.2048          |
| 131 072  | 11633.7          | 93.0696          |
| 262 144  | 11856.27         | 94.85016         |
| 524 288  | 11962.69         | 95.70152         |
| 1 048 576 | 12025.43         | 96.20344         |
| 2 097 152 | 12038.7          | 96.3096          |
| 4 194 304 | 11290.92         | 90.32736         |
