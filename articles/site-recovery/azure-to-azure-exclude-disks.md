---
title: Azure Site Recovery - exclusion de disques lors de la réplication de machines virtuelles à l’aide d’Azure PowerShell | Microsoft Docs
description: Découvrez comment exclure des disques de machines virtuelles Azure pendant la récupération de Site Azure à l’aide d’Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678273"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Exclure des disques de la réplication de machines virtuelles Azure PowerShell

Cet article décrit comment exclure des disques lorsque vous répliquez des machines virtuelles Azure. Vous pouvez exclure des disques pour optimiser la bande passante utilisée pour la réplication ou les ressources côté cible qui utilisent ces disques. Actuellement, cette fonctionnalité est disponible uniquement par le biais d’Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer :

- Assurez-vous que vous comprenez le [architecture de récupération d’urgence et composants](azure-to-azure-architecture.md).
- Vérifiez les [exigences de prise en charge](azure-to-azure-support-matrix.md) pour tous les composants.
- Assurez-vous que vous avez AzureRm PowerShell « Az » module. Pour installer ou mettre à jour PowerShell, consultez [installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Assurez-vous que vous avez créé un coffre recovery services et machines virtuelles protégées au moins une fois. Si vous n’avez pas effectué ces choses, suivez le processus à [configurer la récupération d’urgence pour les machines virtuelles Azure à l’aide d’Azure PowerShell](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Pourquoi exclure des disques de la réplication
Vous devrez peut-être exclure les disques de réplication, car :

- Votre machine virtuelle a atteint [limites Azure Site Recovery pour répliquer les données des taux de modification](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- Les données hautement évolutives sur le disque exclu n’est pas importantes ou n’a pas besoin d’être répliquées.

- Voulez-vous enregistrer les ressources de stockage et réseau ne pas répliquer les données.

## <a name="how-to-exclude-disks-from-replication"></a>Comment exclure des disques de réplication

Dans notre exemple, nous répliquer une machine virtuelle qui a un système d’exploitation et trois disques de données dans la région est des États-Unis vers la région ouest des États-Unis 2. Le nom de la machine virtuelle est *AzureDemoVM*. Nous exclure le disque 1 et que vous conservez les disques 2 et 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Obtenir les détails des machines virtuelles à répliquer

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Obtenir des détails sur les disques de la machine virtuelle. Ces informations seront utilisées plus tard, lorsque vous démarrez la réplication de la machine virtuelle.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Répliquer une machine virtuelle Azure

Pour l’exemple suivant, nous partons du principe que vous avez déjà un compte de stockage de cache, la stratégie de réplication et les mappages. Si vous n’avez pas ces éléments, suivez le processus à [configurer la récupération d’urgence pour les machines virtuelles Azure à l’aide d’Azure PowerShell](azure-to-azure-powershell.md).

Répliquer une machine virtuelle Azure avec *des disques gérés*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Lorsque l’opération de réplication de début réussit, la machine virtuelle sont répliquées vers la région de récupération.

Vous pouvez accéder au portail Azure et voir les machines virtuelles répliquées sous « éléments répliqués ».

Le processus de réplication commence par une copie de disques de réplication de la machine virtuelle dans la région de récupération de l’amorçage. Cette phase est appelée la phase de la réplication initiale.

Une fois la réplication initiale terminée, la réplication passe à la phase de synchronisation de la sauvegarde différentielle. À ce stade, la machine virtuelle est protégée. Sélectionnez la machine virtuelle protégée pour voir si tous les disques sont exclus.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [un test de basculement en cours d’exécution](site-recovery-test-failover-to-azure.md).
