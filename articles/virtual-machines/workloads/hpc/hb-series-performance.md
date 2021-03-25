---
title: Performances des tailles de machine virtuelle de la série HB
description: Découvrez les résultats des tests de performances des machines virtuelles de série HB de différentes tailles dans Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f37f0bb14cf29551e85c0e592c327b86048f18e1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721258"
---
# <a name="hb-series-virtual-machine-sizes"></a>Tailles des machines virtuelles de la série HB

Différents tests de performances ont été effectués sur les machines de série HB de différentes tailles. Voici certains des résultats obtenus.

| Charge de travail                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | 260 Go/s (32-33 Go/s par CCX)  |
| Linpack hautes performances (HPL)                  | 1 000 GigaFLOPS (Rpeak), 860 GigaFLOPS (Rmax) |
| Latence et bande passante RDMA                        | 1,27 microseconde, 99,1 Gbits/s   |
| FIO sur des disques SSD NVMe locaux                           | 1,7 Gbit/s de lectures, 1 Gbit/s d’écritures      |  
| IOR sur 4* disques SSD Azure Premium (disques P30 managés, RAID0)**  | Environ 725 Mo/s de lectures, environ 780 Mo/s d’écritures   |


## <a name="mpi-latency"></a>Latence MPI

Le test de latence MPI de la suite de microbenchmark OSU est exécuté. Les exemples de scripts se trouvent sur [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="Latence MPI sur Azure HB.":::

## <a name="mpi-bandwidth"></a>Bande passante MPI

Le test de bande passante MPI de la suite de microbenchmark OSU est exécuté. Les exemples de scripts se trouvent sur [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="Bande passante MPI sur Azure HB.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

Le [package Mellanox Peretpt](https://community.mellanox.com/s/article/perftest-package) comporte de nombreux tests InfiniBand tels que la latence (ib_send_lat) et la bande passante (ib_send_bw). Vous trouverez ci-dessous un exemple de commande.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
