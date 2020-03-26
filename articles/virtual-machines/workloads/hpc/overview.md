---
title: Calcul haute performance sur des machines virtuelles de la série H - Machines virtuelles Azure
description: Découvrez les fonctionnalités et les capacités des machines virtuelles de la série H optimisées pour le HPC (High-Performance Computing, Calcul haute performance).
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271018"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Calcul haute performance sur des machines virtuelles de la série H

Le calcul haute performance (HPC) sur les machines virtuelles des séries HB et HC permet les performances HPC les plus optimisées parmi toutes les machines virtuelles sur Azure. Les machines virtuelles optimisées pour le HPC sont utilisées pour résoudre certains des problèmes mathématiques les plus complexes, comme la dynamique des fluides, les simulations relatives aux hydrocarbures et la modélisation météorologique.

Cet article traite de certaines fonctionnalités clés des machines virtuelles des séries HB et HC, explique pourquoi ces machines virtuelles conviennent bien aux scénarios HPC et comment bien démarrer.

## <a name="features-and-capabilities"></a>Fonctionnalités et capacités

Les machines virtuelles des série HB et HC sont conçues pour fournir les meilleures performances HPC, une scalabilité de l’interface MPI (Message Passing Interface, Interface de transmission de messages) et un bon rapport coût-efficacité pour les charges de travail HPC.

### <a name="message-passing-interface"></a>Interface de transmission de messages (MPI)

Les séries HB et HC prennent en charge presque tous les types et les versions de MPI. Voici certains des types de MPI les plus courants pris en charge : OpenMPI, MVAPICH2, Plateform MPI, Intel MPI et tous les verbes d’accès direct à la mémoire à distance (RDMA, Remote Direct Memory Access). Pour plus d’informations, consultez [Configurer l’interface de transmission de messages pour HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA et InfiniBand

L’interface RDMA est standard sur les machines virtuelles des séries HB et HC. Les instances compatibles RDMA communiquent sur un réseau InfiniBand, fonctionnant à des débits de données améliorés pour les machines virtuelles des séries HB et HC. Les instances compatibles RDMA peuvent améliorer la scalabilité et les performances de certaines applications MPI.

La configuration d’InfiniBand prenant en charge les machines virtuelles des séries HB et HC sont réseaux fat-tree avec une conception à faible diamètre pour des performances RDMA cohérentes.

Pour plus d’informations sur la configuration d’InfiniBand sur vos machines virtuelles des séries HB et HC, consultez [Activer InfiniBand](enable-infiniband.md).

## <a name="get-started"></a>Bien démarrer

Décidez d’abord quelle machine virtuelle de la série H vous allez utiliser. Pour plus d’informations sur les machines virtuelles optimisées pour HPC, consultez [Vue d’ensemble de la série HB](hb-series-overview.md) et [Vue d’ensemble de la série HC](hc-series-overview.md). Pour les spécifications, consultez [Tailles des machines virtuelles de calcul haute performance](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Une fois que vous avez sélectionné et créé une machine virtuelle pour votre application, vous devez la configurer en activant InfiniBand. Pour découvrir comment activer InfiniBand sur les machines virtuelles Windows et Linux, consultez [Activer InfiniBand](enable-infiniband.md).

MPI est un composant critique des charges de travail HPC. Les séries HB et HC prennent en charge presque tous les types et les versions de MPI. Pour plus d’informations, consultez [Configurer l’interface de transmission de messages pour HPC](setup-mpi.md).

Une fois que vous avez choisi la série de votre machine virtuelle, configurez Infiniband et MPI : vous êtes alors prêt à commencer à créer vos charges de travail HPC.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les spécifications et les principales différences, consultez [Vue d’ensemble de la série HB](hb-series-overview.md) et [Vue d’ensemble de la série HC](hc-series-overview.md).

- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
