---
title: 'Sauvegarde Azure : Sauvegarder et restaurer des bases de données SQL dans des machines virtuelles Azure à l’aide de la sauvegarde Azure et PowerShell'
description: Sauvegarder et restaurer des bases de données SQL dans des machines virtuelles Azure à l’aide de la sauvegarde Azure et PowerShell.
services: backup
author: pvrk
manager: vijayts
keywords: Azure Backup; SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 6a2e065466ab4426a6472b64fae19d264ff8dd81
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734218"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Sauvegarder et restaurer des bases de données SQL dans des machines virtuelles Azure avec PowerShell

Cet article explique comment utiliser Azure PowerShell pour sauvegarder et restaurer une base de données SQL au sein d’une machine virtuelle Azure à l’aide [sauvegarde Azure](backup-overview.md) coffre Recovery Services.

Ce didacticiel explique comment :

> [!div class="checklist"]
> * Configuration de PowerShell et inscrire le fournisseur Azure Recovery Services.
> * Créez un coffre Recovery Services.
> * Configurer la sauvegarde pour la base de données SQL au sein d’une machine virtuelle Azure.
> * Exécuter un travail de sauvegarde.
> * Restaurer une base de données SQL sauvegardées.
> * Surveiller la sauvegarde et restauration des travaux.

## <a name="before-you-start"></a>Avant de commencer

* [En savoir plus](backup-azure-recovery-services-vault-overview.md) sur les coffres Recovery Services.
* En savoir plus sur les fonctionnalités pour [sauvegarde des bases de données SQL au sein de machines virtuelles Azure](backup-azure-sql-database.md#before-you-start).
* Passez en revue la hiérarchie d’objets PowerShell pour les Services de récupération.

### <a name="recovery-services-object-hierarchy"></a>Hiérarchie des objets dans Recovery Services

La hiérarchie d’objets est résumée dans le diagramme suivant.

![Hiérarchie des objets dans Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Examinez le **Az.RecoveryServices** [référence d’applet de commande](/powershell/module/az.recoveryservices) référence dans la bibliothèque Azure.

### <a name="set-up-and-install"></a>Configurer et installer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configurer PowerShell, procédez comme suit :

1. [Téléchargez la dernière version d’Az PowerShell](/powershell/azure/install-az-ps). La version minimale requise est 1.5.0.

2. Rechercher les applets de commande PowerShell de sauvegarde Azure avec cette commande :

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Passez en revue les alias et les applets de commande pour la sauvegarde Azure et le coffre Recovery Services. Voici un exemple de ce que vous pouvez voir. Il n’est pas une liste complète des applets de commande.

    ![Liste des cmdlets Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Connectez-vous à votre compte Azure avec **Connect-AzAccount**.
5. Dans la page web qui s’affiche, vous êtes invité à entrer vos informations d’identification de compte.

    * Alternativement, vous pouvez inclure vos informations d’identification de compte en tant que paramètre dans le **Connect-AzAccount** applet de commande avec **-informations d’identification**.
    * Si vous êtes un partenaire CSP pour un client, spécifiez le client en tant que client, à l’aide de leur nom de domaine principal ID de locataire ou client. Par exemple **Connect-AzAccount -Tenant** fabrikam.com.

6. Associez l’abonnement que vous souhaitez utiliser avec le compte, car un compte peut compter plusieurs abonnements.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Si vous utilisez le service Sauvegarde Azure pour la première fois, utilisez la cmdlet **Register-AzResourceProvider** pour associer le fournisseur Azure Recovery Service à votre abonnement.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Vérifiez que les fournisseurs inscrits correctement :

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Dans la sortie de commande, vérifiez que **RegistrationState** devient **Registered**. Si elle ne, exécutez le **Register-AzResourceProvider** applet de commande à nouveau.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Pour créer un coffre Recovery Services, procédez comme suit :

Le coffre Recovery Services étant une ressource Resource Manager, vous devez le placer dans un groupe de ressources. Vous pouvez utiliser un groupe de ressources existant ou en créer un avec la cmdlet **New-AzResourceGroup**. Quand vous créez un groupe de ressources, spécifiez son nom et son emplacement.

1. Un coffre est placé dans un groupe de ressources. Si vous n’avez pas une ressource existante regrouper, créez-en un avec le [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Dans cet exemple, nous créer un nouveau groupe de ressources dans la région ouest des États-Unis.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Utilisez le [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) applet de commande pour créer le coffre. Spécifiez pour le coffre le même emplacement que pour le groupe de ressources.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Spécifiez le type de redondance à utiliser pour le stockage du coffre.

    * Vous pouvez utiliser le [stockage localement redondant](../storage/common/storage-redundancy-lrs.md) ou le [stockage géoredondant](../storage/common/storage-redundancy-grs.md).
    * L’exemple suivant définit la **- BackupStorageRedundancy** option pour le[Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd pour **testvault** défini sur  **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Afficher les coffres dans un abonnement

Pour afficher tous les coffres de l’abonnement, utilisez [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

La sortie est similaire à ce qui suit. Le groupe de ressources associé et l’emplacement sont fournis.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Définir le contexte du coffre

Store de l’objet de coffre dans une variable et définissez le contexte du coffre.

* Nombreuses applets de commande Azure Backup nécessitent l’objet de coffre Recovery Services en tant qu’entrée, par conséquent, il est pratique de stocker l’objet de coffre dans une variable.
* Le contexte du coffre spécifie le type de données protégées dans le coffre. Définir avec [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Une fois que le contexte est défini, il s’applique à toutes les applets de commande suivantes.

L’exemple suivant définit le contexte pour le coffre **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Extraire l’ID de coffre

Nous prévoyons de déconseiller le contexte du coffre définissant conformément aux instructions d’Azure PowerShell. Au lieu de cela, vous pouvez stocker ou récupérer l’ID du coffre et passez-le à des commandes appropriées, comme suit :

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurer une stratégie de sauvegarde

Une stratégie de sauvegarde spécifie la planification des sauvegardes et la durée pendant laquelle les points de récupération de sauvegarde doivent être conservées :

* Une stratégie de sauvegarde est associée à au moins une stratégie de rétention. Une stratégie de rétention définit la durée de conservation d’un point de restauration avant sa suppression.
* La rétention de stratégie de sauvegarde par défaut à l’aide de vue [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* La planification de stratégie de sauvegarde par défaut à l’aide de vue [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Vous utilisez le [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) applet de commande pour créer une stratégie de sauvegarde. Vous entrez les objets de stratégie de planification et de rétention.

L’exemple suivant stocke la stratégie de planification et la stratégie de conservation dans des variables. Il utilise ensuite ces variables en tant que paramètres pour une nouvelle stratégie (**NewSQLPolicy**). **NewSQLPolicy** effectue une sauvegarde quotidienne « complète », la conserve pendant 180 jours et effectue une sauvegarde de journal toutes les 2 heures

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

La sortie est similaire à ce qui suit.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>Activer la sauvegarde

### <a name="registering-the-sql-vm"></a>L’inscription de la machine virtuelle SQL

Pour les sauvegardes de machines virtuelles Azure et les partages de fichiers Azure, service de sauvegarde peut se connecter à ces ressources Azure Resource Manager et extraire les détails pertinents. Étant donné que SQL est une application au sein d’une machine virtuelle Azure, service de sauvegarde a besoin d’autorisation d’accéder à l’application et de récupérer les détails nécessaires. Pour ce faire, vous devez *'register'* la machine virtuelle Azure qui contient l’application SQL avec un coffre Recovery services. Une fois que vous enregistrez une VM SQL avec un coffre, vous pouvez protéger les bases de données SQL à ce coffre uniquement. Utilisez [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) applet de commande PS pour inscrire la machine virtuelle.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

La commande retourne un conteneur sauvegarde de cette ressource et l’état est « enregistré »

> [!NOTE]
> Si le paramètre force n’est pas spécifié, l’utilisateur est invité à confirmer avec un texte 'voulez-vous désactiver la protection pour ce conteneur'. Veuillez ignorer ce texte et dire « Y » pour confirmer. Il s’agit d’un problème connu et nous travaillons à supprimer le texte et la configuration requise pour le paramètre force

### <a name="fetching-sql-dbs"></a>Récupération des bases de données SQL

Une fois l’inscription est effectuée, le service de sauvegarde sera en mesure de répertorier tous les composants SQL disponibles au sein de la machine virtuelle. Pour afficher tous les composants SQL encore être sauvegardées pour ce coffre, utilisez [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) applet de commande PS

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

La sortie affiche tous les composants SQL non protégés sur toutes les machines virtuelles de SQL inscrit auprès de ce coffre avec le Type d’élément et le nom du serveur. Vous pouvez filtrer davantage à une VM SQL particulier en passant le '-conteneur ' paramètre ou utilisez la combinaison de « Name » et « ServerName » avec ItemType indicateur pour arriver à un élément unique de SQL.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Configuration de la sauvegarde

Maintenant que nous disposons de la base de données SQL requis et la stratégie avec lequel il doit être sauvegardée, nous pouvons utiliser le [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) applet de commande pour configurer la sauvegarde pour cette base de données SQL.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

La commande attend jusqu'à ce que la sauvegarde de la configuration est terminée et renvoie le résultat suivant.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>L’extraction de nouvelles bases de données SQL

Une fois que l’ordinateur est inscrit, le service sauvegarde extrait les détails des bases de données puis disponibles. Si l’utilisateur ajoute des instances des bases de données SQL/SQL à l’ordinateur inscrit plus tard, vous devez déclencher manuellement le service de sauvegarde pour effectuer une nouvelle « consultation » pour obtenir tous les non protégés bases de données (y compris ceux qui vient d’être ajouté) à nouveau. Utilisez le [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) applet de commande PS sur la VM SQL pour effectuer une nouvelle demande. La commande attend jusqu'à ce que l’opération est terminée. Utiliser ultérieurement le [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) applet de commande PS pour obtenir la liste de la dernière version non protégées des composants de SQL

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Une fois que les éléments protégeables pertinentes sont extraites, activer les sauvegardes comme indiqué dans le [au-dessus de section](#configuring-backup).
Si un ne souhaite pas détecter manuellement les nouvelles bases de données, ils peuvent opter pour autoprotection comme expliqué [ci-dessous](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Activer AutoProtection

Un utilisateur peut configurer la sauvegarde telles que toutes les bases de données ajoutées à l’avenir sont automatiquement protégés par une stratégie spécifique. Pour activer autoprotection, utilisez [Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) applet de commande PS.

Étant donné que l’instruction est la sauvegarde toutes les futures des bases de données, l’opération est fait à un SQLInstance niveau.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

Une fois que l’intention d’autoprotection est accordée, l’enquête sur l’ordinateur pour extraire nouvellement ajouté des bases de données intervient en tant que tâche en arrière-plan planifiée toutes les 8 heures.

## <a name="restore-sql-dbs"></a>Restaurer des bases de données SQL

Sauvegarde Azure peut restaurer des bases de données SQL Server qui sont en cours d’exécution sur des machines virtuelles Azure comme suit :

1. Restaurer à une date ou heure spécifique (à la seconde) à l’aide de sauvegardes de fichier journal. Azure Backup détermine automatiquement la sauvegarde différentielle complète appropriée et la chaîne de sauvegardes de journaux nécessaires à la restauration basée sur l’heure sélectionnée.
2. Restaurer une sauvegarde complète ou différentielle spécifique à restaurer à un point de récupération spécifique.

Vérifier les prérequis mentionnés [ici](restore-sql-database-azure-vm.md#prerequisites) avant de restaurer des bases de données SQL.

Tout d’abord extraire les informations pertinentes sauvegardées à l’aide de la base de données SQL la [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) applet de commande PS.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Extraire l’heure de restauration pertinentes

Comme indiqué plus haut, utilisateur peut restaurer la base de données SQL sauvegardées vers une copie différentielle/complète **ou** à un journal dans le temps.

#### <a name="fetch-distinct-recovery-points"></a>Extraire des points de récupération distincts

Utilisez [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) pour extraire des points de récupération distincts (différentielle/complète) pour une base de données SQL sauvegardées.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

La sortie est similaire à l’exemple suivant

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Pour extraire le point de récupération approprié, utilisez le filtre « RecoveryPointId » ou un filtre de tableau.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Extraire le point de récupération de point-à-temps

Si l’utilisateur souhaite restaurer la base de données à un certain point dans le temps, utilisez [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) applet de commande PS. L’applet de commande renvoie une liste de dates qui représentent les heures de début et de fin d’une chaîne ininterrompue, continue de journal pour cet élément de sauvegarde SQL. Le souhaitée point-à-temps doit être comprise dans cette plage.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

La sortie sera similaire à l’exemple suivant.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

La sortie ci-dessus, que cet utilisateur peut restaurer n’importe quel point dans le temps entre l’heure de début affiché et l’heure de fin. Les heures sont exprimées en UTC. Construire n’importe quel point-à-temps dans PS qui se trouve dans la plage indiquée ci-dessus.

> [!NOTE]
> Lorsqu’un journal dans le temps sélectionné pour la restauration, utilisateur pas besoin de spécifier le point de départ par exemple, la sauvegarde complète à partir de laquelle la base de données est restaurée. Le service sauvegarde Azure s’occupe la totalité du plan de récupération par exemple, qui sauvegarde à choisir, ce qui complète les sauvegardes à appliquer, etc. du journal.

### <a name="determine-recovery-configuration"></a>Déterminer la configuration de la récupération

En cas de restauration de base de données SQL, les scénarios de restauration suivants sont pris en charge.

1. Substitution de la base de données SQL sauvegardées avec les données d’un autre point de récupération - OriginalWorkloadRestore
2. Restauration de la base de données SQL en tant que nouvelle base de données dans la même instance SQL - AlternateWorkloadRestore
3. Restauration de la base de données SQL en tant que nouvelle base de données dans une autre instance SQL dans un autre SQL VM - AlternateWorkloadRestore

Après l’extraction du point de récupération approprié (distincts ou des journaux de point-à-temps), utilisez [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) applet de commande PS pour extraire l’objet de configuration de récupération selon le plan de récupération souhaité.

#### <a name="original-workload-restore"></a>Restauration de la charge de travail d’origine

Pour remplacer la base de données sauvegardées avec les données à partir du point de récupération, spécifiez simplement l’indicateur approprié et le point de récupération appropriées comme indiqué dans l’exemple (s) suivant.

##### <a name="original-restore-with-distinct-recovery-point"></a>Restauration d’origine avec un point de récupération distinct

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Restauration d’origine avec le journal dans le temps

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Restauration de l’autre charge de travail

> [!IMPORTANT]
> Une base de données SQL sauvegardées peuvent être restaurées en tant que nouvelle base de données à un autre SQLInstance uniquement, dans une machine virtuelle Azure inscrit auprès de ce coffre.

Comme indiqué ci-dessus, si la cible SQLInstance se situe au sein d’une autre machine virtuelle de Azure, assurez-vous qu’il est [inscrit auprès de ce coffre](#registering-the-sql-vm) et le paramètre SQLInstance pertinentes apparaît sous la forme d’un élément protégeable.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Puis transmettez simplement le point de récupération approprié, la cible SQL instance avec l’indicateur approprié comme indiqué ci-dessous.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Autre restauration avec un point de récupération distinct

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Autre restauration avec journal point-à-temps

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

L’objet de configuration de point de récupération final obtenu à partir de [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) applet de commande PS a toutes les informations pertinentes pour la restauration et est comme indiqué ci-dessous.

````powershell
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

Vous pouvez modifier les champs de nom, OverwriteWLIfpresent, NoRecoveryMode et targetPhysicalPath base de données restaurées. Obtenir des détails sur les chemins d’accès de fichier cible comme indiqué ci-dessous.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Définir les propriétés pertinentes de PS en tant que valeurs de chaîne, comme indiqué ci-dessous.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

> [!IMPORTANT]
> Assurez-vous que l’objet de configuration de récupération final a toutes les valeurs nécessaires et appropriées dans la mesure où l’opération de restauration doit reposer sur l’objet de configuration.

### <a name="restore-with-relevant-configuration"></a>Restaurer avec la configuration appropriée

Une fois que l’objet de configuration de récupération approprié est obtenu et vérifié, utilisez le [AzRecoveryServicesBackupItem de restauration](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) applet de commande PS pour démarrer le processus de restauration.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

L’opération de restauration retourne une tâche à suivre.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Gérer les sauvegardes SQL

### <a name="on-demand-backup"></a>Sauvegarde de la demande

Une fois la sauvegarde a été activée pour une base de données, utilisateur peut également déclencher une sauvegarde à la demande pour la base de données à l’aide [AzRecoveryServicesBackupItem de sauvegarde](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) applet de commande PS. L’exemple suivant déclenche une sauvegarde complète sur une base de données SQL avec la compression activée et la sauvegarde complète doit être conservée pendant 60 jours.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

La commande de sauvegarde ad hoc retourne une tâche à suivre.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Si la sortie est perdue ou si vous souhaitez obtenir l’ID de travail pertinents, [obtenir la liste des tâches](#track-azure-backup-jobs) à partir de la sauvegarde Azure de service, puis suivre elle et ses détails.

### <a name="change-policy-for-backup-items"></a>Modifier la stratégie pour les éléments de sauvegarde

Utilisateur peut modifier la stratégie existante ou diffère de la stratégie de l’élément sauvegardé Policy1 Stratégie2. Pour basculer des stratégies pour un élément sauvegardé, simplement extraire la stratégie concernée et sauvegarder élément et utiliser le [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) avec l’élément de sauvegarde comme paramètre.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

La commande attend jusqu'à ce que la sauvegarde de la configuration est terminée et renvoie le résultat suivant.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Re-register machines virtuelles SQL

> [!WARNING]
> Veillez à lire ce [document](backup-sql-server-azure-troubleshoot.md#re-registration-failures) pour comprendre les symptômes et les causes avant de tenter la réinscription

Pour déclencher la réinscription de VM SQL, extraire le conteneur de sauvegarde pertinentes et passer à l’applet de commande register.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Arrêter la protection

#### <a name="retain-data"></a>Conserver les données

Si l’utilisateur souhaite arrêter la protection, ils peuvent utiliser le [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) applet de commande PS. Cela empêchera les sauvegardes planifiées, mais sont soutenues par les données jusqu'à présent sont conservées indéfiniment.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Supprimer les données de sauvegarde

Pour supprimer complètement les données de sauvegarde stockées dans le coffre, il suffit d’ajouter «-indicateur/basculer des RemoveRecoveryPoints vers le ['disable' commande de protection](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Désactiver la protection automatique

Si autoprotection a été configurée sur un SQLInstance, utilisateur peut désactiver à l’aide de la [Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) applet de commande PS.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Annuler l’inscription de la machine virtuelle SQL

Si toutes les bases de données d’un serveur SQL [sont n’est plus existent des données protégées et ne sauvegarde](#delete-backup-data), utilisateur peut annuler l’inscription de la VM SQL à partir de ce coffre. Alors seulement utilisateur peut protéger des bases de données à un autre coffre. Utilisez [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) applet de commande PS pour annuler l’inscription de la VM SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Effectuer le suivi des travaux de sauvegarde Azure

Il est important de noter que sauvegarde Azure uniquement effectue le suivi des travaux de l’utilisateur déclenchée dans la sauvegarde SQL. Les sauvegardes planifiées (y compris les sauvegardes du journal) ne sont pas visibles dans le portail ou de powershell. Toutefois, éventuelle planifiée tâches échouent, un [sauvegarde alerte](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) est généré et affiché dans le portail. [Utiliser Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) pour effectuer le suivi de toutes les tâches planifiées et autres informations pertinentes.

Les utilisateurs peuvent suivre des opérations de l’utilisateur ou ad hoc est déclenché avec le JobID est retourné dans le [sortie](#on-demand-backup) de tâches asynchrones telles que la sauvegarde. Utilisez [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) applet de commande PS pour effectuer le suivi du travail et ses détails.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Pour obtenir la liste des travaux d’ad hoc et leurs États de service de sauvegarde Azure, utilisez [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) applet de commande PS. L’exemple suivant retourne tous les travaux SQL en cours d’exécution.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Pour annuler un travail en cours d’exécution, utilisez la [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) applet de commande PS.

## <a name="managing-sql-always-on-availability-groups"></a>La gestion des groupes de disponibilité SQL Always On

Pour SQL groupes de disponibilité AlwaysOn, veillez à [inscrire tous les nœuds](#registering-the-sql-vm) du groupe de disponibilité (AG). Une fois l’inscription est effectuée pour tous les nœuds, un objet de groupe de disponibilité SQL est logiquement créé sous éléments pouvant être protégés. Les bases de données sous le groupe de disponibilité SQL seront afficheront en tant que « SQLDatabase ». Les nœuds apparaîtront en tant qu’instances autonomes et les bases de données SQL par défaut sous les seront afficheront en tant que bases de données SQL également.

Supposons par exemple, un groupe de disponibilité SQL a deux nœuds : « sql-server-0 » et « sql-server-1 » et 1 base de données de groupe de disponibilité de SQL. Une fois que les deux nœuds sont inscrits, si utilisateur [répertorie les éléments protégeables](#fetching-sql-dbs), il répertorie les composants suivants

1. Un objet de groupe de disponibilité SQL - protégeable type en tant que SQLAvailabilityGroup d’élément
2. Un groupe de disponibilité de SQL DB - type d’élément pouvant être protégés en tant que SQLDatabase
3. type d’élément protégeable SQL-server-0 - comme SQLInstance
4. SQL-server-1 : élément protégeable tapez comme SQLInstance
5. Le type d’élément pouvant être protégé par défaut quelconque des bases de données SQL (master, model et msdb) sous sql-server-0 - comme SQLDatabase
6. Les bases de données SQL (master, model et msdb) par défaut sous sql-server-1 : le type d’élément pouvant être protégé en tant que SQLDatabase

SQL-server-0, 1 de sql server est également répertorié comme « AzureVMAppContainer » lorsque [conteneurs sauvegarde figurent](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Extraire la base de données SQL approprié pour [activer la sauvegarde](#configuring-backup) et [sauvegarde ad hoc](#on-demand-backup) et [restaurer des applets de commande PS](#restore-sql-dbs) sont identiques.
