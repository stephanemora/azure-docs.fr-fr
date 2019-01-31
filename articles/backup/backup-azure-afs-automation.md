---
title: Déployer et gérer des sauvegardes de partages de fichiers Azure à l’aide de PowerShell
description: Utiliser PowerShell pour déployer et gérer les sauvegardes de partages de fichiers Azure
services: backup
author: pvrk
manager: shivamg
keywords: PowerShell ; sauvegarde Azure Files ; restauration Azure Files ;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 912336d697e8f7b5d9c71080ec9a052ca562da4b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101128"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>Utiliser PowerShell pour sauvegarder et restaurer des partages de fichiers Azure

Cet article explique comment utiliser les cmdlets Azure PowerShell pour sauvegarder et restaurer un partage de fichiers Azure à partir d'un coffre Recovery Services. Un coffre Recovery Services est une ressource Azure Resource Manager utilisée pour protéger les données et les actifs dans Sauvegarde Azure et Azure Site Recovery.

## <a name="concepts"></a>Concepts

Si vous ne connaissez pas Sauvegarde Azure, consultez la page de [présentation de Sauvegarde Azure](backup-introduction-to-azure-backup.md) afin d’obtenir une vue d’ensemble du service. Avant de commencer, consultez les fonctionnalités en préversion qui sont utilisées pour sauvegarder des partages de fichiers Azure dans [Sauvegarder des partages de fichiers Azure](backup-azure-files.md).

Pour pouvoir utiliser efficacement PowerShell, il est nécessaire de comprendre la hiérarchie des objets et par où commencer.

![Hiérarchie des objets dans Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Pour voir les informations de référence sur la cmdlet PowerShell **AzureRmRecoveryServicesBackup**, consultez [Sauvegarde Azure - Cmdlets Recovery Services](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) dans la bibliothèque Azure.

## <a name="setup-and-registration"></a>Installation et inscription

> [!NOTE]
> Comme indiqué dans [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0), la prise en charge des nouvelles fonctionnalités du module AzureRM a pris fin en novembre 2018. La prise en charge est fournie pour la sauvegarde de partages de fichiers Azure avec le nouveau module Az PowerShell qui est à présent en disponibilité générale.

Pour commencer, procédez comme suit.

1. [Téléchargez la dernière version d’Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0). La version 1.0.0 est la version minimale requise.

2. Trouvez les cmdlets **PowerShell Sauvegarde Azure** disponibles en tapant la commande suivante.

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Les alias et cmdlets pour Sauvegarde Azure, Azure Site Recovery et le coffre Recovery Services s’affichent. L’image suivante est un exemple de ce que vous allez voir. Il ne s’agit pas de la liste complète des cmdlets.

    ![Liste des cmdlets Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Connectez-vous à votre compte Azure à l’aide de **Connect-AzAccount**. Cette cmdlet permet d’afficher une page web qui vous demande les informations d’identification de votre compte :

    * Vous pouvez également inclure les informations d’identification de votre compte en tant que paramètre dans la cmdlet **Connect-AzAccount** à l’aide du paramètre **-Credential**.
    * Si vous êtes partenaire CSP travaillant pour le compte d’un locataire, spécifiez le client en tant que locataire à l’aide de son ID locataire ou de son nom de domaine principal. Par exemple **Connect-AzAccount -Tenant** fabrikam.com.

4. Associez l’abonnement que vous souhaitez utiliser avec le compte, car un compte peut compter plusieurs abonnements.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Si vous utilisez le service Sauvegarde Azure pour la première fois, utilisez la cmdlet **Register-AzResourceProvider** pour associer le fournisseur Azure Recovery Service à votre abonnement.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Vérifiez que les fournisseurs ont été correctement inscrits à l’aide des commandes suivantes.
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    Dans la sortie de commande, **RegistrationState** doit maintenant avoir la valeur **Registered (Inscrit)**. Si ce n’est pas ce cas, réexécutez la cmdlet **Register-AzResourceProvider**.

Les tâches ci-après peuvent être automatisées avec PowerShell :

* Créez un coffre Recovery Services.
* Configurez la sauvegarde des partages de fichiers Azure.
* Déclenchez un travail de sauvegarde.
* Surveillez un travail de sauvegarde.
* Restaurez un partage de fichiers Azure.
* Restaurez un fichier individuel Azure à partir d’un partage de fichiers Azure.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Pour créer un coffre Recovery Services, procédez comme suit :

1. Le coffre Recovery Services étant une ressource Resource Manager, vous devez le placer dans un groupe de ressources. Vous pouvez utiliser un groupe de ressources existant ou en créer un avec la cmdlet **New-AzResourceGroup**. Quand vous créez un groupe de ressources, spécifiez son nom et son emplacement.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Utilisez la cmdlet **New-AzRecoveryServicesVault** pour créer le coffre Recovery Services. Spécifiez pour le coffre le même emplacement que pour le groupe de ressources.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Spécifiez le type de redondance de stockage à utiliser. Vous pouvez utiliser le [stockage localement redondant](../storage/common/storage-redundancy-lrs.md) ou le [stockage géoredondant](../storage/common/storage-redundancy-grs.md). L’exemple suivant montre que l’option **-BackupStorageRedundancy** pour **testvault** est définie sur **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Afficher les coffres dans un abonnement

Pour afficher tous les coffres de l’abonnement, utilisez **Get-AzRecoveryServicesVault**.

```powershell
Get-AzRecoveryServicesVault
```

Le résultat ressemble à l’exemple qui suit. Notez que le paramètre **ResourceGroupName** et l’**emplacement** associés sont fournis.

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

Pour définir le contexte du coffre, utilisez la commande **Set-AzRecoveryServicesVaultContext**. Une fois le contexte du coffre défini, il s’applique à toutes les cmdlets suivantes. L’exemple suivant définit le contexte pour le coffre **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Conformément aux instructions d’Azure PowerShell, nous prévoyons de déprécier la définition du contexte de coffre. Nous recommandons plutôt aux utilisateurs de transmettre l’ID du coffre comme indiqué dans les instructions suivantes.

Vous pouvez également stocker ou récupérer l’ID du coffre sur lequel vous souhaitez effectuer une opération PowerShell et le transmettre à la commande appropriée.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Configurer la sauvegarde d'un partage de fichiers Azure

### <a name="create-a-protection-policy"></a>Création d’une stratégie de protection

Une stratégie de protection de la sauvegarde est associée à au moins une stratégie de rétention. Une stratégie de rétention définit la durée de conservation d’un point de restauration avant sa suppression. Utilisez **Get-AzRecoveryServicesBackupRetentionPolicyObject** pour afficher la stratégie de rétention par défaut. 

De même, vous pouvez utiliser **Get-AzRecoveryServicesBackupSchedulePolicyObject** pour obtenir la stratégie de planification par défaut. La cmdlet **New-AzRecoveryServicesBackupProtectionPolicy** crée un objet PowerShell contenant des informations sur la stratégie de sauvegarde. Les objets des stratégies de planification et de rétention sont utilisés comme entrées pour la cmdlet **New-AzRecoveryServicesBackupProtectionPolicy**. 

L’exemple suivant stocke la stratégie de planification et la stratégie de conservation dans des variables. L’exemple utilise ces variables pour définir les paramètres lors de la création d’une stratégie de protection **NewPolicy**.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Le résultat ressemble à l’exemple qui suit.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

La stratégie **NewAFSPolicy** effectue une sauvegarde quotidienne et la conserve pendant 30 jours.

### <a name="enable-protection"></a>Activer la protection

Une fois que vous avez défini la stratégie de protection, vous pouvez l’utiliser pour activer la protection du partage de fichiers Azure.

Commencez par récupérer l’objet de stratégie approprié avec la cmdlet **Get-AzRecoveryServicesBackupProtectionPolicy**. Utilisez cette cmdlet pour obtenir une stratégie spécifique ou pour afficher les stratégies associées à un type de charge de travail.

L’exemple suivant permet d’obtenir des stratégies pour le type de charge de travail **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Le résultat ressemble à l’exemple qui suit.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Le fuseau horaire du champ **BackupTime** dans PowerShell est au format UTC. Lorsque l’heure de sauvegarde s’affiche dans le portail Azure, elle est alignée sur votre fuseau horaire.
>
>

La stratégie suivante récupère la stratégie de sauvegarde intitulée **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Utilisez **Enable-AzRecoveryServicesBackupProtection** pour activer la protection de l'élément avec la stratégie donnée. Une fois que la stratégie a été associée au coffre, le flux de travail de la sauvegarde est déclenché à l’heure planifiée dans la planification de la stratégie.

L’exemple suivant active la protection du partage de fichiers Azure **testAzureFileShare** sous le compte de stockage **testStorageAcct**, avec la stratégie **dailyafs**.

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

Utilisez **Backup-AzRecoveryServicesBackupItem** pour déclencher un travail de sauvegarde pour un partage de fichiers Azure protégé. Récupérez le compte de stockage et le partage de fichiers qu’il contient à l’aide des commandes suivantes, puis déclenchez une sauvegarde à la demande.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

La commande retourne un travail avec un ID à suivre, comme indiqué dans l’exemple suivant.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Lors des sauvegardes, nous utilisons les captures instantanées de partages de fichiers Azure. Par conséquent, le travail est généralement terminé au moment où la commande retourne cette sortie.

### <a name="modify-the-protection-policy"></a>Modifier la stratégie de protection

Pour modifier la stratégie avec laquelle le partage de fichiers Azure est protégé, utilisez **Enable-AzRecoveryServicesBackupProtection** avec l’élément de sauvegarde approprié et la nouvelle stratégie de protection.

Les exemples suivants modifient la stratégie de protection de **testAzureFS** en remplaçant **dailyafs** par **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-azure-files"></a>Restaurer des partages de fichiers Azure et des fichiers Azure

Vous pouvez intégralement restaurer un partage de fichiers à son emplacement d’origine ou à un autre emplacement. Vous pouvez également restaurer des fichiers individuels issus du partage de fichiers.

### <a name="fetch-recovery-points"></a>Récupérer des points de récupération

Utilisez la cmdlet **Get-AzRecoveryServicesBackupRecoveryPoint** afin de répertorier tous les points de récupération correspondant à l'élément de sauvegarde. Dans le script suivant, la variable **$rp** est un tableau de points de récupération des 7 derniers jours pour l’élément de sauvegarde sélectionné. Le tableau est trié dans l’ordre chronologique inverse, le point de récupération le plus récent détenant l’index **0**. Utilisez l'indexation de tableau PowerShell standard pour sélectionner le point de récupération. Dans l’exemple, **$rp[0]** sélectionne le dernier point de récupération.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Le résultat ressemble à l’exemple qui suit.

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

Une fois le point de récupération pertinent sélectionné, procédez à la restauration du partage de fichiers/fichier à l’emplacement d’origine ou à un autre emplacement, comme expliqué ici.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Restaurer des partages de fichiers Azure à un autre emplacement

#### <a name="restore-an-azure-file-share"></a>Restaurer un partage de fichiers Azure

Identifiez l'emplacement en fournissant les informations suivantes :

* **TargetStorageAccountName** : Compte de stockage sur lequel le contenu sauvegardé est restauré. Le compte de stockage cible doit se trouver au même emplacement que le coffre.
* **TargetFileShareName** : Partages de fichiers du compte de stockage cible sur lesquels le contenu sauvegardé est restauré.
* **TargetFolder** : Dossier situé sous le partage de fichiers sur lequel les données sont restaurées. Si le contenu sauvegardé doit être restauré dans le dossier racine, indiquez les valeurs du dossier cible sous forme de chaîne vide.
* **ResolveConflict** : Instruction en cas de conflit avec les données restaurées. Accepte **Remplacer** ou **Ignorer**.

Fournissez ces paramètres à la commande de restauration pour restaurer un partage de fichiers sauvegardé à un autre emplacement.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

La commande retourne un travail avec un ID à suivre, comme indiqué dans l’exemple suivant.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

#### <a name="restore-an-azure-file"></a>Restaurer un fichier Azure

Pour restaurer un fichier individuel plutôt qu’un partage de fichiers entier, identifiez de façon unique le fichier en question en fournissant les paramètres suivants :

* **TargetStorageAccountName** : Compte de stockage sur lequel le contenu sauvegardé est restauré. Le compte de stockage cible doit se trouver au même emplacement que le coffre.
* **TargetFileShareName** : Partages de fichiers du compte de stockage cible sur lesquels le contenu sauvegardé est restauré.
* **TargetFolder** : Dossier situé sous le partage de fichiers sur lequel les données sont restaurées. Si le contenu sauvegardé doit être restauré dans le dossier racine, indiquez les valeurs du dossier cible sous forme de chaîne vide.
* **SourceFilePath** : Chemin d'accès absolu du fichier, à restaurer dans le partage de fichiers, sous forme de chaîne. Ce chemin d’accès est le même que celui utilisé dans la cmdlet PowerShell **Get-AzStorageFile**.
* **SourceFileType** : Indique si un répertoire ou un fichier est sélectionné. Accepte **Répertoire** ou **Fichier**.
* **ResolveConflict** : Instruction en cas de conflit avec les données restaurées. Accepte **Remplacer** ou **Ignorer**.

Les paramètres supplémentaires ne concernent que le fichier à restaurer.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

La commande retourne également un travail avec un ID à suivre, comme indiqué précédemment.

### <a name="restore-azure-file-shares-to-the-original-location"></a>Restaurer des partages de fichiers Azure à l’emplacement d’origine

En cas de restauration à l’emplacement d’origine, il n’est pas nécessaire de spécifier tous les paramètres liés à la destination/cible. Seul **ResolveConflict** doit être fourni.

#### <a name="overwrite-an-azure-file-share"></a>Remplacer un partage de fichiers Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Remplacer un fichier Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Suivi des travaux de sauvegarde et de restauration

Les opérations de sauvegarde et de restauration à la demande retournent un travail accompagné d’un ID, comme indiqué dans le section précédente [Déclencher une sauvegarde à la demande](#trigger-an-on-demand-backup). Utilisez la cmdlet **Get-AzRecoveryServicesBackupJobDetails** pour suivre l’avancement du travail et obtenir plus de détails.

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
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
