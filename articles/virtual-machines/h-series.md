---
title: Série H - Machines virtuelles Azure
description: Spécifications pour les machines virtuelles de la série H.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: jushiman
ms.openlocfilehash: 34f89656fc56b27edafeff0e60874a00139266dd
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87268949"
---
# <a name="h-series"></a>Série H

Les machines virtuelles de la série H sont optimisées pour les applications tributaires de fréquences de processeur élevées ou de besoins en mémoire important par cœur. Les machines virtuelles de la série H disposent de 8 ou 16 cœurs de processeur Intel Xeon E5 2667 v3, de 14 Go de RAM par cœur de processeur, et d’aucun hyperthreading. Les machines virtuelles de la série H disposent d’un Infiniband FDR Mellanox de 56 Go/s dans une configuration de gros arbres non bloquant pour des performances RDMA homogènes. Les machines virtuelles de la série H prennent en charge Intel MPI 5.x et MS-MPI.

ACU : 290-300

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Migration dynamique : Non pris en charge

Mises à jour avec préservation de la mémoire : Non pris en charge

| Taille | Processeurs virtuels | Processeur | Mémoire (Go) | Bande passante mémoire (Go/s) | Fréquence du processeur de base (GHz) | Fréquence de tous les cœurs (GHz, pic) | Fréquence d’un cœur (GHz, pic) | Performances RDMA (Gbit/s) | Prise en charge MPI | Stockage temporaire (Go) | Disques de données max. | Débit de disque max : E/S par seconde | Cartes réseau Ethernet max. |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1 000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1 000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand FDR.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Images de système d’exploitation prises en charge (Linux)
 
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
  
  La commande suivante installe la version 1.0 de l’extension InfiniBandDriverLinux sur toutes les machines virtuelles RDMA d’un groupe de machines virtuelles identiques existant nommé *myVMSS* et déployé dans le groupe de ressources nommé *myResourceGroup* :
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

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Pour plus d’informations sur l’activation InfiniBand, la configuration de MPI, consultez [Activer InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
