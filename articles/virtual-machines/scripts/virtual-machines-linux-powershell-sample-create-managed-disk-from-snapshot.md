---
title: Créer un disque managé à partir d’une capture instantanée (Linux) - Exemple de code PowerShell
description: 'Exemple de script Azure PowerShell : Créer un disque géré à partir d’une capture instantanée'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 844bff9a363fb9734159e892006dd61440132a34
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079959"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell-linux"></a>Créer un disque managé à partir d’une capture instantanée avec PowerShell (Linux)

Ce script crée un disque managé à partir d’une capture instantanée. Utilisez-le pour restaurer une machine virtuelle à partir de captures instantanées de disques de système d’exploitation et de données. Créez des disques managés de système d’exploitation et de données à partir des captures instantanées respectives, puis créez une machine virtuelle en joignant les disques managés. Vous pouvez également restaurer les disques de données d’une machine virtuelle existante en joignant les disques de données créés à partir de captures instantanées.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer un disque managé à partir d’une capture instantanée. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Obtient les propriétés de capture instantanée.  |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Crée une configuration de disque qui est utilisée pour la création du disque. Il inclut l’ID de ressource de la capture instantanée parente, un emplacement identique à l’emplacement de la capture instantanée parente, et le type de stockage.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Crée un disque à partir de la configuration de disque, du nom du disque et du nom de groupe de ressources transmis en tant que paramètres. |

## <a name="next-steps"></a>Étapes suivantes


[Créer une machine virtuelle à partir d’un disque géré](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
