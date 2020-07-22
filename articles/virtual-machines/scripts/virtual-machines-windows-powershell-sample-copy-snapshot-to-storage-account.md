---
title: Exporter/Copier une capture instantanée en tant que disque dur virtuel dans un compte de stockage appartenant à une autre région - Exemple PowerShell
description: 'Exemple de script Azure PowerShell : exporter/copier une capture instantanée en tant que disque dur virtuel vers un compte de stockage dans une autre région'
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
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 9b8510d3eb66cc63a0c9c660b34514a83a35d25c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509578"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Exporter/copier des captures instantanées gérées en tant que disque dur virtuel vers un compte de stockage dans une région différente avec PowerShell

Ce script exporte une capture instantanée managée vers un compte de stockage dans une région différente. Il génère d’abord l’URI SAP de la capture instantanée et l’utilise ensuite pour copier celle-ci vers un compte de stockage dans une autre région. Ce script vous permet de conserver la sauvegarde de vos disques managés dans une autre région en cas de récupération d’urgence.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes afin de générer un URI SAS pour une capture instantanée gérée et copie cette dernière vers un compte de stockage à l’aide de l’URI SAS. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Grant-AzSnapshotAccess](/powershell/module/az.compute/new-azdisk) | Génère l’URI SAS pour une capture instantanée qui est utilisée pour sa copie dans un compte de stockage. |
| [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) | Crée un contexte de compte de stockage avec le nom de compte et la clé. Ce contexte peut être utilisé pour effectuer des opérations de lecture/écriture sur le compte de stockage. |
| [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) | Copie le VHD sous-jacent d’une capture instantanée vers un compte de stockage |

## <a name="next-steps"></a>Étapes suivantes

[Créer un disque managé à partir d’un VHD](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Créer une machine virtuelle à partir d’un disque géré](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
