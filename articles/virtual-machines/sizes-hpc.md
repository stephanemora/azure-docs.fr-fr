---
title: Tailles des machines virtuelles Azure – HPC | Microsoft Docs
description: Répertorie les différentes tailles disponibles pour les machines virtuelles de calcul haute performance dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772431"
---
# <a name="high-performance-computing-vm-sizes"></a>Tailles des machines virtuelles de calcul haute performance (HPC)

Les machines virtuelles Azure de la série H sont conçues pour offrir des performances, une extensibilité et une rentabilité exceptionnelles adaptées à diverses charges de travail HPC réelles.

Les machines virtuelles de la [série HBv3](hbv3-series.md) sont optimisées pour les applications HPC telles que la dynamique des fluides, l’analyse des éléments finis explicites et implicites, la modélisation de phénomènes météorologiques, le traitement de données sismiques, la simulation de réservoir et la simulation RTL. Les machines virtuelles HBv3 offrent jusqu’à 120 cœurs de processeurs AMD EPYC™ 7003 (Milan), 448 Go de RAM et aucun hyperthreading. Les machines virtuelles de la série HBv3 offrent également une bande passante mémoire de 350 Go/sec, jusqu’à 32 Mo de cache L3 par cœur, des performances de disque SSD allant jusqu’à 7 Go/s et des fréquences d’horloge allant jusqu’à 3,675 GHz. 

Toutes les machines virtuelles de la série HBv3 sont équipées d’InfiniBand HDR à 200 Gbit/s des solutions de mise en réseau NVIDIA pour permettre des charges de travail MPI à l’échelle d’un superordinateur. Ces machines virtuelles sont connectées dans une arborescence FAT non bloquante pour des performances RDMA optimisées et cohérentes. L’architecture InfiniBand HDR prend également en charge le routage adaptatif et le transport connecté dynamique (DCT, en plus des transports RC et UD standards). Ces fonctionnalités améliorent les performances, la scalabilité et la cohérence des applications, et leur utilisation est vivement recommandée.

Les machines virtuelles de la [série HBv2](hbv2-series.md) sont optimisées pour des applications tributaires de la bande passante mémoire, par exemple la dynamique des fluides, l’analyse par éléments finis et la simulation de réservoir. Les machines virtuelles HBv2 disposent de 120 cœurs de processeur AMD EPYC 7742, de 4 Go de RAM par cœur de processeur, et d’aucun multithreading simultané. Chaque machine virtuelle HBv2 fournit jusqu’à 340 Go/s de bande passante de mémoire et jusqu’à 4 téraflops de calcul FP64.

Les machines virtuelles de la série HBv2 disposent d’un bus InfiniBand Mellanox HDR à 200 Gb/s, tandis que les machines virtuelles des séries HB et HC disposent d’un bus InfiniBand Mellanox EDR à 100 Gb/s. Tous ces types de machines virtuelles sont connectés dans une arborescence FAT non bloquante pour des performances RDMA optimisées et cohérentes. Les machines virtuelles HBv2 prennent en charge le routage adaptatif et le transport connecté dynamique (DCT, en plus des transports RC et UD standard). Ces fonctionnalités améliorent les performances, la scalabilité et la cohérence des applications, et leur utilisation est vivement recommandée.

Les machines virtuelles de la [série HB](hb-series.md) sont optimisées pour des applications tributaires de la bande passante mémoire, par exemple la dynamique des fluides, l’analyse explicite par éléments finis et la modélisation du climat. Les machines virtuelles HB disposent de 60 cœurs de processeur AMD EPYC 7551, de 4 Go de RAM par cœur de processeur, et d’aucun hyperthreading. La plateforme AMD EPYC fournit plus de 260 Go/s de bande passante mémoire.

Les machines virtuelles de la [série HC](hc-series.md) sont optimisées pour les applications tributaires d’un calcul dense, telles que l’analyse implicite par éléments finis, la dynamique moléculaire et la chimie numérique. Les machines virtuelles HC disposent de 44 cœurs de processeur Intel Xeon Platinum 8168, de 8 Go de RAM par cœur de processeur, et d’aucun hyperthreading. La plateforme Intel Xeon Platinum prend en charge le riche écosystème d’outils logiciels d’Intel, comme Intel Math Kernel Library.

Les machines virtuelles de la [série H](h-series.md) sont optimisées pour les applications tributaires de fréquences de processeur élevées ou ayant des besoins en mémoire importants par cœur. Les machines virtuelles de la série H disposent de 8 ou 16 cœurs de processeur Intel Xeon E5 2667 v3, de 7 ou 14 Go de RAM par cœur de processeur, et d’aucun hyperthreading. Les machines virtuelles de la série H disposent d’un Infiniband FDR Mellanox de 56 Go/s dans une configuration de gros arbres non bloquant pour des performances RDMA homogènes. Les machines virtuelles de la série H prennent en charge Intel MPI 5.x et MS-MPI.

> [!NOTE]
> Toutes les machines virtuelles des séries HBv3, HBv2, HB et HC ont un accès exclusif aux serveurs physiques. Il n’y a qu’une seule machine virtuelle par serveur physique et aucune multilocation partagée avec d’autres machines virtuelles pour ces tailles de machine virtuelle.

> [!NOTE]
> Les [machines virtuelles A8 à A11](./sizes-previous-gen.md#a-series---compute-intensive-instances) ont été mises hors service en mars 2021. Aucun nouveau déploiement de machine virtuelle de ces tailles n’est possible. Si vous avez des machines virtuelles existantes, reportez-vous aux notifications par e-mail pour les étapes suivantes, dont la migration vers d’autres tailles de machine virtuelle dans le [Guide de migration HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Instances prenant en charge RDMA

La plupart des tailles de machines virtuelles HPC offrent une interface réseau pour la connectivité par accès direct à la mémoire à distance (RDMA). Les tailles sélectionnées de la [série N](./nc-series.md) désignées par « r » prennent également en charge la fonctionnalité RDMA. Cette interface s’ajoute à l’interface réseau Ethernet Azure standard disponible dans d’autres tailles de machine virtuelle.

Cette interface secondaire permet aux instances compatibles RDMA de communiquer sur un réseau InfiniBand (IB), opérant à des vitesses HDR pour les machines virtuelles HBv3 et HBv2, EDR pour les machines virtuelles HB, HC et NDv2, FDR pour les machines virtuelles H16r et H16mr, et autres machines virtuelles de la série N compatibles RDMA. Ces fonctionnalités RDMA peuvent améliorer la scalabilité et les performances des applications basées sur une interface de passage de messages (Message Passing Interface, MPI).

> [!NOTE]
> **Prise en charge de SR-IOV** : sans Azure HPC, il existe actuellement deux classes de machines virtuelles, selon qu’elles sont ou non compatibles avec SR-IOV pour InfiniBand. À l’heure actuelle, presque toutes les machines virtuelles les plus récentes prenant en charge RDMA ou InfiniBand sur Azure sont compatibles avec SR-IOV, à l’exception de H16r, H16mr et NC24r.
> RDMA est activé uniquement sur le réseau InfiniBand (IB) et est pris en charge pour toutes les machines virtuelles compatibles RDMA.
> IP over IB est uniquement pris en charge sur les machines virtuelles compatibles SR-IOV.
> RDMA n’est pas activé sur le réseau Ethernet.

- **Système d’exploitation** : des distributions Linux telles que CentOS, RHEL, Ubuntu et SUSE sont couramment utilisées. Windows Server 2016 et versions ultérieures sont pris en charge sur toutes les machines virtuelles de la série HPC. Windows Server 2012 R2 et Windows Server 2012 sont également pris en charge sur les machines virtuelles non compatibles SR-IOV. Notez que [Windows Server 2012 R2 n’est pas pris en charge sur les HBv2 et autres machines virtuelles dotées de plus de 64 cœurs (virtuels ou physiques)](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Pour obtenir la liste des images de machines virtuelles prises en charge sur la place de marché et savoir comment les configurer de manière appropriée, consultez [Images de machine virtuelle](./workloads/hpc/configure.md). Les pages de taille de machine virtuelle respectives affichent également la prise en charge de la pile logicielle.

- **InfiniBand et pilotes** : sur les machines virtuelles prenant en charge InfiniBand, certains pilotes sont requis pour activer la fonctionnalité RDMA. Pour obtenir la liste des images de machines virtuelles prises en charge sur la place de marché et savoir comment les configurer de manière appropriée, consultez [Images de machine virtuelle](./workloads/hpc/configure.md). Consultez également [Activation d’InfiniBand](./workloads/hpc/enable-infiniband.md) pour découvrir les extensions de machine virtuelle ou l’installation manuelle des pilotes InfiniBand.

- **MPI** : les tailles des machines virtuelles SR-IOV activées sur Azure permettent d’utiliser presque tous les qualificateurs de MPI avec Mellanox OFED. Sur les machines virtuelles non compatibles SR-IOV, les implémentations MPI prises en charge utilisent l’interface Microsoft Network Direct (ND) pour la communication entre les machines virtuelles. Par conséquent, seules les versions Intel MPI 5.x et Microsoft MPI (MS-MPI) 2012 R2 ou ultérieures sont prises en charge. Les versions ultérieures de la bibliothèque runtime MPI Intel peuvent ne pas être compatibles avec les pilotes Azure RDMA. Pour plus d’informations sur la configuration de l’interface de passage de messages sur des machines virtuelles HPC sur Azure, consultez [Configurer MPI pour HPC](./workloads/hpc/setup-mpi.md).

  > [!NOTE]
  > **Espace d'adressage réseau RDMA** : le réseau RDMA dans Azure réserve l'espace d’adressage 172.16.0.0/16. Si vous exécutez des applications MPI sur des instances déployées dans un réseau virtuel Azure, assurez-vous que l’espace d’adressage du réseau virtuel ne chevauche pas le réseau RDMA.

## <a name="cluster-configuration-options"></a>Options de configuration de cluster

Azure fournit plusieurs options pour créer des clusters de machines virtuelles HPC pouvant communiquer via le réseau RDMA, notamment : 

- **Machines virtuelles** : déployez les machines virtuelles HPC compatibles RDMA dans le même groupe de machines virtuelles identiques ou groupe à haute disponibilité (quand vous utilisez le modèle de déploiement Azure Resource Manager). Si vous utilisez le modèle de déploiement classique, déployez les machines virtuelles dans le même service cloud.

- **Groupes de machines virtuelles identiques** : dans un groupe de machines virtuelles identiques, veillez à limiter le déploiement à un seul groupe de placements pour la communication InfiniBand au sein du groupe identique. Par exemple, dans un modèle Resource Manager, définissez la propriété `singlePlacementGroup` avec la valeur `true`. Notez que la taille d’un groupe identique qui peut être lancé avec `singlePlacementGroup=true` est limitée par défaut à 100 machines virtuelles. Si vos besoins de mise à l’échelle des travaux HPC sont supérieurs à 100 machines virtuelles dans un seul locataire, vous pouvez demander une augmentation : [ouvrez une demande de support client en ligne](../azure-portal/supportability/how-to-create-azure-support-request.md) gratuitement. La limite du nombre de machines virtuelles dans un seul groupe identique peut être portée à 300. Notez que quand vous déployez des machines virtuelles à l’aide de groupes à haute disponibilité, la limite maximale est de 200 machines virtuelles par groupe à haute disponibilité.

  > [!NOTE]
  > **MPI entre les machines virtuelles** : si RDMA (par exemple en utilisant la communication MPI) est nécessaire entre les machines virtuelles, assurez-vous que les machines virtuelles figurent dans le même groupe de machines virtuelles identiques ou groupe à haute disponibilité.

- **Azure CycleCloud** : Créez un cluster HPC utilisant [Azure CycleCloud](/azure/cyclecloud/) pour exécuter des travaux MPI.

- **Azure Batch** : Créez un pool [Azure Batch](../batch/index.yml) pour exécuter des charges de travail MPI. Pour utiliser des instances nécessitant beaucoup de ressources système lors de l’exécution d’applications MPI avec Azure Batch, consultez [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../batch/batch-mpi.md).

- **Microsoft HPC Pack** - [HPC Pack](/powershell/high-performance-computing/overview) comprend un environnement d’exécution pour MS-MPI qui utilise le réseau RDMA Azure en cas de déploiement sur des machines virtuelles Linux compatibles RDMA. Pour des exemples de déploiement, voir [Configuration d’un cluster RDMA Linux avec HPC Pack pour exécuter des applications MPI](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

- **Abonnement Azure** : pour déployer un plus grand nombre d’instances de calcul intensif, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Si vous utilisez un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez seulement utiliser un nombre limité de cœurs de calcul Azure.

- **Tarification et disponibilité** : vérifiez la [tarification](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et la [disponibilité](https://azure.microsoft.com/global-infrastructure/services/) des machines virtuelles par région Azure.

- **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs dans votre abonnement Azure à partir de la valeur par défaut. Votre abonnement peut également limiter le nombre de cœurs, que vous pouvez déployer dans certaines familles de taille de machine virtuelle, dont la série H. Pour demander une augmentation de quota, [ouvrez une demande de service clientèle en ligne](../azure-portal/supportability/how-to-create-azure-support-request.md) gratuitement. (Les limites par défaut peuvent varier en fonction de la catégorie de votre abonnement.)

  > [!NOTE]
  > Si vous avez des besoins de capacité à grande échelle, contactez le support Azure. Les quotas d’Azure sont des limites de crédit et non des garanties de capacité. Quel que soit votre quota, vous êtes facturé uniquement pour les cœurs que vous utilisez.
  
- **Réseau virtuel** : un [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) Azure n’est pas requis pour utiliser les instances qui nécessitent beaucoup de ressources système. Cependant, pour bon nombre de scénarios de déploiement, vous avez besoin d’au moins un réseau virtuel Azure cloud ou d’une connexion de site à site si vous devez accéder à des ressources locales. Si nécessaire, créez un réseau virtuel avant de déployer les instances. L’ajout de machines virtuelles nécessitant beaucoup de ressources système à un réseau virtuel dans un groupe d’affinités n’est pas pris en charge.

- **Redimensionnement** : en raison de leur matériel spécialisé, seules les instances nécessitant beaucoup de ressources système qui appartiennent à la même famille de taille (série H ou N) peuvent être redimensionnées. Par exemple, vous pouvez redimensionner une machine virtuelle de la série H uniquement d’une seule taille en une autre de cette même série. Des considérations supplémentaires concernant la prise en charge des pilotes InfiniBand et les disques NVMe peuvent se révéler nécessaires pour certaines machines virtuelles.


## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [la configuration de vos machines virtuelles](./workloads/hpc/configure.md), [l’activation d’InfiniBand](./workloads/hpc/enable-infiniband.md), [la configuration de MPI](./workloads/hpc/setup-mpi.md) et l’optimisation des applications HPC pour Azure dans l’article relatif aux [charges de travail HPC](./workloads/hpc/overview.md).
- Consultez la [Présentation de la série HBv3](./workloads/hpc/hbv3-series-overview.md) et la [Présentation de la série HC](./workloads/hpc/hc-series-overview.md).
- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
