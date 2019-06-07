---
title: Tailles des machines virtuelles Linux Azure - Calcul haute performance | Microsoft Docs
description: Répertorie les différentes tailles disponibles pour les machines virtuelles de calcul haute performance Linux dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 32b0f467f11cf8cb0a04657006cb5a86b11e27e9
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755170"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tailles de machine virtuelle de calcul haute performance

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Le tailles de machine virtuelle est activées sur Azure autoriser presque toute version de MPI à utiliser SR-IOV.
Sur SR-IOV des machines virtuelles est activées, seules les versions Intel MPI 5.x sont prises en charge. Versions ultérieures (2017, 2018) du runtime Intel MPI library peut ou ne peut pas être compatible avec les pilotes Azure Linux RDMA.


### <a name="supported-os-images"></a>Images de système d’exploitation prises en charge
 
La place de marché Azure présente de nombreuses distributions Linux qui prennent en charge la connectivité RDMA :
  
* **HPC basé sur centOS** - pour les non-SR-IOV activé des machines virtuelles, basé sur CentOS version 6.5 HPC ou une version ultérieure, jusqu'à 7.5 conviennent. Pour les machines virtuelles de série H, versions 7.1 à 7.5 sont recommandées. Les pilotes RDMA et Intel MPI 5.1 sont installés sur la machine virtuelle.
  Pour les machines virtuelles SR-IOV, CentOS-HPC 7.6 est optimisé et préchargé avec les pilotes RDMA et divers packages MPI installés.
  Pour les autres images de machine virtuelle RHEL/CentOS, ajoutez l’extension de InfiniBandLinux pour activer InfiniBand. Cette extension Linux VM installe les pilotes Mellanox OFED (sur des machines virtuelles de SR-IOV) pour la connectivité RDMA. L’applet de commande PowerShell suivante installe la dernière version (version 1.0) de l’extension InfiniBandDriverLinux sur une machine virtuelle prenant en charge RDMA existante. La machine virtuelle prenant en charge RDMA est nommée *myVM* et est déployé dans le groupe de ressources nommé *myResourceGroup* dans le *ouest des États-Unis* région comme suit :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Vous pouvez également les extensions de machine virtuelle peuvent être incluses dans les modèles Azure Resource Manager pour faciliter le déploiement avec l’élément JSON suivant :
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
 
  > [!NOTE]
  > Sur les images HPC basées sur CentOS, les mises à jour du noyau sont désactivées dans le fichier de configuration **yum** . Il s’agit, car les pilotes RDMA Linux sont distribués dans un package RPM et les mises à jour de pilote peuvent ne pas fonctionnent si le noyau est mis à jour.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 pour HPC, SLES 12 SP3 pour HPC (Premium), SLES 12 SP1 pour HPC, SLES 12 SP1 pour HPC (Premium), SLES 12 SP4 et SLES 15. Les pilotes RDMA sont installés et les packages Intel MPI sont distribués sur la machine virtuelle. Installez MPI en exécutant la commande suivante :

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -Ubuntu Server 16.04 LTS, 18.04 LTS. Configurez les pilotes RDMA sur la machine virtuelle et inscrivez-vous auprès d’Intel pour télécharger Intel MPI :

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Pour plus d’informations sur l’activation InfiniBand, configuration de MPI, consultez [InfiniBand activer](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband-with-sriov).


### <a name="cluster-configuration-options"></a>Options de configuration de cluster

Azure fournit plusieurs options pour créer des clusters de machines virtuelles HPC Linux qui peuvent communiquer via le réseau RDMA, notamment : 

* **Machines virtuelles** : Déployez les machines virtuelles HPC compatibles RDMA dans le même groupe à haute disponibilité (quand vous utilisez le modèle de déploiement Azure Resource Manager). Si vous utilisez le modèle de déploiement classique, déployez les machines virtuelles dans le même service cloud. 

* **Machines virtuelles identiques** : dans une échelle de machine virtuelle définie, assurez-vous de limiter le déploiement à un seul groupe de placement. Par exemple, dans un modèle Resource Manager, définissez la propriété `singlePlacementGroup` avec la valeur `true`. 

* **Azure CycleCloud** : Créez un cluster HPC dans [Azure CycleCloud](/azure/cyclecloud/) pour exécuter des travaux MPI sur des nœuds Linux.

* **Azure Batch** : Créez un pool [Azure Batch](/azure/batch/) pour exécuter des charges de travail MPI sur des nœuds de calcul Linux. Pour plus d’informations, consultez [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](../../batch/batch-pool-compute-intensive-sizes.md). Consultez également le projet [Batch Shipyard](https://github.com/Azure/batch-shipyard) pour l’exécution de charges de travail basées sur des conteneurs sur Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) prend en charge l’exécution de plusieurs distributions Linux sur les nœuds de calcul déployés dans des machines virtuelles Azure compatibles RDMA et gérés par un nœud principal Windows Server. Pour un exemple de déploiement, consultez [Créer un cluster RDMA HPC Pack Linux dans Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Considérations relatives au réseau
* Sur non-SR-IOV, compatible RDMA des machines virtuelles Linux dans Azure, eth1 est réservé au trafic réseau RDMA. Ne modifiez pas aucun paramètre eth1 ou toutes les informations dans le fichier de configuration qui fait référence à ce réseau.
* Sur SR-IOV est activé (chaud et HC-series) des machines virtuelles, ib0 est réservé au trafic réseau RDMA.
* Le réseau RDMA dans Azure réserve l'espace d’adressage 172.16.0.0/16. Si vous exécutez des applications MPI sur des instances déployées dans un réseau virtuel Azure, assurez-vous que l’espace d’adressage du réseau virtuel ne chevauche pas le réseau RDMA.
* Selon l’outil de gestion de clusters que vous choisissez, une configuration système supplémentaire peut être nécessaire pour exécuter des travaux MPI. Par exemple, sur un cluster de machines virtuelles, vous devez établir une approbation entre les nœuds de cluster en générer des clés SSH ou en établissant des connexions SSH sans mot de passe.


## <a name="other-sizes"></a>Autres tailles
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur comment configurer, optimiser et mettre à l’échelle [charges de travail HPC](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc) sur Azure.
- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
