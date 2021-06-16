---
title: Convertir les disques managés d’un type de disque à l’autre avec Azure PowerShell
description: Comment convertir les disques managés Azure d’un type de disque à l’autre à l’aide d’Azure PowerShell.
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d2b0ac2750515c8a61674b695e0faf3fd54ff7c6
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110670251"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Mettre à jour le type de stockage d’un disque managé

Il existe quatre types de disques managés Azure : Disques Ultra, SSD Premium, SSD Standard et HDD Standard Azure. Vous pouvez basculer vers des disques SSD Premium, SSD Standard et HDD Standard, selon vos besoins en performances. Il n'est pas encore possible de passer d'un disque Ultra à un autre type de disque ou l'inverse : vous devez en déployer un nouveau.

Cette fonctionnalité n’est pas prise en charge pour les disques non managés. En revanche, vous pouvez facilement [convertir un disque non managé en disque managé](convert-unmanaged-to-managed-disks.md) pour pouvoir basculer entre les différents types de disques.



## <a name="before-you-begin"></a>Avant de commencer

* Étant donné que la conversion nécessite un redémarrage de la machine virtuelle, vous devez planifier la migration de votre stockage sur disque pendant une fenêtre de maintenance préexistante.
* Si votre disque est non managé, commencez par le [convertir en disque managé](convert-unmanaged-to-managed-disks.md) afin de pouvoir basculer entre les options de stockage.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Faire basculer tous les disques managés d’une machine virtuelle d’un compte vers un autre

Cet exemple montre comment convertir tous les disques d’une machine virtuelle en stockage Premium. Toutefois, en modifiant la variable $storageType de cet exemple, vous pouvez convertir les disques de la machine virtuelle en disques SSD standard ou HDD standard. Pour utiliser des disques managés Premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](../sizes.md) qui prend en charge le stockage Premium. Cet exemple passe également à une taille prenant en charge le stockage premium :

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Basculer des disques managés individuels entre les stockages Standard et Premium

Pour votre charge de travail de développement/test, vous pouvez mélanger des disques Standard et Premium afin de réduire les coûts. Vous pouvez choisir de mettre à niveau seulement les disques nécessitant de meilleures performances. Cet exemple montre comment faire passer un disque de machine virtuelle du stockage Standard au stockage Premium. Toutefois, en modifiant la variable $storageType de cet exemple, vous pouvez convertir les disques de la machine virtuelle en disques SSD standard ou HDD standard. Pour utiliser des disques managés Premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](../sizes.md) qui prend en charge le stockage Premium. Cet exemple montre également comment passer à une taille prenant en charge le stockage Premium :

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Faire basculer des disques managés d’un type de disque vers un autre

Procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez la machine virtuelle dans la liste des **machines virtuelles**.
3. Si la machine virtuelle n’est pas arrêtée, sélectionnez **Arrêter** en haut du volet **Vue d’ensemble** de la machine virtuelle et attendez que celle-ci s’arrête.
4. Dans le menu du volet pour la machine virtuelle, sélectionnez **Disques**.
5. Sélectionnez le disque à convertir.
6. Sélectionnez **Configuration** dans le menu.
7. Remplacez le **Type de compte** d’origine du disque par le type de disque souhaité.
8. Sélectionnez **Enregistrer**, puis fermez le volet du disque.

La conversion du type de disque est instantanée. Vous pouvez redémarrer votre machine virtuelle après la conversion.

## <a name="next-steps"></a>Étapes suivantes

Créez une copie en lecture seule d’une machine virtuelle en utilisant une [capture instantanée](snapshot-copy-managed-disk.md).
