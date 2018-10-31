---
title: Attacher un disque de données à une machine virtuelle Windows dans Azure à l’aide de PowerShell | Microsoft Docs
description: Comment attacher un disque de données nouveau ou existant à une machine virtuelle Windows à l’aide de PowerShell avec le modèle de déploiement Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cynthn
ms.openlocfilehash: cd11bb8ae8f22705feb7eebeafde385fcf11fdcd
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637083"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Attacher un disque de données à une machine virtuelle Windows dans Azure avec PowerShell

Cet article vous explique comment attacher des disques nouveaux et existants à une machine virtuelle Windows à l’aide de PowerShell. 

Commencez par passer en revue ces conseils :
* La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour plus d’informations, consultez [Tailles des machines virtuelles](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour utiliser le stockage Premium, vous avez besoin d’un type de machine virtuelle de série DS ou GS, compatible avec ce genre de stockage. Pour plus d’informations, consultez [Stockage Premium hautes performances et disques gérés pour machines virtuelles](premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pour installer et utiliser PowerShell en local, ce didacticiel requiert le module Azure PowerShell version 6.0.0 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzureRmAccount` pour créer une connexion avec Azure.


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Ajouter un disque de données vide à une machine virtuelle

Cet exemple montre comment ajouter un disque de données vide à une machine virtuelle existante.

### <a name="using-managed-disks"></a>Utilisation de disques gérés

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Utilisation de disques gérés dans un groupe à haute disponibilité
Pour créer un disque dans une zone de disponibilité, utilisez [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) avec le paramètre `-Zone`. L’exemple suivant crée un disque dans la zone *1*.


```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```


### <a name="initialize-the-disk"></a>Initialiser le disque

Après avoir ajouté un disque vide, vous devez l’initialiser. Pour initialiser le disque, vous pouvez vous connecter à une machine virtuelle et utiliser la gestion des disques. Si vous avez activé [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) et un certificat sur la machine virtuelle lors de sa création, vous pouvez utiliser PowerShell à distance pour initialiser le disque. Vous pouvez également utiliser une extension de script personnalisée : 

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
Le fichier de script peut contenir du code pour initialiser les disques, par exemple :

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```


## <a name="attach-an-existing-data-disk-to-a-vm"></a>Ajouter un disque de données existant à une machine virtuelle

Vous pouvez attacher un disque géré existant à une machine virtuelle en tant que disque de données. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Étapes suivantes

Créer une [capture instantanée](snapshot-copy-managed-disk.md).
