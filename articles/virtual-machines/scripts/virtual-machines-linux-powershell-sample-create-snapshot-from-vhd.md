---
title: Créer une capture instantanée à partir d’un disque dur virtuel afin de créer plusieurs disques managés identiques - Exemple PowerShell
description: Exemple de script Azure PowerShell - Création d’une capture instantanée à partir d’un disque dur virtuel pour créer rapidement plusieurs disques managés identiques
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
ms.openlocfilehash: b163b5a1aad343382b9b2d22a91c1b07078c14df
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459710"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Création d’une capture instantanée à partir d’un disque dur virtuel pour créer rapidement plusieurs disques managés identiques avec PowerShell

Ce script crée une capture instantanée à partir d’un fichier de disque dur virtuel dans un compte de stockage dans le même abonnement ou un abonnement différent. Utilisez ce script pour importer un disque dur virtuel spécialisé (non généralisé/préparé avec Sysprep) dans une capture instantanée, puis utilisez la capture instantanée pour créer rapidement plusieurs disques managés identiques. Vous pouvez également vous en servir pour importer un disque dur virtuel de données dans une capture instantanée, puis utiliser la capture instantanée pour créer rapidement plusieurs disques managés.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer un disque géré à partir d’un disque dur virtuel dans un abonnement différent. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Crée une configuration de disque qui est utilisée pour la création du disque. Il inclut le type de stockage, l’emplacement, l’ID de ressource du compte de stockage dans lequel le disque dur virtuel parent est stocké et l’URI du disque dur virtuel parent. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Crée un disque à partir de la configuration de disque, du nom du disque et du nom de groupe de ressources transmis en tant que paramètres. |

## <a name="next-steps"></a>Étapes suivantes

[Créer un disque géré à partir d’une capture instantanée](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
