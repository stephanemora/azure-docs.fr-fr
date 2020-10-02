---
title: Performances des tailles de machine virtuelle de la série HC
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
ms.date: 09/10/2020
ms.author: amverma
ms.openlocfilehash: 0d63d9770dacf6a200e8b81e8d47d9f807a8a448
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603426"
---
# <a name="hc-series-virtual-machine-sizes"></a>Tailles des machines virtuelles de la série HC

Différents tests de performances ont été effectués sur les machines de série HC de différentes tailles. Voici certains des résultats obtenus.

| Charge de travail                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | 190 Go/s (Intel MLC AVX-512)  |
| Linpack hautes performances (HPL)                  | 3520 GigaFLOPS (Rpeak), 2970 GigaFLOPS (Rmax) |
| Latence et bande passante RDMA                        | 1,05 microseconde, 96,8 Gbits/s   |
| FIO sur des disques SSD NVMe locaux                           | 1,3 Go/s de lectures, 900 Mo/s d’écritures |  
| IOR sur 4 disques SSD Azure Premium (disques P30 managés, RAID0)**  | 780 Mo/s de lectures, 780 Mo/s d’écritures |

## <a name="mpi-latency"></a>Latence MPI

Le test de latence MPI de la suite de microbenchmark OSU est exécuté. Les exemples de scripts se trouvent sur [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="Latence MPI sur Azure HC.":::

## <a name="mpi-bandwidth"></a>Bande passante MPI

Le test de bande passante MPI de la suite de microbenchmark OSU est exécuté. Les exemples de scripts se trouvent sur [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="Latence MPI sur Azure HC.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

Le [package Mellanox Peretpt](https://community.mellanox.com/s/article/perftest-package) comporte de nombreux tests InfiniBand tels que la latence (ib_send_lat) et la bande passante (ib_send_bw). Vous trouverez ci-dessous un exemple de commande.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez des informations sur les dernières annonces et des exemples et des résultats de calcul haute performance (HPC) sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
