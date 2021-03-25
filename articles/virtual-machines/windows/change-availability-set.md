---
title: Modification du groupe à haute disponibilité d’une machine virtuelle avec Azure PowerShell
description: Apprenez à modifier un groupe à haute disponibilité pour votre machine virtuelle à l’aide d’Azure PowerShell.
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: mimckitt
ms.openlocfilehash: 99985d0bb2294c538efa712e477cc6f8a2eb4938
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102498470"
---
# <a name="change-the-availability-set-for-a-vm-using-azure-powershell"></a>Modification du groupe à haute disponibilité d’une machine virtuelle avec Azure PowerShell    
Les étapes suivantes décrivent comment modifier le groupe à haute disponibilité d’une machine virtuelle à l’aide d’Azure PowerShell. Une machine virtuelle ne peut être ajoutée à un groupe à haute disponibilité que lors de sa création. Pour modifier le groupe à haute disponibilité, vous devez supprimer, puis recréer la machine virtuelle. 

Cet article s’applique aux machines virtuelles Linux et Windows.

Cet article a été testé pour la dernière fois le 12/02/2019 à l’aide d’[Azure Cloud Shell](https://shell.azure.com/powershell) et du [module Az PowerShell](/powershell/azure/install-az-ps) version 1.2.0.

Cet exemple ne vérifie pas si la machine virtuelle est attachée à un équilibreur de charge. Si votre machine virtuelle est attachée à un équilibreur de charge, vous devez mettre à jour le script pour gérer ce cas. 


## <a name="change-the-availability-set"></a>Changer le groupe à haute disponibilité 

Le script suivant fournit un exemple de rassemblement des informations requises, de suppression de la machine virtuelle d’origine et de recréation de la machine virtuelle dans un groupe à haute disponibilité.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary; keep the Private IP too, if it exists. 
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Étapes suivantes

Ajout de stockage supplémentaire à votre machine virtuelle en ajoutant un [disque de données](attach-managed-disk-portal.md)supplémentaire.
