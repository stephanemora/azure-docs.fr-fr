---
title: Créer un disque managé à partir d’un fichier VHD dans un compte de stockage - Exemple PowerShell
description: Exemple de script Azure PowerShell - Créer un disque géré à partir d’un fichier de disque dur virtuel dans un compte de stockage dans le même abonnement ou dans un abonnement différent
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
ms.openlocfilehash: bec459079bfbc85d339e094e2acdef4fefbad0c7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459740"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Créer un disque géré à partir d’un fichier de disque dur virtuel dans un compte de stockage dans le même abonnement ou dans un abonnement différent avec PowerShell

Ce script crée un disque géré à partir d’un fichier de disque dur virtuel dans un compte de stockage dans le même abonnement ou un abonnement différent. Utilisez ce script pour importer un VHD spécialisé (non généralisé/préparé avec Sysprep) vers un disque de système d’exploitation managé pour créer une machine virtuelle. Vous pouvez également vous en servir pour importer un disque dur virtuel de données dans un disque de données géré.

Ne créez pas plusieurs disques managés identiques à partir d’un fichier de disque dur virtuel dans un court laps de temps. Pour créer des disques managés à partir d’un fichier de disque dur virtuel, la capture instantanée de blob du fichier de disque dur virtuel est créée, puis utilisée pour créer des disques managés. Seule une capture instantanée de blob peut être créée par minute, sous peine d’entraîner des échecs de création de disque en raison d’une limitation. Pour éviter cette limitation, créez une [capture instantanée gérée à partir du fichier de disque dur virtuel](virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), puis utilisez-la pour créer rapidement plusieurs disques managés.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer un disque géré à partir d’un disque dur virtuel dans un abonnement différent. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Crée une configuration de disque qui est utilisée pour la création du disque. Il inclut le type de stockage, l’emplacement, l’ID de ressource du compte de stockage dans lequel le disque dur virtuel parent est stocké et l’URI du disque dur virtuel parent. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Crée un disque à partir de la configuration de disque, du nom du disque et du nom de groupe de ressources transmis en tant que paramètres. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
