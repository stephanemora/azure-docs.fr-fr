---
title: Copier la capture instantanée d’un disque managé dans un abonnement - Exemple PowerShell
description: 'Exemple de script Azure PowerShell : Copier (ou déplacer) la capture instantanée d’un disque managé dans un abonnement identique ou différent'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 65a3e39206864f10c41e79ba6b3e7a89da99dc6f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463778"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Copier une capture instantanée de disque géré dans un abonnement identique ou différent avec PowerShell

Ce script copie la capture instantanée d’un disque managé vers un abonnement identique ou différent. Utilisez ce script dans les situations suivantes :

1. Migrez une capture instantanée du stockage Premium (Premium_LRS) vers le stockage Standard (Standard_LRS ou Standard_ZRS) pour réduire vos coûts.
1. Migrez une capture instantanée du stockage localement redondant (Premium_LRS, Standard_LRS) vers un stockage redondant interzone (Standard_ZRS) pour bénéficier d’une fiabilité accure de stockage ZRS.
1. Déplacez une capture instantanée vers un autre abonnement dans la même région pour allonger la rétention de données.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer une capture instantanée dans l’abonnement cible à l’aide de l’ID de la capture instantanée source. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Crée une configuration de capture instantanée, utilisée pour la création de captures instantanées. Elle inclut l’ID de ressource de la capture instantanée parente et un emplacement identique à l’emplacement de la capture instantanée parente.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Crée une capture instantanée à partir de la configuration de capture instantanée, du nom de capture instantanée et du nom de groupe de ressources transmis en tant que paramètres. |

## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle à partir d’une capture instantanée](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).