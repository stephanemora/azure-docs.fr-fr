---
title: Restaurer des objets blob Azure via Azure PowerShell
description: Découvrez comment restaurer des objets blob Azure à n’importe quel point dans le temps à l’aide d’Azure PowerShell.
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 653c3ce6fbfea4d890c3dc3e52fb1032a72b7eab
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108803059"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-powershell"></a>Restaurer des objets blob Azure à un point dans le temps à l’aide d’Azure PowerShell

Cet article explique comment restaurer des [objets blob](blob-backup-overview.md) à n’importe quel point dans le temps à l’aide de la sauvegarde Azure.

> [!IMPORTANT]
> La prise en charge des objets blob Azure est disponible à partir de la version Az 5.9.0.

> [!IMPORTANT]
> Avant de procéder à la restauration des objets blob Azure à l’aide de Sauvegarde Azure, consultez les [points importants](blob-restore.md#before-you-start).

Dans cet article, vous allez apprendre à :

- Restaurer des objets blob Azure à un point dans le temps

- suivre l’état de l’opération de restauration.

Nous allons faire référence au coffre de sauvegarde existant _TestBkpVault_ du groupe de ressources _testBkpVaultRG_ dans les exemples.

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restoring-azure-blobs-within-a-storage-account"></a>Restauration d’objets blob Azure dans un compte de stockage

### <a name="fetching-the-valid-time-range-for-restore"></a>Récupération de l’intervalle de temps valide pour la restauration

Étant donné que la sauvegarde opérationnelle pour les objets blob est continue, il n’existe aucun point distinct à partir duquel effectuer la restauration. Au lieu de cela, nous devons extraire la plage horaire valide dans laquelle les objets blob peuvent être restaurés à n’importe quel point dans le temps. Dans cet exemple, nous allons vérifier les plages horaires valides pour la restauration au cours des 30 derniers jours.

```azurepowershell-interactive
$startDate = (Get-Date).AddDays(-30)
$endDate = Get-Date
```

Extrayez d’abord toutes les instances à l’aide de la commande [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true), puis identifiez l’instance appropriée.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Vous pouvez également utiliser Az.Resourcegraph et la commande [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.9.0&preserve-view=true) pour effectuer des recherches parmi les instances dans de nombreux coffres et abonnements.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureBlob -ProtectionStatus ProtectionConfigured
```

Une fois l’instance identifiée, récupérez la plage de récupération appropriée à l’aide de la commande [Find-AzDataProtectionRestorableTimeRange](/powershell/module/az.dataprotection/find-azdataprotectionrestorabletimerange).

```azurepowershell-interactive
Find-AzDataProtectionRestorableTimeRange -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName -StartTime $startDate -endTime $endDate

EndTime    : 2021-04-24T08:57:36.4149422Z
ObjectType : RestorableTimeRange
StartTime  : 2021-03-25T14:27:31.0000000Z

$DesiredPIT = (Get-Date -Date "2021-04-23T02:47:02.9500000Z")
```

### <a name="preparing-the-restore-request"></a>Préparation de la demande de restauration

Une fois le point dans le temps à restaurer résolu, il existe plusieurs options de restauration. Utilisez la commande [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.9.0&preserve-view=true) pour préparer la demande de restauration avec tous les détails appropriés.

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>Restauration de tous les objets blob à un point dans le temps

L’utilisation de cette option restaure tous les objets blob de blocs dans le compte de stockage en les restaurant à l’instant dans le passé qui a été sélectionné. La restauration des comptes de stockage contenant de grandes quantités de données ou témoin d’une attrition élevée peut prendre plus de temps.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2]
```

#### <a name="restoring-selected-containers"></a>Restauration des conteneurs sélectionnés

Cette option vous permet de parcourir et de sélectionner jusqu’à 10 conteneurs à restaurer.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2] -ItemLevelRecovery -ContainersList "abc","xyz"
```

#### <a name="restoring-containers-using-a-prefix-match"></a>Restauration de conteneurs à l’aide d’une correspondance de préfixe

Cette option vous permet de restaurer un sous-ensemble d’objets blob à l’aide d’une correspondance de préfixe. Vous pouvez spécifier jusqu’à 10 plages lexicographiques d’objets blob dans un seul conteneur ou dans plusieurs conteneurs pour ramener ces objets blob à l’état qu’ils avaient à un instant dans le passé donné. Voici quelques points à prendre en compte :

- Vous pouvez utiliser une barre oblique (/) pour délimiter le nom du conteneur du préfixe de l’objet blob
- Le début de la plage spécifiée est inclusif mais la plage spécifiée est exclusive.

[En savoir plus](blob-restore.md#use-prefix-match-for-restoring-blobs) sur l’utilisation de préfixes pour restaurer des plages d’objets blob.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2] -ItemLevelRecovery -FromPrefixPattern "containerabc/aaa","containerabc/ccc" -ToPrefixPattern "containerabc/bbb","containerabc/ddd"
```

### <a name="trigger-the-restore"></a>Déclencher la restauration

Utilisez la commande [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.9.0&preserve-view=true) pour déclencher la restauration avec la demande préparée ci-dessus.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Suivi des travaux

Effectuez le suivi de tous les travaux à l’aide de la commande [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.9.0&preserve-view=true). Vous pouvez répertorier tous les travaux et extraire le détail d’un travail particulier.

Vous pouvez également utiliser Az.ResourceGraph pour effectuer le suivi de tous les travaux dans tous les coffres de sauvegarde. Utilisez la commande [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.9.0&preserve-view=true) pour récupérer le travail approprié qui peut s’appliquer à n’importe quel coffre de sauvegarde.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureBlob -Operation Restore
```

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la sauvegarde d’objet blob Azure](blob-backup-overview.md)
