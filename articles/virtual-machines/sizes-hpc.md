---
title: Tailles des machines virtuelles Azure – HPC | Microsoft Docs
description: Répertorie les différentes tailles disponibles pour les machines virtuelles de calcul haute performance dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: dc2086223dea9bff311aac9e7d4771b5273f0e91
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492550"
---
# <a name="high-performance-compute-vm-sizes"></a>Tailles de machines virtuelles de calcul haute performance

Les machines virtuelles Azure de la série H sont conçues pour offrir des performances, une extensibilité MPI et une rentabilité exceptionnelles adaptées à un vaste éventail de charges de travail HPC réelles.

Les machines virtuelles de la [série HBv2](hbv2-series.md) disposent d’un bus InfiniBand Mellanox HDR à 200 Gb/s, tandis que les machines virtuelles des séries HB et HC disposent d’un bus InfiniBand Mellanox EDR à 100 Gb/s. Chacun de ces types de machine virtuelle est connecté dans une arborescence FAT non bloquante pour des performances RDMA optimisées et cohérentes. Les machines virtuelles HBv2 prennent en charge le routage adaptatif et le transport connecté dynamique (DCT, en plus des transports RC et UD standards). Ces fonctionnalités améliorent les performances, l’extensibilité et la cohérence des applications, et leur utilisation est fortement recommandée.

Les machines virtuelles de la [série HB](hb-series.md) sont optimisées pour des applications tributaires de la bande passante mémoire, par exemple la dynamique des fluides, l’analyse explicite par éléments finis et la modélisation du climat. Les machines virtuelles HB disposent de 60 cœurs de processeur AMD EPYC 7551, de 4 Go de RAM par cœur de processeur, et d’aucun hyperthreading. La plateforme AMD EPYC fournit plus de 260 Go/s de bande passante mémoire.

Les machines virtuelles de la [série HC](hc-series.md) sont optimisées pour les applications tributaires d’un calcul dense, telles que l’analyse implicite par éléments finis, la dynamique moléculaire et la chimie numérique. Les machines virtuelles HC disposent de 44 cœurs de processeur Intel Xeon Platinum 8168, de 8 Go de RAM par cœur de processeur, et d’aucun hyperthreading. La plateforme Intel Xeon Platinum prend en charge le riche écosystème d’outils logiciels d’Intel, comme Intel Math Kernel Library.

Les machines virtuelles de la [série H](h-series.md) sont optimisées pour les applications tributaires de fréquences de processeur élevées ou ayant des besoins en mémoire importants par cœur. Les machines virtuelles de la série H disposent de 8 ou 16 cœurs de processeur Intel Xeon E5 2667 v3, de 7 ou 14 Go de RAM par cœur de processeur, et d’aucun hyperthreading. Les machines virtuelles de la série H disposent d’un Infiniband FDR Mellanox de 56 Go/s dans une configuration de gros arbres non bloquant pour des performances RDMA homogènes. Les machines virtuelles de la série H prennent en charge Intel MPI 5.x et MS-MPI.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

- **Abonnement Azure** : pour déployer un plus grand nombre d’instances de calcul intensif, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Si vous utilisez un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez seulement utiliser un nombre limité de cœurs de calcul Azure.

- **Tarification et disponibilité** : ces tailles de machines virtuelles sont proposées uniquement au niveau tarifaire Standard. Pour connaître la disponibilité dans les différentes régions Azure, voir [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/) .
- **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs dans votre abonnement Azure à partir de la valeur par défaut. Votre abonnement peut également limiter le nombre de cœurs, que vous pouvez déployer dans certaines familles de taille de machine virtuelle, dont la série H. Pour demander une augmentation de quota, [ouvrez une demande de service clientèle en ligne](../azure-supportability/how-to-create-azure-support-request.md) gratuitement. (Les limites par défaut peuvent varier en fonction de la catégorie de votre abonnement.)

  > [!NOTE]
  > Si vous avez des besoins de capacité à grande échelle, contactez le support Azure. Les quotas d’Azure sont des limites de crédit et non des garanties de capacité. Quel que soit votre quota, vous êtes facturé uniquement pour les cœurs que vous utilisez.
  
- **Réseau virtuel** : un [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) Azure n’est pas requis pour utiliser les instances qui nécessitent beaucoup de ressources système. Cependant, pour bon nombre de scénarios de déploiement, vous avez besoin d’au moins un réseau virtuel Azure cloud ou d’une connexion de site à site si vous devez accéder à des ressources locales. Si nécessaire, créez un réseau virtuel avant de déployer les instances. L’ajout de machines virtuelles nécessitant beaucoup de ressources système à un réseau virtuel dans un groupe d’affinités n’est pas pris en charge.
- **Redimensionnement** : en raison de leur matériel spécialisé, vous pouvez uniquement redimensionner les instances nécessitant beaucoup de ressources système qui appartiennent à la même famille de taille (série H ou série A nécessitant beaucoup de ressources système). Par exemple, vous pouvez redimensionner une machine virtuelle de la série H uniquement d’une seule taille en une autre de cette même série. Le redimensionnement d’une taille ne nécessitant pas beaucoup de ressources système en une taille nécessitant beaucoup de ressources système n’est pas pris en charge.  
## <a name="rdma-capable-instances"></a>Instances prenant en charge RDMA

Un sous-ensemble d’instances nécessitant beaucoup de ressources système (A8, A9, H16r et H16mr) offre une interface réseau pour la connectivité par accès direct à la mémoire à distance (RDMA). Les tailles de la série N désignées par « r », telles que les configurations NC24rs (NC24rs_v2 et NC24rs_v3) sont également compatibles RDMA. Cette interface s’ajoute à l’interface réseau Azure standard disponible pour d’autres tailles de machine virtuelle.

Cette interface permet aux instances prenant en charge l’accès RDMA de communiquer sur un réseau InfiniBand (IB), opérant à des vitesses EDR pour les machines virtuelles HB, HC, à des vitesses FDR pour les machines virtuelles de la série N H16r, H16mr, compatibles RDMA, et à des vitesses QDR pour les machines virtuelles A8 et A9. Ces fonctionnalités RDMA peuvent améliorer l’extensibilité et les performances de certaines applications MPI (Message Passing Interface). Pour plus d’informations sur la vitesse, consultez les détails dans les tables sur cette page.

> [!NOTE]
> Dans Azure, la fonction IP over IB est uniquement prise en charge sur les machines virtuelles compatibles SR-IOV (SR-IOV pour InfiniBand, actuellement HB et HC). RDMA over IB est pris en charge pour toutes les instances compatibles RDMA.
- **Système d’exploitation** : Windows Server 2016 sur toutes les machines virtuelles de la série de calcul haute performance (HPC) ci-dessus. Windows Server 2012 R2, Windows Server 2012 sont également pris en charge sur les machines virtuelles non compatibles SR-IOV (excluant HB et HC).

- **MPI** : les tailles des machines virtuelles SR-IOV activées sur Azure (HB, HC) permettent d’utiliser presque tous les qualificateurs de MPI avec Mellanox OFED.
Sur les machines virtuelles non compatibles SR-IOV, les implémentations MPI prises en charge utilisent l’interface Microsoft Network Direct (ND) pour la communication entre les instances. Par conséquent, seules les versions Microsoft MPI (MS-MPI) 2012 R2 ou ultérieures et Intel MPI 5.x sont supportées. Les versions ultérieures (2017, 2018) de la bibliothèque runtime MPI Intel peuvent ne pas être compatibles avec les pilotes Azure RDMA.

- **Extension de machine virtuelle InfiniBandDriverWindows** : sur les machines virtuelles compatibles RDMA, ajoutez l’extension InfiniBandDriverWindows pour activer InfiniBand. Cette extension de machine virtuelle Windows installe des pilotes Windows Network Direct (sur des machines virtuelles non compatibles SR-IOV) ou des pilotes Mellanox OFED (sur des machines virtuelles compatibles SR-IOV) pour la connectivité RDMA.
Dans certains déploiements des instances A8 et A9, l’extension HpcVmDrivers est ajoutée automatiquement. Notez que l’extension de machine virtuelle HpcVmDrivers est déconseillée ; elle ne sera pas mise à jour. Pour ajouter l’extension de machine virtuelle sur une machine virtuelle, vous pouvez utiliser les cmdlets [Azure PowerShell](/powershell/azure/overview). 

  La commande suivante installe la dernière version 1.0 de l’extension InfiniBandDriverWindows sur une machine virtuelle existante prenant en charge RDMA et nommée *myVM* déployée dans le groupe de ressources nommé *myResourceGroup* dans la région *USA Ouest* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Les extensions de machine virtuelle peuvent également être incluses dans les modèles Azure Resource Manager pour faciliter le déploiement, avec l’élément JSON suivant :

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  La commande suivante installe la dernière version 1.0 de l’extension InfiniBandDriverWindows sur toutes les machines virtuelles prenant en charge RDMA qui existent dans un groupe de machines virtuelles identique nommé *myVMSS* et déployées dans le groupe de ressources nommé *myResourceGroup* :

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Pour plus d’informations, consultez [Extensions et fonctionnalités de la machine virtuelle](/extensions/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vous pouvez également utiliser les extensions pour les machines virtuelles déployées dans le [modèle de déploiement classique](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **Espace d’adressage réseau RDMA** : le réseau RDMA dans Azure réserve l’espace d’adressage 172.16.0.0/16. Si vous exécutez des applications MPI sur des instances déployées dans un réseau virtuel Azure, assurez-vous que l’espace d’adressage du réseau virtuel ne chevauche pas le réseau RDMA.

## <a name="cluster-configuration-options"></a>Options de configuration de cluster

Azure fournit plusieurs options pour créer des clusters de machines virtuelles HPC Windows pouvant communiquer via le réseau RDMA, notamment : 

- **Machines virtuelles** : Déployez les machines virtuelles HPC compatibles RDMA dans le même groupe à haute disponibilité (quand vous utilisez le modèle de déploiement Azure Resource Manager). Si vous utilisez le modèle de déploiement classique, déployez les machines virtuelles dans le même service cloud. 

- **Groupes de machines virtuelles identiques** : dans un groupe de machines virtuelles identiques, veillez à limiter le déploiement à un seul groupe de placements. Par exemple, dans un modèle Resource Manager, définissez la propriété `singlePlacementGroup` avec la valeur `true`. 

- **MPI entre les machines virtuelles** : si la communication MPI est nécessaire entre les machines virtuelles (VM), assurez-vous que les machines virtuelles sont dans le même groupe à haute disponibilité ou le même groupe identique de machines virtuelles.

- **Azure CycleCloud** : Créez un cluster HPC dans [Azure CycleCloud](/azure/cyclecloud/) pour exécuter des travaux MPI sur des nœuds Windows.

- **Azure Batch** : Créez un pool [Azure Batch](/azure/batch/) pour exécuter des charges de travail MPI sur des nœuds de calcul Windows Server. Pour plus d’informations, consultez [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](../batch/batch-pool-compute-intensive-sizes.md). Consultez également le projet [Batch Shipyard](https://github.com/Azure/batch-shipyard) pour l’exécution de charges de travail basées sur des conteneurs dans Batch.

- **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) comprend un environnement d’exécution pour MS-MPI qui utilise le réseau RDMA Azure en cas de déploiement sur des machines virtuelles Linux compatibles RDMA. Pour des exemples de déploiement, voir [Configuration d’un cluster RDMA Linux avec HPC Pack pour exécuter des applications MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir les listes de vérification à mettre en œuvre afin d’utiliser des instances nécessitant beaucoup de ressources système avec HPC Pack sur Windows Server, voir [Configuration d’un cluster RDMA Linux avec HPC Pack pour exécuter des applications MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

- Pour utiliser des instances nécessitant beaucoup de ressources système lors de l’exécution d’applications MPI avec Azure Batch, consultez [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../batch/batch-mpi.md).

- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.