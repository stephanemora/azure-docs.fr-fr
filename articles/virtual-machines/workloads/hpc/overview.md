---
title: Calcul haute performance sur des machines virtuelles des séries H et N avec InfiniBand - Machines virtuelles Azure
description: Découvrez les fonctionnalités et les capacités des machines virtuelles des séries H et N avec InfiniBand optimisées pour le HPC (High-Performance Computing, Calcul haute performance).
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 04/09/2021
ms.reviewer: cynthn
ms.openlocfilehash: 8017b3e84c80acc71fca1442c4493bd1ec239175
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108141214"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Calcul haute performance sur des machines virtuelles des séries H et N avec InfiniBand

Les machines virtuelles Azure des séries H et N avec InfiniBand sont conçues pour offrir des performances, une scalabilité MPI et une rentabilité exceptionnelles adaptées à un vaste éventail de charges de travail HPC et IA du monde réel. Ces machines virtuelles optimisées pour le calcul hautes performances (HPC) sont utilisées pour résoudre certains des problèmes nécessitant le plus de puissance de calcul dans la science et l’ingénierie, comme la dynamique des fluides, la modélisation terrestre, les simulations météorologiques, etc.

Ces articles décrivent comment bien démarrer avec les machines virtuelles des séries H et N avec InfiniBand sur Azure ainsi que la configuration optimale des charges de travail HPC et IA sur les machines virtuelles pour la scalabilité.

## <a name="features-and-capabilities"></a>Fonctionnalités et capacités

Les machines virtuelles des séries H et N avec InfiniBand sont conçues pour fournir les meilleures performances HPC, une scalabilité MPI et un bon rapport coût-efficacité pour les charges de travail HPC. Pour plus d’informations sur les fonctionnalités et les capacités des machines virtuelles, consultez Machines virtuelles [Série H](../../sizes-hpc.md) et [Série N](../../sizes-gpu.md).

### <a name="rdma-and-infiniband"></a>RDMA et InfiniBand

Les machines virtuelles [Série H](../../sizes-hpc.md) et [Série N](../../sizes-gpu.md) avec [RDMA](../../sizes-hpc.md#rdma-capable-instances) communiquent sur le réseau InfiniBand à faible latence et à bande passante élevée. La fonctionnalité RDMA sur une telle interconnexion est critique pour améliorer la scalabilité et les performances des charges de travail HPC et IA sur les nœuds distribués. Les machines virtuelles des séries H et N avec InfiniBand sont connectées sont connectées dans un réseau fat-tree avec une conception à faible diamètre pour des performances RDMA optimisées et cohérentes.
Pour plus d’informations sur la configuration d’InfiniBand sur les machines virtuelles avec InfiniBand, consultez [Activer InfiniBand](enable-infiniband.md).

### <a name="message-passing-interface"></a>Interface de transmission de messages (MPI)

Les séries H et N avec SR-IOV prennent en charge presque toutes les bibliothèques et versions de MPI. Voici quelques-unes des bibliothèques MPI les plus couramment utilisées : Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, Platform MPI. Tous les verbes d’accès direct à la mémoire à distance (RDMA) sont pris en charge.
Pour plus d’informations sur l’installation des différentes bibliothèques MPI prises en charge et leur configuration optimale, consultez [Configurer MPI](setup-mpi.md).

## <a name="get-started"></a>Bien démarrer

La première étape consiste à sélectionner le type de machine virtuelle [Série H](../../sizes-hpc.md) et [Série N](../../sizes-gpu.md) optimal pour la charge de travail, en fonction des spécifications de la machine virtuelle et des [capacités RDMA](../../sizes-hpc.md#rdma-capable-instances).
Ensuite, configurez la machine virtuelle en activant InfiniBand. Vous pouvez faire cela de différentes façon, notamment en utilisant des images de machine virtuelle optimisées avec des pilotes intégrés. Pour plus d’informations, consultez [Optimisation pour Linux](configure.md) et [Activer InfiniBand](enable-infiniband.md).
Troisièmement, pour les charges de travail des nœud distribués, le choix et la configuration appropriés de MPI sont critiques. Pour plus d’informations, consultez [Configurer MPI](setup-mpi.md).
Quatrièmement, pour les performances et la scalabilité, configurez de façon optimale les charges de travail en suivant les instructions spécifiques à la famille de machines virtuelles, par exemple dans [Vue d’ensemble de la série HBv3](hbv3-series-overview.md) et [Vue d’ensemble de la série HC](hc-series-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus d’informations sur [la configuration et l’optimisation](configure.md) des machines virtuelles [Série H](../../sizes-hpc.md) et [Série N](../../sizes-gpu.md) avec InfiniBand.
- Consultez [Vue d’ensemble de la série HBv3](hb-series-overview.md) et [Vue d’ensemble de la série HC](hc-series-overview.md) afin de découvrir la configuration optimale des charges de travail pour les performances et la scalabilité.
- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Testez vos connaissances avec un [module d’apprentissage sur l’optimisation des applications HPC dans Azure](/learn/modules/optimize-tightly-coupled-hpc-apps/).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).