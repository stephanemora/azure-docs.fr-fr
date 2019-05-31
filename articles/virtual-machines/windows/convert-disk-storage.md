---
title: Convertir Azure stockage managed disks standard vers Premium ou Premium à Standard | Microsoft Docs
description: Comment convertir Azure disques gérés standard vers Premium ou Premium à Standard à l’aide d’Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 5687e6d0094083a9ee58455cc72b0b2e4da32d65
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417154"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Mettre à jour le type de stockage d’un disque managé

Il existe quatre types de disques de Azure des disques gérés : SSDs ultra Azure (aperçu), premium SSD, disque SSD standard et disques durs standard. Vous pouvez basculer entre les trois types de disque de la disponibilité générale (premium SSD, disque SSD standard et disques durs standard) en fonction de vos besoins en performances. Vous n’êtes pas encore en mesure de basculer depuis ou vers un disque SSD ultra, vous devez déployer un nouveau.

Cette fonctionnalité n’est pas pris en charge pour les disques non gérés. Toutefois, vous pouvez facilement [convertir un disque non managé en un disque géré](convert-unmanaged-to-managed-disks.md) pour être en mesure de basculer entre les types de disque.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables

* Étant donné que la conversion nécessite un redémarrage de la machine virtuelle (VM), vous devez planifier la migration de votre stockage sur disque pendant une fenêtre de maintenance préexistante.
* Si votre disque n’est pas géré, tout d’abord [convertir en un disque géré](convert-unmanaged-to-managed-disks.md) afin que vous pouvez basculer entre les options de stockage.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Basculer l’ensemble des disques gérés d’une machine virtuelle entre Standard et Premium

Cet exemple montre comment convertir tous les disques d’une machine virtuelle à partir de Standard vers le stockage Premium ou de Premium vers le stockage Standard. Pour utiliser des disques managés Premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage Premium. Cet exemple passe également à une taille prenant en charge le stockage premium :

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Basculer les disques gérés individuels entre Standard et Premium

Pour votre charge de travail de développement/test, vous souhaiterez peut-être un mélange de disques Standard et Premium pour réduire les coûts. Vous pouvez choisir de mettre à niveau uniquement ces disques qui ont besoin de meilleures performances. Cet exemple montre comment convertir un disque de machine virtuelle unique à partir de Standard vers le stockage Premium ou de Premium vers le stockage Standard. Pour utiliser des disques managés Premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage Premium. Cet exemple montre également comment passer à une taille qui prend en charge le stockage Premium :

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Convertir des disques gérés de Standard en Premium dans le portail Azure

Procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez la machine virtuelle à partir de la liste des **machines virtuelles** dans le portail.
3. Si la machine virtuelle n’est pas arrêtée, sélectionnez **arrêter** en haut de la machine virtuelle **vue d’ensemble** volet et attendez que la machine virtuelle à arrêter.
3. Dans le volet de la machine virtuelle, sélectionnez **disques** à partir du menu.
4. Sélectionnez le disque que vous souhaitez convertir.
5. Sélectionnez **Configuration** dans le menu.
6. Modifier le **type de compte** à partir de **HDD Standard** à **Premium SSD**.
7. Cliquez sur **enregistrer**et fermez le volet de disque.

La conversion de type de disque est instantanée. Vous pouvez redémarrer votre machine virtuelle après la conversion.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Basculer entre Standard HDD et SSD Standard des disques gérés 

Cet exemple montre comment convertir un disque de machine virtuelle unique à partir du disque dur Standard pour disque SSD Standard ou d’un disque SSD Standard sur le disque dur Standard :

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Étapes suivantes

Créez une copie en lecture seule d’une machine virtuelle en utilisant une [capture instantanée](snapshot-copy-managed-disk.md).
