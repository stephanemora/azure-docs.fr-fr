---
title: 'PowerShell : Migrer en ligne SQL Server vers SQL Managed Instance'
titleSuffix: Azure Database Migration Service
description: Apprenez à migrer en ligne SQL Server vers Azure SQL Managed Instance à l’aide d’Azure PowerShell et du service Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: devx-track-azurepowershell
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 8947a9bb62306d3497e3888c15ea6f82d82d056e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110702861"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>Migrer en ligne SQL Server vers SQL Managed Instance avec PowerShell et Azure Database Migration Service

Dans cet article, vous allez migrer en ligne la base de données **AdventureWorks2016** restaurée vers une instance locale de SQL Server 2005 ou ultérieure vers une instance Azure SQL Managed Instance à l’aide de Microsoft Azure PowerShell. Vous pouvez migrer des bases de données à partir d’une instance SQL Server vers une instance Azure SQL Managed Instance à l’aide du module `Az.DataMigration` dans Microsoft Azure PowerShell.

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
>
> * Créez un groupe de ressources.
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration dans une instance Azure Database Migration Service.
> * Exécutez la migration en ligne.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Cet article décrit les étapes d’une migration en ligne, mais il est également possible de migrer [hors connexion](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md).


## <a name="prerequisites"></a>Prérequis

Pour effectuer cette procédure, vous avez besoin de :

* [SQL Server 2016 ou version ultérieure](https://www.microsoft.com/sql-server/sql-server-downloads) (toute édition).
* Une copie locale de la base de données **AdventureWorks2016**, qui est disponible au téléchargement [ici](/sql/samples/adventureworks-install-configure).
* Activer le protocole TCP/IP, qui est désactivé par défaut avec l’installation de SQL Server Express. Activer le protocole TCP/IP en suivant l’article [Activer ou désactiver un protocole réseau de serveur](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Pour configurer votre [pare-feu Windows pour accéder au moteur de base de données](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Instance SQL Managed Instance. Créez une instance SQL Managed Instance en suivant les indications de l'article [Créer une instance SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md).
* Téléchargez et installez l’[Assistant Migration de données](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou version ultérieure.
* Un réseau virtuel Microsoft Azure créé à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux pour le service Azure Database Migration Service via [ExpressRoute](../expressroute/expressroute-introduction.md) ou une passerelle [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* L’évaluation de la base de données locale et de la migration de schéma à l’aide de l’Assistant Migration de données Microsoft, comme indiqué dans l’article [Évaluation de la migration de SQL Server](/sql/dma/dma-assesssqlonprem).
* Téléchargez et installez le module `Az.DataMigration` (version 0.7.2 ou ultérieure) à partir de PowerShell Gallery en utilisant la [cmdlet PowerShell Install-Module](/powershell/module/powershellget/Install-Module).
* Pour vous assurer que les informations d’identification utilisées pour se connecter à une instance SQL Server source disposent de l’autorisation [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Pour vous assurer que les informations d’identification utilisées pour se connecter à une instance Azure Managed Instance cible disposent de l’autorisation CONTROL DATABASE sur les bases de données SQL Managed Instance cibles.

    > [!IMPORTANT]
    > Pour les migrations en ligne, vous devez déjà avoir configuré vos informations d’identification Azure Active Directory. Pour plus d’informations, consultez l’article [Utiliser le portail pour créer une application et un principal du service Azure AD pouvant accéder aux ressources](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup).

L’exemple suivant permet de créer un groupe de ressources nommé *myResourceGroup* dans la région *USA Est*.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>Créer une instance de DMS

Vous pouvez créer une instance Azure Database Migration Service à l’aide de l’applet de commande `New-AzDataMigrationService`.
Cette cmdlet attend les paramètres requis suivants :

* *Nom du groupe de ressources Azure*. Vous pouvez utiliser la commande [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) pour créer un groupe de ressources Azure indiqué précédemment et indiquez son nom en tant que paramètre.
* *Nom du service*. Chaîne qui correspond au nom de service unique à donner à l’instance Azure Database Migration Service.
* *Emplacement*. Spécifie l’emplacement du service. Indiquez un emplacement de centre de données Azure, tel que USA Ouest ou Asie Sud-Est.
* *Référence SKU*. Ce paramètre correspond au nom de référence SKU DMS. Les noms de référence SKU actuellement pris en charge sont *Basic_1vCore*, *Basic_2vCores* et *GeneralPurpose_4vCores*.
* *Identificateur de sous-réseau virtuel*. Vous pouvez utiliser la cmdlet [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) pour créer un sous-réseau.

L’exemple suivant crée un service nommé *MyDMS* dans le groupe de ressources *MyDMSResourceGroup*, qui se trouve dans la région *USA Est* à l’aide d’un réseau virtuel appelé *MyVNET* et d’un sous-réseau appelé *MySubnet*.


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois l’instance Azure Database Migration Service créée, générez un projet de migration. Un projet Azure Database Migration Service requiert des informations de connexion pour les instances source et cible, ainsi que la liste des bases de données que vous souhaitez migrer dans le cadre de ce projet.
Définissez les chaînes de connexion de connectivité source et cible.

Le script suivant définit les détails de la connexion SQL Server source : 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

Le script suivant définit les détails de la connexion à l’instance SQL Managed Instance cible : 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>Définir le mappage de la base de données source et cible

Fournir les bases de données à migrer dans ce projet de migration

Le script suivant mappe la base de données source à la nouvelle base de données correspondante sur l’instance SQL Managed Instance cible avec le nom fourni.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

Pour plusieurs bases de données, ajoutez la liste des bases de données au script ci-dessus en utilisant le format suivant :

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>Créer un projet DMS

Vous pouvez créer un projet Azure Database Migration Service dans l’instance DMS.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>Créer un objet de partage de fichiers de sauvegarde

Passez maintenant à la création d’un objet de partage de fichiers de sauvegarde représentant le partage réseau SMB local qu’Azure Database Migration Service peut utiliser pour les sauvegardes de bases de données source à l’aide de la cmdlet New-AzDmsFileShare.

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Définir le stockage Azure 

Sélectionnez le conteneur de stockage Azure à utiliser pour la migration : 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Configurer l’application Azure Active Directory

Fournissez les informations requises pour Azure Active Directory en vue d’une migration de SQL Managed Instance en ligne : 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>Créer et démarrer une tâche de migration

Ensuite, créez et démarre une tâche Azure Database Migration Service. Appelez la source et la cible à l’aide de variables, puis répertoriez les tables de base de données à migrer : 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

L’exemple suivant crée et démarre une tâche de migration en ligne nommée : 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

Pour plus d’informations, consultez [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask).

## <a name="monitor-the-migration"></a>Surveiller la migration

Pour surveiller la migration, effectuez les tâches suivantes.

### <a name="check-the-status-of-task"></a>Vérifier l'état de la tâche

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

Utilisez la commande suivante pour obtenir la liste des erreurs :-

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>Exécution du basculement 

Avec une migration en ligne, une sauvegarde et une restauration complètes des bases de données sont réalisées, et puis la tâche poursuit la restauration des journaux des transactions stockés dans le BackupFileShare.

Lorsque la base de données d’une instance Azure SQL Managed Instance est mise à jour avec les données les plus récentes et synchronisée avec la base de données source, vous pouvez effectuer une migration à basculement.

L’exemple suivant termine le basculement\la migration. Les utilisateurs appeler cette commande à leur convenance.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Supprimer l’instance Azure Database Migration Service

Au terme de la migration, vous pourrez supprimer l’instance Azure Database Migration Service :

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations sur les scénarios de migration supplémentaires (paires source/cible), consultez le [Guide de migration de base de données](https://datamigration.microsoft.com/) Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Database Migration Service, consultez l’article [Qu’est-ce qu’Azure Database Migration Service ?](./dms-overview.md).
