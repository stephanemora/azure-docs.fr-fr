---
title: Tailles des machines virtuelles Windows Azure - HPC | Microsoft Docs
description: Répertorie les différentes tailles disponibles pour les machines virtuelles de calcul haute performance Windows dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck;amverma
ms.openlocfilehash: ad490084b34a8bf6e89c7feb14d5cd2e70a8138f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755324"
---
# <a name="high-performance-compute-vm-sizes"></a>Tailles de machines virtuelles de calcul haute performance

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Système d’exploitation** : Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **MPI** : Microsoft MPI (MS-MPI) 2012 R2 ou version ultérieure, bibliothèque Intel MPI 5.x

  Sur SR-IOV des machines virtuelles est activées, les implémentations MPI prises en charge utilisent l’interface Microsoft Network Direct (ND) pour communiquer entre les instances. SR-IOV activés tailles de machine virtuelle (chaud et HC-series) sur Azure autorisant presque n’importe quelle version de MPI pour être utilisé avec Mellanox OFED. 

* **Extension VM de InfiniBandDriverWindows** - sur les machines virtuelles prenant en charge RDMA, ajoutez l’extension InfiniBandDriverWindows pour activer InfiniBand. Cette extension de machine virtuelle Windows installe des pilotes Windows Network Direct (sur des machines virtuelles de SR-IOV) ou les pilotes Mellanox OFED (sur des machines virtuelles de SR-IOV) pour la connectivité RDMA.
Dans certains déploiements des instances A8 et A9, l’extension HpcVmDrivers est ajoutée automatiquement. Notez que l’extension HpcVmDrivers VM est déconseillée ; Il ne sera pas mis à jour. Pour ajouter l’extension de machine virtuelle sur une machine virtuelle, vous pouvez utiliser les cmdlets [Azure PowerShell](/powershell/azure/overview). 

  La commande suivante installe la dernière extension InfiniBandDriverWindows version 1.0 sur une machine virtuelle existante prenant en charge RDMA nommée *myVM* déployé dans le groupe de ressources nommé *myResourceGroup* dans le  *Ouest des États-Unis* région :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Vous pouvez également les extensions de machine virtuelle peuvent être incluses dans les modèles Azure Resource Manager pour faciliter le déploiement, avec l’élément JSON suivant :
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Pour plus d’informations, consultez [Extensions et fonctionnalités de la machine virtuelle](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Vous pouvez également utiliser les extensions pour les machines virtuelles déployées dans le [modèle de déploiement classique](classic/manage-extensions.md).

* **Espace d’adressage réseau RDMA** : le réseau RDMA dans Azure réserve l’espace d’adressage 172.16.0.0/16. Si vous exécutez des applications MPI sur des instances déployées dans un réseau virtuel Azure, assurez-vous que l’espace d’adressage du réseau virtuel ne chevauche pas le réseau RDMA.


### <a name="cluster-configuration-options"></a>Options de configuration de cluster

Azure fournit plusieurs options pour créer des clusters de machines virtuelles HPC Windows pouvant communiquer via le réseau RDMA, notamment : 

* **Machines virtuelles** : Déployez les machines virtuelles HPC compatibles RDMA dans le même groupe à haute disponibilité (quand vous utilisez le modèle de déploiement Azure Resource Manager). Si vous utilisez le modèle de déploiement classique, déployez les machines virtuelles dans le même service cloud. 

* **Machines virtuelles identiques** : dans une échelle de machine virtuelle définie, assurez-vous de limiter le déploiement à un seul groupe de placement. Par exemple, dans un modèle Resource Manager, définissez la propriété `singlePlacementGroup` avec la valeur `true`. 

* **Azure CycleCloud** : Créez un cluster HPC dans [Azure CycleCloud](/azure/cyclecloud/) pour exécuter des travaux MPI sur des nœuds Windows.

* **Azure Batch** : Créez un pool [Azure Batch](/azure/batch/) pour exécuter des charges de travail MPI sur des nœuds de calcul Windows Server. Pour plus d’informations, consultez [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](../../batch/batch-pool-compute-intensive-sizes.md). Consultez également le projet [Batch Shipyard](https://github.com/Azure/batch-shipyard) pour l’exécution de charges de travail basées sur des conteneurs dans Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) comprend un environnement d’exécution pour MS-MPI qui utilise le réseau RDMA Azure en cas de déploiement sur des machines virtuelles Windows compatibles RDMA. Pour des exemples de déploiement, consultez [Configuration d'un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Autres tailles
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](../virtual-machines-windows-sizes-memory.md)
- [Optimisé pour le stockage](../virtual-machines-windows-sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir les listes de vérification à mettre en œuvre afin d’utiliser les instances nécessitant beaucoup de ressources système avec HPC Pack sur Windows Server, consultez [Configuration d’un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Pour utiliser des instances nécessitant beaucoup de ressources système lors de l’exécution d’applications MPI avec Azure Batch, consultez [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../../batch/batch-mpi.md).

- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
