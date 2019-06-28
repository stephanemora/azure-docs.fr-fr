---
title: Utiliser PowerShell pour redimensionner une machine virtuelle Windows dans Azure | Microsoft Docs
description: Redimensionnez une machine virtuelle Windows créée avec le modèle de déploiement Resource Manager à l’aide d’Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 3dc6ea7419e3ee1f24d19447a7a33e5556ffc70f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688722"
---
# <a name="resize-a-windows-vm"></a>Redimensionner une machine virtuelle Windows

Cet article vous explique comment changer la [taille d’une machine virtuelle](sizes.md) à l’aide d’Azure Powershell.

Une fois que vous avez créé une machine virtuelle, vous pouvez la mettre à l’échelle en modifiant sa taille. Dans certains cas, vous devez commencer par libérer la machine virtuelle. Cela peut se produire si la nouvelle taille n’est pas disponible sur le cluster matériel qui héberge actuellement la machine virtuelle.

Si votre machine virtuelle utilise le stockage Premium, assurez-vous de choisir une version **s** de la taille pour obtenir un support de stockage Premium. Par exemple, choisissez Standard_E4**s**_v3 au lieu de Standard_E4_v3.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Redimensionner une machine virtuelle Windows qui ne se trouve pas dans un groupe à haute disponibilité

Définissez des variables. Remplacez les valeurs par vos propres informations.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Liste les tailles de machines virtuelles qui sont disponibles sur le cluster matériel sur lequel la machine virtuelle est hébergée. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Si la taille voulue est répertoriée, exécutez les commandes suivantes pour redimensionner la machine virtuelle. Si la taille souhaitée n’est pas répertoriée, passez à l’étape 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Si la taille voulue n’est pas répertoriée, exécutez les commandes suivantes pour libérer la machine virtuelle, la redimensionner et la redémarrer. Remplacez **\<newVMsize>** par la taille voulue.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Le fait de libérer la machine virtuelle libère toutes les adresses IP dynamiques affectées à la machine virtuelle. Les disques de données et du système d’exploitation ne sont pas affectés. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Redimensionner une machine virtuelle Windows qui se trouve dans un groupe à haute disponibilité

Si la nouvelle taille d’une machine virtuelle se trouvant dans un groupe à haute disponibilité n’est pas disponible sur le cluster matériel qui l’héberge actuellement, toutes les machines virtuelles du groupe à haute disponibilité doivent être libérées pour redimensionner la machine virtuelle. Vous devrez peut-être mettre à jour la taille des autres machines virtuelles du groupe à haute disponibilité après le redimensionnement d’une des machines virtuelles. Pour redimensionner une machine virtuelle dans un groupe à haute disponibilité, procédez comme suit.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Liste les tailles de machines virtuelles qui sont disponibles sur le cluster matériel sur lequel la machine virtuelle est hébergée. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Si la taille voulue n’est pas répertoriée, exécutez les commandes suivantes pour redimensionner la machine virtuelle. Si elle n’est pas répertoriée, passez à la section suivante.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Si la taille voulue n’est pas répertoriée, passez aux étapes suivantes pour libérer toutes les machines virtuelles du groupe à haute disponibilité, les redimensionner et les redémarrer.

Arrêtez toutes les machines virtuelles du groupe à haute disponibilité.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Redimensionnez et redémarrez toutes les machines virtuelles du groupe à haute disponibilité.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Étapes suivantes

Pour une évolutivité supplémentaire, exécutez plusieurs instances de machine virtuelle et augmentez leur taille. Pour plus d’informations, consultez [Mise à l’échelle automatique des machines Windows dans un groupe de machines virtuelles identiques](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

