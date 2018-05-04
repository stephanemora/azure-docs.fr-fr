---
title: Créer une capture instantanée d’un disque dur virtuel dans Azure | Microsoft Docs
description: Découvrez comment créer une copie d’une machine virtuelle Azure pour l’utiliser comme sauvegarde ou pour la résolution de problèmes.
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: d7315d3fb7fc156beb85271d0e5aa19ec6baa7a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-snapshot"></a>Créer un instantané

Prenez une capture instantanée d’un disque dur virtuel de système d’exploitation ou de données pour la sauvegarde ou pour résoudre les problèmes de la machine virtuelle. Une capture instantanée est une copie complète en lecture seule d’un disque dur virtuel. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Utiliser le portail Azure pour prendre une capture instantanée 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans l’angle supérieur gauche, cliquez sur **Créer une ressource** et recherchez **capture instantanée**.
3. Dans le panneau Capture instantanée, cliquez sur **Créer**.
4. Entrez un **nom** pour la capture instantanée.
5. Sélectionnez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#resource-groups) existant ou tapez le nom d’un nouveau groupe. 
6. Sélectionnez un emplacement de centre de données Azure.  
7. Dans **Disque source**, sélectionnez le disque géré dont vous souhaitez obtenir une capture instantanée.
8. Sélectionnez le **type de compte** à utiliser pour stocker la capture instantanée. Nous vous recommandons d’utiliser le type **Standard_LRS**, sauf si vous avez besoin de la stocker sur un disque hautes performances.
9. Cliquez sur **Créer**.

## <a name="use-powershell-to-take-a-snapshot"></a>Utiliser PowerShell pour prendre une capture instantanée

Les étapes suivantes vous expliquent comment obtenir la copie du disque dur virtuel, créer les configurations de capture instantanée et prendre une capture instantanée du disque avec l’applet de commande [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Avant de commencer, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Cet article nécessite l’utilisation du module AzureRM version 5.7.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.

Définissez certains paramètres. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

Obtenez la machine virtuelle.

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

Créez la configuration de capture instantanée. Dans cet exemple, nous capturons un instantané du disque de système d’exploitation.

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> Si vous souhaitez stocker votre capture instantanée dans le stockage résilient aux zones, vous devez la créer dans une région qui prend en charge des [zones de disponibilité](../../availability-zones/az-overview.md) et inclut le paramètre `-SkuName Standard_ZRS`.   

   
Prenez la capture instantanée.

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>Étapes suivantes

Créez une machine virtuelle à partir d’une capture instantanée en créant un disque managé à partir d’une capture instantanée, puis en attachant le nouveau disque managé comme disque de système d’exploitation. Pour plus d’informations, consultez l’exemple [Créer une machine virtuelle à partir d’une capture instantanée](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
