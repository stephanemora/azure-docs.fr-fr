---
title: Convertir le stockage Managed Disks Azure de standard en premium, et vice versa | Microsoft Docs
description: Comment convertir les disques gérés Azure de standard en premium, et vice versa, à l’aide de Microsoft Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.openlocfilehash: 958f661585b38b156cf523fe00986e7594474917
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093814"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Mettre à jour le type de stockage d’un disque managé

Azure Managed Disks offre trois types de stockage : [SSD Premium](../windows/premium-storage.md), [SSD Standard](../windows/disks-standard-ssd.md) et [HDD Standard](../windows/standard-storage.md). Vous pouvez changer le type de stockage d’un disque managé avec un temps d’arrêt minimal en fonction de vos besoins en performances. Le changement du type de stockage d’un disque non managé n’est pas pris en charge. Toutefois, vous pouvez facilement [convertir un disque non managé en disque managé](convert-unmanaged-to-managed-disks.md).

Cet article montre comment convertir un disque managé standard en premium, et vice versa, à l’aide d’Azure PowerShell. Si vous devez installer ou mettre à niveau PowerShell, consultez [Installer et configurer Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1).

## <a name="prerequisites"></a>Prérequis

* Étant donné que la conversion nécessite un redémarrage de la machine virtuelle, vous devez planifier la migration de votre stockage sur disque durant une fenêtre de maintenance préexistante. 
* Si vous utilisez un disque non managé, commencez par [le convertir en disque managé](convert-unmanaged-to-managed-disks.md) pour pouvoir basculer entre les types de stockage. 
* Les exemples de cet article nécessitent le module Azure PowerShell version 6.0.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Exécutez [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) pour créer une connexion avec Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>Convertir tous les disques managés d’une machine virtuelle de standard en premium

L’exemple suivant montre comment faire passer tous les disques d’une machine virtuelle du stockage standard au stockage premium. Pour utiliser des disques managés premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage premium. Cet exemple passe également à une taille prenant en charge le stockage premium :

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>Convertir un disque managé standard en premium

Pour votre charge de travail de développement/test, vous souhaiterez peut-être combiner disques standard et disques premium pour réduire les coûts. Pour cela, mettez uniquement à niveau les disques qui nécessitent de meilleures performances avec le stockage premium. L’exemple suivant montre comment faire passer un seul disque d’une machine virtuelle du stockage standard au stockage premium, et vice versa. Pour utiliser des disques managés premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage premium. Cet exemple montre également comment passer à une taille prenant en charge le stockage premium :

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Convertir un disque managé HDD standard en SSD standard

L’exemple suivant montre comment faire passer un seul disque d’une machine virtuelle HDD standard en SSD standard, et vice versa :

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Étapes suivantes

Créez une copie en lecture seule d’une machine virtuelle en utilisant une [capture instantanée](snapshot-copy-managed-disk.md).

