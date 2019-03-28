---
title: 'Exemple de Script Azure PowerShell : sauvegarder une machine virtuelle Azure | Microsoft Docs'
description: 'Exemple de Script Azure PowerShell : sauvegarder une machine virtuelle Azure'
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bc54832b300bf7a70d067f07b9eb7cc67404f2e7
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496801"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Sauvegarder une machine virtuelle Azure chiffrée avec PowerShell

Ce script crée un coffre Recovery Services avec un stockage géoredondant (GRS) pour une machine virtuelle Azure chiffrée. La stratégie de protection par défaut est appliquée au coffre. La stratégie génère une sauvegarde quotidienne de la machine virtuelle et conserve chaque sauvegarde pendant 30 jours. En outre, le script déclenche le point de récupération initial pour la machine virtuelle et conserve ce point de récupération pendant 365 jours.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.


| Commande | Notes | 
|---|---| 
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. | 
| [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Crée un coffre Recovery Services pour stocker les sauvegardes. | 
| [Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties) | Définit les propriétés de stockage des sauvegardes sur le coffre Recovery Services. | 
| [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Crée une stratégie de protection à l’aide de la stratégie de planification et de la stratégie de rétention dans le coffre Recovery Services. | 
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Définit les autorisations sur Key Vault pour accorder au principal de service l’accès aux clés de chiffrement. | 
| [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Active la sauvegarde d’un élément avec une stratégie de protection de Sauvegarde spécifiée. | 
| [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Modifie une stratégie de protection de Sauvegarde existante. | 
| [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Démarre une sauvegarde pour un élément de Sauvegarde Azure protégé qui n’est pas lié à la planification de sauvegarde. |
| [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Attend la fin d’un travail de Sauvegarde Azure. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. | 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

