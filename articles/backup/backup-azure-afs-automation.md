---
title: Déployer et gérer les sauvegardes de partages de fichiers Azure à l'aide de PowerShell
description: Utiliser PowerShell pour déployer et gérer les sauvegardes de partages de fichiers Azure
services: backup
author: pvrk
manager: shivamg
keywords: PowersShell ; sauvegarde de fichiers Azure ; restauration de fichiers Azure ;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 30fc36f29a7602e2bc3f192b445474bfc50e9434
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632633"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>Utiliser PowerShell pour sauvegarder et restaurer des partages de fichiers Azure

Cet article explique comment utiliser les cmdlets Azure PowerShell pour sauvegarder et restaurer un partage de fichiers Azure à partir d'un coffre Recovery Services. Un coffre Recovery Services est une ressource Azure Resource Manager utilisée pour protéger les données et les actifs dans Sauvegarde Azure et les services Azure Site Recovery.

## <a name="concepts"></a>Concepts

Si vous ne connaissez pas le service Sauvegarde Azure, consultez l'article [Qu'est-ce que la Sauvegarde Azure ?](backup-introduction-to-azure-backup.md) afin d'obtenir une vue d'ensemble du service. Avant de commencer, familiarisez-vous avec les fonctionnalités de sauvegarde de partages de fichiers Azure présentées [ici](backup-azure-files.md).

Pour pouvoir utiliser efficacement PowerShell, il est nécessaire de comprendre la hiérarchie des objets et par où commencer.

![Hiérarchie des objets dans Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Pour voir les informations de référence sur l’applet de commande PowerShell AzureRmRecoveryServicesBackup, consultez [Sauvegarde Azure - Applets de commande de Recovery Services](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) dans la bibliothèque Azure.

## <a name="setup-and-registration"></a>Installation et inscription

> [!NOTE]
> Comme indiqué [ici](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0), la prise en charge des nouvelles fonctionnalités du module AzureRM a pris fin en novembre 2018. Par conséquent, la prise en charge de la sauvegarde des partages de fichiers Azure est assurée par le nouveau module PS « Az » à présent dans GA.

Pour commencer :

1. [Téléchargez la dernière version de PowerShell « Az »](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0) (version minimale requise : 1.0.0)

2. Rechercher les applets de commande PowerShell Azure Backup disponibles en tapant la commande suivante :

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Les alias et cmdlets pour Sauvegarde Azure, Azure Site Recovery et le coffre Recovery Services s’affichent. L’image suivante est un exemple de ce que vous allez voir. Il ne s’agit pas de la liste complète des cmdlets.

    ![Liste des services Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Connectez-vous à votre compte Azure à l'aide de **Connect-AzAccount**. Cette applet de commande permet d’afficher une page web qui vous demande les informations d’identification de votre compte :

    * Vous pouvez également inclure les informations d'identification de votre compte en tant que paramètre dans la cmdlet **Connect-AzAccount** à l'aide du paramètre **-Credential**.
    * Si vous êtes partenaire CSP travaillant pour le compte d’un locataire, spécifiez le client en tant que locataire à l’aide de son ID locataire ou de son nom de domaine principal. Par exemple :  **Connect-AzAccount -Tenant "fabrikam.com"**

4. Associez l’abonnement que vous souhaitez utiliser avec le compte, car un compte peut compter plusieurs abonnements :

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Si vous utilisez le service Sauvegarde Azure pour la première fois, vous devez utiliser la cmdlet **Register-AzResourceProvider** pour associer le fournisseur Azure Recovery Service à votre abonnement.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Vous pouvez vérifier que les fournisseurs ont été correctement inscrits à l’aide des commandes suivantes :
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    Dans la sortie de commande, **RegistrationState** doit prendre la valeur **Inscrit**. Si ce n'est pas ce cas, réexécutez la cmdlet **Register-AzResourceProvider**.

Les tâches ci-après peuvent être automatisées avec PowerShell :

* Créer un coffre Recovery Services
* Configurer la sauvegarde des partages de fichiers Azure
* Déclencher une tâche de sauvegarde
* Surveiller une tâche de sauvegarde
* Restaurer un partage de fichiers Azure
* Restaurer un fichier individuel Azure à partir d'un partage de fichiers Azure

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Les étapes suivantes vous montrent comment créer un coffre Recovery Services.

1. Le coffre Recovery Services étant une ressource Resource Manager, vous devez le placer dans un groupe de ressources. Vous pouvez utiliser un groupe de ressources existant ou en créer un avec la cmdlet **New-AzResourceGroup**. Quand vous créez un groupe de ressources, spécifiez son nom et son emplacement.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Utilisez la cmdlet **New-AzRecoveryServicesVault** pour créer le coffre Recovery Services. Spécifiez pour le coffre le même emplacement que pour le groupe de ressources.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Spécifiez le type de redondance de stockage à utiliser : [Stockage localement redondant (LRS)](../storage/common/storage-redundancy-lrs.md) ou [Stockage géo-redondant (GRS)](../storage/common/storage-redundancy-grs.md). L’exemple suivant montre que l’option -BackupStorageRedundancy pour testvault est définie sur GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Afficher les coffres dans un abonnement

Pour afficher tous les coffres de l'abonnement, utilisez **Get-AzRecoveryServicesVault** :

```powershell
Get-AzRecoveryServicesVault
```

La sortie ressemble à celle de l’exemple suivant. Notez que le paramètre ResourceGroupName et l’emplacement associés sont fournis.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

De nombreuses applets de commande Azure Backup nécessitent l’objet coffre Recovery Services en tant qu’entrée.

Pour définir le contexte du coffre, utilisez la commande **Set-AzRecoveryServicesVaultContext**. Une fois le contexte du coffre défini, il s’applique à toutes les applets de commande suivantes. L’exemple suivant définit le contexte du coffre pour le coffre *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Conformément aux instructions d'Azure PowerShell, nous prévoyons de déconseiller de définir le contexte du coffre. Nous recommandons plutôt aux utilisateurs de transmettre l'ID du coffre comme indiqué ci-dessous.

Vous pouvez également stocker/récupérer l'ID du coffre sur lequel vous souhaitez effectuer une opération PowerShell et le transmettre à la commande appropriée.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Configurer la sauvegarde d'un partage de fichiers Azure

### <a name="create-protection-policy"></a>Créer une stratégie de protection

Une stratégie de protection de la sauvegarde est associée à au moins une stratégie de rétention. La stratégie de conservation définit la durée de conservation d’un point de restauration avant sa suppression. Utilisez **Get-AzRecoveryServicesBackupRetentionPolicyObject** pour afficher la stratégie de rétention par défaut.  De même, vous pouvez utiliser **Get-AzRecoveryServicesBackupSchedulePolicyObject** pour obtenir la stratégie de planification par défaut. La cmdlet **New-AzRecoveryServicesBackupProtectionPolicy** crée un objet PowerShell contenant des informations sur la stratégie de sauvegarde. Les objets des stratégies de planification et de rétention sont utilisés comme entrées pour la cmdlet **New-AzRecoveryServicesBackupProtectionPolicy**. L’exemple suivant stocke la stratégie de planification et la stratégie de conservation dans des variables. L’exemple utilise ces variables pour définir les paramètres lors de la création d’une stratégie de protection, *NewPolicy*.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Le résultat ressemble à l’exemple suivant :

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

La stratégie « NewAFSPolicy » exploite une sauvegarde quotidienne et la conserve pendant 30 jours.

### <a name="enable-protection"></a>Activer la protection

Une fois la stratégie de protection définie, vous pouvez l'utiliser pour activer la protection du partage de fichiers Azure.

Commencez par récupérer l'objet de stratégie approprié avec la cmdlet **Get-AzRecoveryServicesBackupProtectionPolicy**. Vous pouvez utiliser cette applet de commande pour obtenir une stratégie spécifique ou pour afficher les stratégies associées à un type de charge de travail.

L'exemple suivant permet d'obtenir des stratégies pour le type de charge de travail AzureFiles.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Le résultat ressemble à l’exemple suivant :

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Le fuseau horaire du champ BackupTime dans PowerShell est UTC. Toutefois, lorsque l’heure de sauvegarde s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.
>
>

La stratégie suivante récupère la stratégie de sauvegarde intitulée « dailyafs ».

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Utilisez **Enable-AzRecoveryServicesBackupProtection** pour activer la protection de l'élément avec la stratégie donnée. Une fois que la stratégie a été associée au coffre, le flux de travail de la sauvegarde est déclenché à l’heure planifiée dans la planification de la stratégie.

L'exemple suivant active la protection du partage de fichiers Azure, « testAzureFileShare », sous le compte de stockage « testStorageAcct », avec la stratégie dailyafs.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

La commande attend que le travail de configuration de la protection soit terminé et produit une sortie similaire à celle illustrée ci-dessous.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>Déclencher une sauvegarde à la demande

Utilisez **Backup-AzRecoveryServicesBackupItem** pour déclencher un travail de sauvegarde pour un partage de fichiers Azure protégé. Récupérez le compte de stockage et le partage de fichiers qu'il contient à l'aide des commandes suivantes, puis déclenchez une sauvegarde à la demande.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

La commande renvoie un travail à suivre accompagné d'un ID, comme dans l'exemple suivant.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Lors des sauvegardes, nous utilisons les captures instantanées des partages de fichiers Azure. Par conséquent, le travail est généralement terminé au moment où la commande renvoie cette sortie.

### <a name="modify-protection-policy"></a>Modifier la stratégie de protection

Si vous souhaitez modifier la stratégie avec laquelle le partage de fichiers Azure est protégé, utilisez **Enable-AzRecoveryServicesBackupProtection** avec l'élément de sauvegarde et la nouvelle stratégie de protection appropriés.

Les exemples suivants modifient la stratégie de protection de « testAzureFS » en remplaçant « dailyafs » par « monthlyafs ».

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-sharesazure-files"></a>Restaurer des partages de fichiers Azure/fichiers Azure

Vous pouvez intégralement restaurer un partage de fichiers à son emplacement d'origine ou à un autre emplacement. Vous pouvez également restaurer des fichiers individuels issus du partage de fichiers.

### <a name="fetching-recovery-points"></a>Extraire des points de récupération

Utilisez la cmdlet **Get-AzRecoveryServicesBackupRecoveryPoint** afin de répertorier tous les points de récupération correspondant à l'élément de sauvegarde. Dans le script suivant, la variable **$rp**est un tableau de points de récupération des 7 derniers jours pour l’élément de sauvegarde sélectionné. Le tableau est trié dans l’ordre chronologique inverse, le point de récupération le plus récent détenant l’index 0. Utilisez l'indexation de tableau PowerShell standard pour sélectionner le point de récupération. Dans l’exemple, $rp[0] sélectionne le dernier point de récupération.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Le résultat ressemble à l’exemple suivant :

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Une fois le point de récupération pertinent sélectionné, procédez à la restauration du partage de fichiers/fichier à l'emplacement d'origine ou à un autre emplacement, comme expliqué ci-dessous.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Restaurer des partages de fichiers Azure à un autre emplacement

#### <a name="restoring-an-azure-file-share"></a>Restaurer un partage de fichiers Azure

Identifiez l'emplacement en fournissant les informations suivantes :

* ***TargetStorageAccountName*** : Compte de stockage sur lequel le contenu sauvegardé est restauré. Le compte de stockage cible doit se trouver au même emplacement que le coffre.
* ***TargetFileShareName*** : Partages de fichiers du compte de stockage cible sur lesquels le contenu sauvegardé est restauré.
* ***TargetFolder*** : Dossier situé sous le partage de fichiers sur lequel les données sont restaurées. Si le contenu sauvegardé doit être restauré dans le dossier racine, indiquez les valeurs du dossier cible sous forme de chaîne vide.
* ***ResolveConflict*** : Instruction en cas de conflit avec les données restaurées. Accepte « Remplacer » ou « Ignorer ».

Fournissez ces paramètres à la commande de restauration pour restaurer un partage de fichiers sauvegardé à un autre emplacement.

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

La commande renvoie un travail avec un ID à suivre, comme indiqué dans l'exemple suivant.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restoring-an-azure-file"></a>Restaurer un fichier Azure

Si vous souhaitez restaurer un fichier individuel plutôt qu'un partage de fichiers entier, vous devez identifier le fichier en question en fournissant les paramètres suivants.

* ***TargetStorageAccountName*** : Compte de stockage sur lequel le contenu sauvegardé est restauré. Le compte de stockage cible doit se trouver au même emplacement que le coffre.
* ***TargetFileShareName*** : Partages de fichiers du compte de stockage cible sur lesquels le contenu sauvegardé est restauré.
* ***TargetFolder*** : Dossier situé sous le partage de fichiers sur lequel les données sont restaurées. Si le contenu sauvegardé doit être restauré dans le dossier racine, indiquez les valeurs du dossier cible sous forme de chaîne vide.
* ***SourceFilePath*** : Chemin d'accès absolu du fichier, à restaurer dans le partage de fichiers, sous forme de chaîne. Il s'agit du même chemin que celui utilisé dans la cmdlet PS ```Get-AzStorageFile```.
* ***SourceFileType*** : Indique si un répertoire ou un fichier est sélectionné. Accepte « Répertoire » ou « Fichier ».
* ***ResolveConflict*** : Instruction en cas de conflit avec les données restaurées. Accepte « Remplacer » ou « Ignorer ».

Comme vous pouvez le constater, les paramètres supplémentaires ne concernent que le fichier à restaurer.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Un travail à suivre accompagné d'un ID est également renvoyé, comme indiqué ci-dessus.

### <a name="restore-azure-file-shares-to-original-location"></a>Restaurer des partages de fichiers Azure à l'emplacement d'origine

En cas de restauration à l'emplacement d'origine, il n'est pas nécessaire de spécifier tous les paramètres liés à la destination/cible. Seul ```ResolveConflict``` ​​doit être fourni.

#### <a name="overwrite-an-azure-file-share"></a>Remplacer un partage de fichiers Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Remplacer un fichier Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Suivi des travaux de sauvegarde et de restauration

Les opérations de sauvegarde et de restauration à la demande renvoient un travail accompagné d'un ID, comme indiqué [ci-dessus](#trigger-an-on-demand-backup). Utilisez la cmdlet ```Get-AzRecoveryServicesBackupJobDetails``` pour suivre l'avancement du travail et obtenir plus de détails.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support
```
