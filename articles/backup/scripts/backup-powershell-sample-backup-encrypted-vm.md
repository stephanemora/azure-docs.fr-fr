---
title: Exemple de script PowerShell – Sauvegarder une machine virtuelle Azure
description: Cet article explique comment utiliser un exemple de script Azure PowerShell pour sauvegarder une machine virtuelle Azure.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: a60320c165499ed1fb02fa544deec0e304d447df
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513640"
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
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Crée un coffre Recovery Services pour stocker les sauvegardes. |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Définit les propriétés de stockage des sauvegardes sur le coffre Recovery Services. |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Crée une stratégie de protection à l’aide de la stratégie de planification et de la stratégie de rétention dans le coffre Recovery Services. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Définit les autorisations sur Key Vault pour accorder au principal de service l’accès aux clés de chiffrement. |
| [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Active la sauvegarde d’un élément avec une stratégie de protection de Sauvegarde spécifiée. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Modifie une stratégie de protection de Sauvegarde existante. |
| [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Démarre une sauvegarde pour un élément de Sauvegarde Azure protégé qui n’est pas lié à la planification de sauvegarde. |
| [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Attend la fin d’un travail de Sauvegarde Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/new-azureps-module-az).
