---
title: Gérer un pool de disques Azure (préversion)
description: Découvrez comment ajouter des disques managés à un pool de disques Azure ou désactiver la prise en charge iSCSI sur un disque.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4811894a3483bbfce1724b744d904fa328c51ded
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436984"
---
# <a name="manage-an-azure-disk-pool-preview"></a>Gérer un pool de disques Azure (préversion)

Cet article explique comment ajouter un disque managé à un pool de disques Azure (préversion) et comment désactiver la prise en charge iSCSI sur un disque qui a été ajouté à un pool de disques.

## <a name="prerequisites"></a>Configuration requise

Installer le module Azure PowerShell, [version 6.1.0 ou ultérieure](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true).

## <a name="add-a-disk-to-a-pool"></a>Ajouter un disque à un pool

Votre disque doit remplir les conditions suivantes pour pouvoir être ajouté au pool de disques :
- Il doit s'agir d'un disque SSD Premium ou d'un disque Ultra intégré dans la même région et zone de disponibilité que le pool de disques.
    - Les disques Ultra doivent avoir une taille de secteur de disque de 512 octets.
- Il doit s'agir d'un disque partagé dont la valeur maxShares est supérieure ou égale à deux.
- Vous devez [fournir les autorisations RBAC du fournisseur de ressources StoragePool aux disques qui seront ajoutés au pool de disques](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions).

Le script suivant ajoute un disque supplémentaire au pool de disques et l’expose sur iSCSI. Il conserve les disques existants dans le pool de disques sans aucune modification.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='LunName>' #Provide the Lun name of the added disk
$diskIds = @()

#Add the disk to disk pool
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
$diskIds += ($Id)
}

$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$diskIds += ,($disk.Id)
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds

#Get the existing iSCSI LUNs and add the new disk
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}

$newlun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $disk.Id -Name $lunName
$luns += ,($newlun)
Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns
```

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>Désactiver iSCSI sur un disque et le supprimer du pool

Avant de désactiver la prise en charge iSCSI sur un disque, vérifiez qu’il n’existe aucune connexion iSCSI en suspens au numéro d’unité logique iSCSI sur lequel le disque est exposé. Lorsqu’un disque est retiré du pool de disques, il n’est pas automatiquement supprimé. Cela permet d’éviter toute perte de données, cependant vous serez toujours facturé pour le stockage des données. Si vous n’avez pas besoin des données stockées sur un disque, vous pouvez supprimer manuellement le disque. Cette opération supprimera le disque et toutes les données qu’il contient et empêchera les frais supplémentaires.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LUNForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
$diskIds = @()

#Get the existing iSCSI LUNs and remove it from iSCS target
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
if ($lun.Name -notlike $lunName)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}
}

Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns

#Remove the disk from disk pool
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
if ($Id -notlike $disk.Id)
{
$diskIds += ($Id)
}
}

Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds
```

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment déplacer un pool de disques vers un autre abonnement, consultez [Déplacer un pool de disques vers un autre abonnement](disks-pools-move-resource.md).
- Pour savoir comment annuler l’approvisionnement d’un pool de disques, voir [Annuler l’approvisionnement d’un pool de disques Azure](disks-pools-deprovision.md).