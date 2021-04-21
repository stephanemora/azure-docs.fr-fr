---
title: Restaurer des disques managés Azure via Azure PowerShell
description: Découvrez comment restaurer des disques managés Azure à l’aide d’Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520035"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Restaurer des disques managés Azure à l’aide d’Azure PowerShell

Cet article explique comment restaurer des [Disques managés Azure](../virtual-machines/managed-disks-overview.md) à partir d’un point de restauration créé par la Sauvegarde Azure.

Actuellement, l’option OLR (Original-Location Recovery) de restauration par remplacement du disque source existant à partir duquel les sauvegardes ont été effectuées n’est pas prise en charge. Il est possible d’effectuer une restauration à partir d’un point de récupération pour créer un disque dans le même groupe de ressources que celui du disque source à partir duquel les sauvegardes ont été effectuées ou dans un autre groupe de ressources. C’est ce que l’on appelle l’option ALR (Alternate-Location Recovery), qui permet de conserver à la fois le disque source et le (nouveau) disque restauré.

Dans cet article, vous allez apprendre à :

- effectuer une restauration pour créer un disque ;

- suivre l’état de l’opération de restauration.

Nous allons faire référence au coffre de sauvegarde existant « TestBkpVault » du groupe de ressources « testBkpVaultRG » dans les exemples

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Restauration visant à créer un disque

### <a name="setting-up-permissions"></a>Configurer les autorisations

Le coffre Sauvegarde utilise l’identité managée pour accéder à d’autres ressources Azure. Il est nécessaire, pour effectuer une restauration à partir d’une sauvegarde, que l’identité managée du coffre Sauvegarde dispose d’un ensemble d’autorisations sur le groupe de ressources dans lequel le disque doit être restauré.

Le coffre Sauvegarde utilise une identité managée affectée par le système, limitée à une par ressource et liée au cycle de vie de celle-ci. Pour accorder des autorisations à l’identité managée, vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) Azure. L’identité managée est un principal de service d’un type spécial qui ne peut être utilisé qu’avec des ressources Azure. Découvrez-en plus sur les [identités managées](../active-directory/managed-identities-azure-resources/overview.md).

Affectez les autorisations appropriées à l’identité managée affectée par le système pour le coffre-fort correspondant au groupe de ressources cible où les disques vont être restaurés/créés, comme indiqué [ici](restore-managed-disks.md#restore-to-create-a-new-disk).

### <a name="fetching-the-relevant-recovery-point"></a>Extraction du point de récupération approprié

Extrayez toutes les instances à l’aide de la commande [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true), puis identifiez l’instance appropriée.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Vous pouvez également utiliser **Az.Resourcegraph** et la commande [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) pour effectuer des recherches parmi les instances dans de nombreux coffres et abonnements.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Une fois l’instance identifiée, extrayez le point de récupération approprié.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Préparation de la demande de restauration

Construisez l’ID ARM du disque à créer avec le groupe de ressources cible, auquel des autorisations ont été affectées comme indiqué [ci-dessus](#setting-up-permissions), et le nom de disque nécessaire. Par exemple, vous pouvez nommer un disque **PSTestDisk2** sous le groupe de ressources **targetrg** avec un abonnement distinct.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Utilisez la commande [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) pour préparer la demande de restauration avec tous les détails appropriés.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Déclencher la restauration

Utilisez la commande [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) pour déclencher la restauration avec la demande préparée ci-dessus.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Suivi des travaux

Effectuez le suivi de tous les travaux à l’aide de la commande [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true). Vous pouvez lister tous les travaux et récupérer les détails d’un travail en particulier.

Vous pouvez également utiliser la commande **Az.ResourceGraph** pour effectuer le suivi de tous les travaux dans l’ensemble des coffres de sauvegarde. Utilisez la commande [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) pour obtenir le travail approprié, qui peut se trouver dans n’importe quel coffre de sauvegarde.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Étapes suivantes

- [FAQ sur la sauvegarde de disque Azure](disk-backup-faq.yml)
