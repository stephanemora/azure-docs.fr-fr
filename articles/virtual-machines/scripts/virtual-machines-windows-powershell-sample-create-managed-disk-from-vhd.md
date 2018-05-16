---
title: Exemple de script Azure PowerShell - Créer un disque géré à partir d’un fichier de disque dur virtuel dans un compte de stockage dans le même abonnement ou dans un abonnement différent | Microsoft Docs
description: Exemple de script Azure PowerShell - Créer un disque géré à partir d’un fichier de disque dur virtuel dans un compte de stockage dans le même abonnement ou dans un abonnement différent
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 6dfdd88f2f1e776fca69ffa53b2e424fe9d2c8ea
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Créer un disque géré à partir d’un fichier de disque dur virtuel dans un compte de stockage dans le même abonnement ou dans un abonnement différent avec PowerShell

Ce script crée un disque géré à partir d’un fichier de disque dur virtuel dans un compte de stockage dans le même abonnement ou un abonnement différent. Utilisez ce script pour importer un disque dur virtuel spécialisé (non généralisé/préparé avec Sysprep) dans un disque de système d’exploitation géré pour créer une machine virtuelle. Vous pouvez également vous en servir pour importer un disque dur virtuel de données dans un disque de données géré. 

Ne créez pas plusieurs disques gérés identiques à partir d’un fichier de disque dur virtuel dans un court laps de temps. Pour créer des disques gérés à partir d’un fichier de disque dur virtuel, la capture instantanée de blob du fichier de disque dur virtuel est créée, puis utilisée pour créer des disques gérés. Seule une capture instantanée de blob peut être créée par minute, sous peine d’entraîner des échecs de création de disque en raison d’une limitation. Pour éviter cette limitation, créez une [capture instantanée gérée à partir du fichier de disque dur virtuel](virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json), puis utilisez-la pour créer rapidement plusieurs disques gérés. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 4.0 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous avez besoin de procéder à une installation ou à une mise à niveau, consultez [Installer Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer un disque géré à partir d’un disque dur virtuel dans un abonnement différent. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Crée une configuration de disque qui est utilisée pour la création du disque. Il inclut le type de stockage, l’emplacement, l’ID de ressource du compte de stockage dans lequel le disque dur virtuel parent est stocké et l’URI du disque dur virtuel parent. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Crée un disque à partir de la configuration de disque, du nom du disque et du nom de groupe de ressources transmis en tant que paramètres. |

## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle en joignant un disque managé en tant que disque de système d’exploitation](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
