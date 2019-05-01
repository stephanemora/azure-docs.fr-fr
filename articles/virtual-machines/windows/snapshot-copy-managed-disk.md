---
title: Créer une capture instantanée d’un disque dur virtuel dans Azure | Microsoft Docs
description: Découvrez comment créer une copie d’une machine virtuelle Azure pour l’utiliser comme sauvegarde ou pour la résolution de problèmes.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: b3b9095cd7ee3fa12523b14f59cc06820b9e4382
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692216"
---
# <a name="create-a-snapshot"></a>Créer un instantané

Une capture instantanée est une copie complète en lecture seule d’un disque dur virtuel (VHD). Vous pouvez prendre une capture instantanée d’un disque dur virtuel de système d’exploitation ou de données que vous utiliserez comme sauvegarde ou pour résoudre les problèmes de la machine virtuelle.

Si vous prévoyez d’utiliser la capture instantanée pour créer une machine virtuelle, nous vous recommandons d’arrêter convenablement la machine virtuelle avant de prendre une capture instantanée, de façon à mettre fin à tous les processus en cours.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Créer une ressource**, puis recherchez et sélectionnez une **capture instantanée**.
3. Dans la fenêtre **Capture instantanée**, sélectionnez **Créer**. La fenêtre **Créer une capture instantanée** s’affiche.
4. Entrez un **nom** pour la capture instantanée.
5. Sélectionnez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#resource-groups) existant ou entrez le nom d’un nouveau. 
6. Sélectionnez un **Emplacement** de centre de données Azure.  
7. Dans **Disque source**, sélectionnez le disque managé dont vous souhaitez obtenir une capture instantanée.
8. Sélectionnez le **type de compte** à utiliser pour stocker la capture instantanée. Sélectionnez **Standard_HDD**, sauf si vous avez besoin que la capture soit stockée sur un disque hautes performances.
9. Sélectionnez **Créer**.

## <a name="use-powershell"></a>Utiliser PowerShell

Les étapes suivantes expliquent comment copier le disque dur virtuel, créer la configuration de capture instantanée et prendre une capture instantanée du disque avec la cmdlet [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot). 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

1. Définissez certains paramètres : 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Obtenez la machine virtuelle :

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Créez la configuration de capture instantanée. Pour cet exemple, la capture instantanée est celle du disque de système d’exploitation :

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Si vous souhaitez stocker votre capture instantanée dans le stockage résilient aux zones, créez-la dans une région qui prend en charge des [zones de disponibilité](../../availability-zones/az-overview.md) et inclut le paramètre `-SkuName Standard_ZRS`.   
   
4. Prenez la capture instantanée :

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Étapes suivantes

Créez une machine virtuelle à partir d’une capture instantanée en créant un disque managé à partir d’une capture instantanée, puis en attachant le nouveau disque managé comme disque de système d’exploitation. Pour plus d’informations, consultez l’exemple dans [Créer une machine virtuelle à partir d’une capture instantanée avec PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
