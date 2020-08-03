---
title: Copier des disques managés dans un abonnement (Windows) – Exemple PowerShell
description: Exemple de script Azure PowerShell - Copier ou déplacer des disques managés dans un même abonnement ou dans un abonnement différent
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 6f191eaf5c0a02f77d267999c307d34e083b6cdf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069175"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell-windows"></a>Copier des disques managés dans le même abonnement ou dans un autre abonnement avec PowerShell (Windows)

Ce script crée la copie d’un disque géré existant dans le même abonnement ou un autre abonnement. Le disque est créé dans la même région que le disque géré parent.   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un disque géré dans l’abonnement cible à l’aide de l’ID du disque source géré. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Crée une configuration de disque qui est utilisée pour la création du disque. Elle inclut l’ID de ressource du disque parent et un emplacement identique à l’emplacement du disque parent.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Crée un disque à partir de la configuration de disque, du nom du disque et du nom de groupe de ressources transmis en tant que paramètres. |


## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle à partir d’un disque géré](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
