---
title: Tailles des machines virtuelles Linux Azure - Calcul haute performance | Microsoft Docs
description: Répertorie les différentes tailles disponibles pour les machines virtuelles de calcul haute performance Linux dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
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
ms.openlocfilehash: 847f25d9be1a8654bbc0435d7874acb0ff793304
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695599"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tailles de machine virtuelle de calcul haute performance

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Les tailles de machines virtuelles compatibles SR-IOV sur Azure permettent d’utiliser presque toutes les versions de MPI.
Sur les machines virtuelles qui ne sont pas compatibles avec SR-IOV, seules les versions Intel MPI 5.x sont prises en charge. Les versions ultérieures (2017, 2018) de la bibliothèque runtime MPI Intel peuvent ne pas être compatibles avec les pilotes Azure Linux RDMA.


### <a name="supported-os-images"></a>Images de système d’exploitation prises en charge
 
La Place de marché Azure présente de nombreuses distributions Linux qui prennent en charge la connectivité RDMA :
  
* **HPC basé sur centOS** : pour les machines virtuelles non compatibles SR-IOV, HPC version 6.5 basé sur CentOS ou une version ultérieure, jusqu’à 7.5 convient. Pour les machines virtuelles de série H, les versions 7.1 à 7.5 sont recommandées. Les pilotes RDMA et Intel MPI 5.1 sont installés sur la machine virtuelle.
  Pour les machines virtuelles SR-IOV, les pilotes RDMA sont préchargés et divers packages MPI sont installés sur HPC CentOS 7.6.
  Pour les autres images de machine virtuelle RHEL/CentOS, ajoutez l’extension InfiniBandLinux pour activer InfiniBand. Cette extension de machine virtuelle Linux installe les pilotes Mellanox OFED (sur les machines virtuelles SR-IOV) pour la connectivité RDMA. La cmdlet PowerShell suivante installe la dernière version (version 1.0) de l’extension InfiniBandDriverLinux sur une machine virtuelle existante compatible RDMA. La machine virtuelle compatible RDMA est nommée *myVM* et est déployée dans le groupe de ressources nommé *myResourceGroup* dans la région *USA Ouest* comme suit :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Les extensions de machine virtuelle peuvent également être incluses dans les modèles Azure Resource Manager pour faciliter le déploiement, avec l’élément JSON suivant :
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  La commande suivante installe la dernière version 1.0 de l’extension InfiniBandDriverLinux sur toutes les machines virtuelles prenant en charge RDMA qui existent dans un groupe de machines virtuelles identique nommé *myVMSS* et déployées dans le groupe de ressources nommé *myResourceGroup* :
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Sur les images HPC basées sur CentOS, les mises à jour du noyau sont désactivées dans le fichier de configuration **yum** . Cela s’explique par le fait que les pilotes RDMA Linux sont distribués sous forme de package RPM, et que les mises à jour du pilote peuvent ne pas fonctionner si le noyau est mis à jour.
  >
  

* **SUSE Linux Enterprise Server** : SLES 12 SP3 pour HPC, SLES 12 SP3 pour HPC (Premium), SLES 12 SP1 pour HPC, SLES 12 SP1 pour HPC (Premium), SLES 12 SP4 et SLES 15. Les pilotes RDMA sont installés et les packages Intel MPI sont distribués sur la machine virtuelle. Installez MPI en exécutant la commande suivante :

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** : Ubuntu Server 16.04 LTS, 18.04 LTS. Configurez les pilotes RDMA sur la machine virtuelle et inscrivez-vous auprès d’Intel pour télécharger Intel MPI :

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Pour plus d’informations sur l’activation InfiniBand, la configuration de MPI, consultez [Activer InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Options de configuration de cluster

Azure fournit plusieurs options pour créer des clusters de machines virtuelles HPC Linux qui peuvent communiquer via le réseau RDMA, notamment : 

* **Machines virtuelles** : Déployez les machines virtuelles HPC compatibles RDMA dans le même groupe à haute disponibilité (quand vous utilisez le modèle de déploiement Azure Resource Manager). Si vous utilisez le modèle de déploiement classique, déployez les machines virtuelles dans le même service cloud. 

* **Groupes de machines virtuelles identiques** : dans un groupe de machines virtuelles identiques, veillez à limiter le déploiement à un seul groupe de placements. Par exemple, dans un modèle Resource Manager, définissez la propriété `singlePlacementGroup` avec la valeur `true`. 

* **MPI entre les machines virtuelles** : si la communication MPI est nécessaire entre les machines virtuelles (VM), assurez-vous que les machines virtuelles sont dans le même groupe à haute disponibilité ou le même groupe identique de machines virtuelles.

* **Azure CycleCloud** : Créez un cluster HPC dans [Azure CycleCloud](/azure/cyclecloud/) pour exécuter des travaux MPI sur des nœuds Linux.

* **Azure Batch** : Créez un pool [Azure Batch](/azure/batch/) pour exécuter des charges de travail MPI sur des nœuds de calcul Linux. Pour plus d’informations, consultez [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](../../batch/batch-pool-compute-intensive-sizes.md). Consultez également le projet [Batch Shipyard](https://github.com/Azure/batch-shipyard) pour l’exécution de charges de travail basées sur des conteneurs sur Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) prend en charge l’exécution de plusieurs distributions Linux sur les nœuds de calcul déployés dans des machines virtuelles Azure compatibles RDMA et gérés par un nœud principal Windows Server. Pour un exemple de déploiement, consultez [Créer un cluster RDMA HPC Pack Linux dans Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Considérations relatives au réseau
* Sur des machines virtuelles Linux compatibles RDMA non SR-IOV dans Azure, eth1 est réservé au trafic réseau RDMA. Ne modifiez aucun paramètre Eth1 ou des informations du fichier de configuration faisant référence à ce réseau.
* Sur les machines virtuelles compatibles SR-IOV (série HB et HC), ib0 est réservé au trafic réseau RDMA.
* Le réseau RDMA dans Azure réserve l'espace d’adressage 172.16.0.0/16. Si vous exécutez des applications MPI sur des instances déployées dans un réseau virtuel Azure, assurez-vous que l’espace d’adressage du réseau virtuel ne chevauche pas le réseau RDMA.
* Selon l’outil de gestion de clusters que vous choisissez, une configuration système supplémentaire peut être nécessaire pour exécuter des travaux MPI. Par exemple, sur un cluster de machines virtuelles, vous devrez peut-être établir une approbation entre les nœuds de cluster en générant des clés SSH ou en établissant des connexions SSH sans mot de passe.


## <a name="other-sizes"></a>Autres tailles
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la configuration, l’optimisation et la mise à l’échelle de [charges de travail HPC](../workloads/hpc/configure.md) sur Azure.
- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
