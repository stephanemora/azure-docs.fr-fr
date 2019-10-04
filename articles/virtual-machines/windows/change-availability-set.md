---
title: Modification d’un groupe à haute disponibilité pour les machines virtuelles | Microsoft Docs
description: Apprenez à modifier un groupe à haute disponibilité pour vos machines virtuelles à l’aide d’Azure PowerShell et du modèle de déploiement Resource Manager.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/12/2019
ms.author: cynthn
ms.openlocfilehash: 0a91a80c18b04e257daa9a42fd7933351fe3a35c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080149"
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>Modification du groupe à haute disponibilité pour une machine virtuelle Windows
Les étapes suivantes décrivent comment modifier le groupe à haute disponibilité d’une machine virtuelle à l’aide d’Azure PowerShell. Une machine virtuelle ne peut être ajoutée à un groupe à haute disponibilité que lors de sa création. Pour modifier le groupe à haute disponibilité, vous devez supprimer, puis recréer la machine virtuelle. 

Cet article a été testé pour la dernière fois le 12/02/2019 à l’aide d’[Azure Cloud Shell](https://shell.azure.com/powershell) et du [module Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) version 1.2.0.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

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

# Create the basic configuration for the replacement VM
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
  
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
    
# Add NIC(s) and keep the same NIC as primary
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

Ajout de stockage supplémentaire à votre machine virtuelle en ajoutant un [disque de données](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)supplémentaire.

