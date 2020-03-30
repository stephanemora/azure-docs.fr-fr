---
title: Performances des machines virtuelles de série HB de différentes tailles - Machine virtuelles Azure | Microsoft Docs
description: Découvrez les résultats des tests de performances des machines virtuelles de série HB de différentes tailles dans Azure.
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
ms.openlocfilehash: e064db5f67e6f8a7e82093bdae9fac7eaa4b6a55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534199"
---
# <a name="hb-series-virtual-machine-sizes"></a>Tailles des machines virtuelles de la série HB

Différents tests de performances ont été effectués sur les machines de série HB de différentes tailles. Voici certains des résultats obtenus.


| Charge de travail                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | environ 260 Go/s (32-33 Go/s par CCX)  |
| Linpack hautes performances (HPL)                  | Environ 1 000 GigaFLOPS (Rpeak), environ 860 GigaFLOPS (Rmax) |
| Latence et bande passante RDMA                        | 2,35 usec, 96,5 Gbit/s   |
| FIO sur des disques SSD NVMe locaux                           | Environ 1,7 Gbit/s de lectures, environ 1 Gbit/s d’écritures      |  
| IOR sur 4* disques SSD Azure Premium (disques P30 managés, RAID0)**  | Environ 725 Mbit/s de lectures, environ 780 Mbit/s d’écritures   |



## <a name="infiniband-send-latency"></a>Latence d’envoi InfiniBand
Mellanox Perftest.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


|  #bytes         | #iterations     | t_min[microseconde]     | t_max[microseconde]     | t_typical[microseconde] | t_avg[microseconde]     | t_stdev[microseconde]   |
|-----------------|-----------------|-----------------|-----------------|-----------------|-----------------|
| 2               | 1 000            | 2,35            | 12,63           | 2,38            | 2,42            | 0,33            |
| 4               | 1 000            | 2,35            | 18,53           | 2,38            | 2.4             | 0,21            |
| 8               | 1 000            | 2,36            | 6,06            | 2,39            | 2.41            | 0,22            |
| 16              | 1 000            | 2,36            | 6,05            | 2,39            | 2.41            | 0,21            |
| 32              | 1 000            | 2,37            | 18,93           | 2.4             | 2,42            | 0,25            |
| 64              | 1 000            | 2,39            | 17,98           | 2,43            | 2.45            | 0,18            |
| 128             | 1 000            | 2.44            | 19,4            | 2.76            | 2.65            | 0,29            |
| 256             | 1 000            | 3,06            | 18,31           | 3.1             | 3,12            | 0,27            |
| 512             | 1 000            | 3,15            | 7,89            | 3.2             | 3,23            | 0,31            |
| 1 024            | 1 000            | 3,27            | 17,62           | 3,31            | 3,33            | 0,22            |
| 2 048            | 1 000            | 3.48            | 7,94            | 3.52            | 3.55            | 0,26            |
| 4096            | 1 000            | 3,91            | 7,7             | 3,96            | 3,98            | 0,21            |


## <a name="osu-mpi-latency-test"></a>Test de latence OSU MPI

OSU MPI Latency Test v5.4.3.

```azure-cli
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```


| #bytes  | Latence [microseconde] (MPICH 3.3 + CH4) | Latence [microseconde] (OpenMPI 4.0.0) | Latence [microseconde] (MVAPICH2 2.3) | Latence [microseconde] (Intel MPI 2019) |
|------|----------|----------|----------|----------|
| 2    | 2.44     | 2.52     | 2.84     | 2.76     |
| 4    | 2.44     | 2.53     | 2.84     | 2.76     |
| 8    | 2.44     | 2.53     | 2.83     | 2.76     |
| 16   | 2.45     | 2.53     | 2.87     | 2.77     |
| 32   | 2.62     | 2.69     | 2,89     | 2.78     |
| 64   | 2.72     | 2.79     | 2,93     | 2.85     |
| 128  | 2.76     | 2,88     | 3,06     | 2,91     |
| 256  | 3,53     | 3.65     | 3,73     | 3.57     |
| 512  | 3.68     | 3,78     | 3,81     | 3.70     |
| 1 024 | 3,86     | 3,97     | 3,95     | 3,93     |
| 2 048 | 4,12     | 4.5      | 4.24     | 4,22     |
| 4096 | 4,79     | 5.28     | 6,33     | 4,91     |


## <a name="mpi-bandwidth"></a>Bande passante MPI

OSU MPI Bandwidth Test v5.4.3.

```azure-cli
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

| #Size            | Bande passante (Mo/s) | Bande passante (Gbits/s) |
|------------------|------------------|------------------|
| 2                | 4,03             | 0,03             |
| 4                | 8,2              | 0,07             |
| 8                | 16,15            | 0,13             |
| 16               | 32,33            | 0,26             |
| 32               | 64,36            | 0,51             |
| 64               | 126,29           | 1.01             |
| 128              | 234,14           | 1,87             |
| 256              | 486,89           | 3,90             |
| 512              | 874,24           | 6,99             |
| 1 024             | 1 538,47          | 12,31            |
| 2 048             | 2743,98          | 21,95            |
| 4096             | 4 194,69          | 33,56            |
| 8 192             | 5657,67          | 45,26            |
| 16384            | 7 618,96          | 60,95            |
| 32 768            | 10 333,76         | 82,67            |
| 65536            | 11 171,06         | 89,37            |
| 131 072           | 11 539,64         | 92,32            |
| 262 144           | 11 768,43         | 94,15            |
| 524 288           | 11 908.59         | 95,27            |
| 1 048 576          | 12 012,8          | 96,10            |
| 2 097 152          | 12 049,38         | 96,40            |
| 4 194 304          | 12 061,33         | 96,49            |


## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur le [calcul haute performance](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) dans Azure.




