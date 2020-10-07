---
title: Créer une machine virtuelle à partir d’une capture instantanée (Linux) – Exemple de code PowerShell
description: Exemples de script Azure PowerShell - Créer une machine virtuelle à partir d’une capture instantanée avec un exemple Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: e39c703452b5bb6855062c1c3efbde29d2becd1e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320147"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-linux"></a>Créer une machine virtuelle à partir d’une capture instantanée avec PowerShell (Linux)

Ce script crée une machine virtuelle à partir d’une capture instantanée d’un disque de système d’exploitation.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour obtenir des propriétés de capture instantanée, créer un disque géré à partir de la capture instantanée et créer une machine virtuelle. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Obtient une capture instantanée à l’aide du nom de celle-ci. |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Crée une configuration de disque. Cette configuration est utilisée dans le processus de création de disque. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Crée un disque géré. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Crée une configuration de machine virtuelle. Cette configuration inclut des informations telles que le nom de la machine virtuelle, le système d’exploitation et les informations d’identification d’administration. La configuration est utilisée lors de la création de machines virtuelles. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Attache le disque géré en tant que disque de système d’exploitation à la machine virtuelle |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crée une adresse IP publique. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Crée une interface réseau. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Crée une machine virtuelle. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
