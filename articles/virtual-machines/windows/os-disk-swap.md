---
title: Remplacer le disque du système d’exploitation d’une machine virtuelle Azure avec PowerShell
description: Utilisez PowerShell pour remplacer le disque du système d’exploitation utilisé par une machine virtuelle Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 566347414ffe707b1d68a61b00ba21d19ff2b1eb
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869382"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Remplacer le disque du système d’exploitation utilisé par une machine virtuelle Azure à l’aide de PowerShell

Pour remplacer le disque d’une machine virtuelle existante par un disque de sauvegarde ou un autre disque de système d’exploitation, vous pouvez utiliser Azure PowerShell. Il est inutile de supprimer et de recréer la machine virtuelle. Vous pouvez même utiliser le disque managé d’un autre groupe de ressources s’il n’est pas déjà utilisé.

 

Vous devez arrêter/libérer la machine virtuelle, puis remplacer l’ID de ressource du disque managé par celui d’un autre disque managé.

Vérifiez que la taille et le type de stockage de la machine virtuelle sont compatibles avec le disque à attacher. Ainsi, si le disque que vous souhaitez utiliser bénéficie d’un stockage Premium, la machine virtuelle doit être de taille suffisante pour prendre en charge ce niveau de stockage (comme une série DS). Les deux disques doivent également avoir la même taille.

Obtenir la liste des disques d'un groupe de ressources à l'aide de [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Après avoir obtenu le nom du disque à utiliser, définissez-le comme disque du système d’exploitation pour la machine virtuelle. Cet exemple arrête/libère la machine virtuelle nommée *myVM* et affecte le disque nommé *newDisk* comme nouveau disque du système d’exploitation. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Étapes suivantes**

Pour créer une copie d’un disque, consultez [Effectuer la capture instantanée d’un disque](snapshot-copy-managed-disk.md).
