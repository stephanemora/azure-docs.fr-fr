---
title: Migrer SQL Server vers Azure SQL Database Managed Instance avec Database Migration Service et PowerShell | Microsoft Docs
description: Apprenez à migrer à partir d’un serveur SQL Server local vers Azure SQL DB Managed Instance à l’aide d’Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: 96ee3f5e1b3cfe67cb75e50c6247e41f0d901393
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867915"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Migrer SQL Server local vers une instance gérée Azure SQL Database à l’aide d’Azure PowerShell
Dans cet article, vous allez migrer la **Adventureworks2016** base de données restaurée vers une instance locale de SQL Server 2005 ou ci-dessus vers Azure SQL Database managed instance à l’aide de Microsoft Azure PowerShell. Vous pouvez migrer des bases de données à partir d’une instance de SQL Server locale vers une instance gérée Azure SQL Database à l’aide de la `Az.DataMigration` module dans Microsoft Azure PowerShell.

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
>
> * Créez un groupe de ressources.
> * Créez une instance d’Azure Database Migration Service.
> * Créer un projet de migration dans une instance d’Azure Database Migration Service.
> * Exécuter la migration.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Cet article contient des détails sur la façon d’effectuer des migrations en ligne et hors connexion.

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer cette procédure, vous avez besoin de :

* [SQL Server 2016 ou version ultérieure](https://www.microsoft.com/sql-server/sql-server-downloads) (toute édition).
* Une copie locale de la **AdventureWorks2016** base de données, qui est disponible au téléchargement [ici](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Activer le protocole TCP/IP, qui est désactivé par défaut avec l’installation de SQL Server Express. Activer le protocole TCP/IP en suivant l’article [Activer ou désactiver un protocole réseau de serveur](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Pour configurer votre [pare-feu Windows pour accéder au moteur de base de données](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Une base de données Azure SQL managed instance. Vous pouvez créer une instance gérée Azure SQL Database en suivant les indications de l’article [créer une Azure SQL Database managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Pour télécharger et installer [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou version ultérieure.
* Un réseau virtuel de Azure (VNet) créé à l’aide du modèle de déploiement Azure Resource Manager, qui fournit le Service de Migration de base de données Azure avec une connectivité site à site à vos serveurs de sources locales à l’aide [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Une évaluation terminée de votre migration de base de données et le schéma en local à l’aide de Data Migration Assistant, comme décrit dans l’article [une évaluation de migration de SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Pour télécharger et installer le `Az.DataMigration` module (la version 0.7.2 ou version ultérieure) à partir de PowerShell Gallery à l’aide de [applet de commande Install-Module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Pour garantir que les informations d’identification utilisées pour se connecter à une instance de SQL Server source ont la [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) autorisation.
* Pour vous assurer que les informations d’identification utilisées pour se connecter à Azure SQL Database cible instance gérée a l’autorisation de base de données de contrôle sur les bases de données cible Azure SQL Database managed instance.

    > [!IMPORTANT]
    > Pour les migrations en ligne, vous devez déjà avoir configuré vos informations d’identification Azure Active Directory. Pour plus d’informations, consultez l’article [utiliser le portail pour créer un compte Azure AD principal d’application et de service pouvant accéder aux ressources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Connectez-vous à votre abonnement Microsoft Azure

Connectez-vous à votre abonnement Azure à l’aide de PowerShell. Pour plus d’informations, consultez l’article [vous connecter avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créer un groupe de ressources à l’aide de la [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) commande.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* dans le *est des États-Unis* région.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Créer une instance d’Azure Database Migration Service

Vous pouvez créer une instance Azure Database Migration Service à l’aide de l’applet de commande `New-AzDataMigrationService`.
Cette cmdlet attend les paramètres requis suivants :

* *Nom du groupe de ressources Azure*. Vous pouvez utiliser [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) commande pour créer un groupe de ressources Azure comme expliqué précédemment et indiquez son nom en tant que paramètre.
* *Nom du service*. Chaîne qui correspond au nom de service unique souhaité pour Azure Database Migration Service.
* *Emplacement*. Spécifie l’emplacement du service. Spécifiez un emplacement de centre de données Azure, tels qu’ouest des États-Unis ou Asie du Sud-est.
* *Référence SKU*. Ce paramètre correspond au nom de référence SKU DMS. Les noms de référence (SKU) prises en charge actuellement sont *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identificateur de sous-réseau virtuel*. Vous pouvez utiliser l’applet de commande [ `New-AzVirtualNetworkSubnetConfig` ](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) pour créer un sous-réseau.

L’exemple suivant crée un service nommé *MyDMS* dans le groupe de ressources *MyDMSResourceGroup* situé dans le *est des États-Unis* région à l’aide d’un réseau virtuel nommé  *MyVNET* et un sous-réseau nommé *MySubnet*.

> [!IMPORTANT]
> L’extrait de code ci-dessous est pour une migration hors connexion, ce qui ne nécessite pas une instance d’Azure Database Migration Service selon une référence (SKU) Premium. Pour une migration en ligne, la valeur du paramètre - référence (SKU) doit inclure une référence (SKU) Premium.

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

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Créer un objet d’informations de connexion de base de données pour les connexions source et cible

Vous pouvez créer un objet d’informations de connexion de base de données à l’aide de la `New-AzDmsConnInfo` applet de commande qui attend les paramètres suivants :

* *ServerType*. Type de connexion de base de données demandé, par exemple, MySQL, Oracle ou MySQL. Utilisez SQL pour SQL Server et Azure SQL.
* *DataSource*. Le nom ou l’adresse IP d’une instance de SQL Server ou d’une instance de base de données SQL Azure.
* *AuthType*. Type d’authentification pour la connexion, qui peut être SqlAuthentication ou WindowsAuthentication.
* *TrustServerCertificate*. Ce paramètre définit une valeur qui indique si le canal est chiffré tout en ignorant l’analyse de la chaîne de certificats pour valider l’approbation. La valeur peut être `$true` ou `$false`.

L’exemple suivant crée un objet d’informations de connexion pour une source de SQL Server appelé *MySourceSQLServer* à l’aide de l’authentification sql :

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

L’exemple suivant illustre la création des informations de connexion pour un serveur d’instance gérée Azure SQL Database nommé « targetmanagedinstance.database.windows.net » à l’aide de l’authentification sql :

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fournir des bases de données pour le projet de migration

Créer une liste de `AzDataMigrationDatabaseInfo` objets qui spécifie les bases de données dans le cadre du projet Azure Database Migration Service, qui pourra être fourni comme paramètre pour la création du projet. Vous pouvez utiliser l’applet de commande `New-AzDataMigrationDatabaseInfo` créer `AzDataMigrationDatabaseInfo`.

L’exemple suivant crée la `AzDataMigrationDatabaseInfo` de projet associé à la **AdventureWorks2016** de base de données et l’ajoute à la liste doit être fourni en tant que paramètre pour la création du projet.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Créer un objet de projet

Enfin, vous pouvez créer un projet Azure Database Migration Service appelé *MyDMSProject* situé dans *est des États-Unis* à l’aide de `New-AzDataMigrationProject` et ajoutez les connexions source et cible créées précédemment et le liste des bases de données à migrer.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Créer et démarrer une tâche de migration

Ensuite, créez et démarrez une tâche Azure Database Migration Service. Cette tâche nécessite des informations d’identification de connexion pour à la fois la source et cible, ainsi que la liste des tables de base de données à migrer et les informations déjà fournies avec le projet créé comme condition préalable.

### <a name="create-credential-parameters-for-source-and-target"></a>Créer des paramètres d’informations d’identification pour la source et la cible

Créer des informations d’identification de sécurité en tant que connexion un [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objet.

L’exemple suivant illustre la création de *PSCredential* objets pour les connexions source et cible, en fournissant des mots de passe en tant que variables de chaîne *$sourcePassword* et *$ targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Créer un objet de partage de fichiers de sauvegarde

Maintenant créer un objet de partage de fichiers qui représente le partage de réseau local SMB vers lequel Azure Database Migration Service peut prendre la source de sauvegardes de base de données à l’aide de la `New-AzDmsFileShare` applet de commande.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Créer l’objet de base de données sélectionné

L’étape suivante consiste à sélectionner les bases de données source et cible à l’aide de la `New-AzDmsSelectedDB` applet de commande.

L’exemple suivant est pour la migration d’une base de données à partir de SQL Server vers une instance gérée Azure SQL Database :

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Si l’intégralité de l’instance SQL Server a besoin d’un lift-and-shift dans Azure SQL Database managed instance, puis une boucle pour prendre toutes les bases de données à partir de la source est fournie ci-dessous. Dans l’exemple suivant, pour $Server, $SourceUserName et $SourcePassword, fournissent votre source de détails de SQL Server.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>URI SAP pour un conteneur Stockage Azure

Créez une variable contenant l’URI SAP qui donne à Azure Database Migration Service l’accès au conteneur du compte de stockage dans lequel le service charge les fichiers de sauvegarde.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Exigences de configuration supplémentaires

Il existe quelques exigences supplémentaires à prendre en compte, mais elles diffèrent selon que vous effectuez une migration en ligne ou hors connexion.

#### <a name="offline-migrations"></a>Migrations hors connexion

Pour les migrations hors connexion uniquement, effectuer les tâches de configuration supplémentaires suivantes.

* **Sélectionnez connexions**. Créez une liste de connexions à migrer comme indiqué dans l’exemple suivant :

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > Actuellement, Azure Database Migration Service prend uniquement en charge les connexions SQL migration.

* **Sélectionnez les travaux de l’agent**. Créer des travaux à migrer comme indiqué dans l’exemple suivant liste de l’agent :

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Actuellement, Azure Database Migration Service prend uniquement en charge les tâches avec des étapes de travail de sous-système de T-SQL.

#### <a name="online-migrations"></a>Migrations en ligne

Pour les migrations en ligne uniquement, effectuer les tâches de configuration supplémentaires suivantes.

* **Configurer une application Azure Active Directory**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Configurer la ressource de stockage**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Créer et démarrer la tâche de migration

Utilisez la cmdlet `New-AzDataMigrationTask` pour créer et démarrer une tâche de migration.

#### <a name="specify-parameters"></a>Spécifier les paramètres

##### <a name="common-parameters"></a>Paramètres communs

Si vous effectuez une migration hors connexion ou en ligne, indépendamment de la `New-AzDataMigrationTask` applet de commande attend les paramètres suivants :

* *TaskType*. Type de tâche de migration à créer pour une migration de SQL Server vers Azure SQL Database Managed Instance. Le type *MigrateSqlServerSqlDbMi* est attendu. 
* *Resource Group Name*. Nom du groupe de ressources Azure dans lequel créer la tâche.
* *ServiceName*. Instance Azure Database Migration Service dans laquelle créer la tâche.
* *ProjectName*. Nom du projet Azure Database Migration Service dans lequel créer la tâche. 
* *TaskName*. Nom de la tâche à créer. 
* *SourceConnection*. Objet AzDmsConnInfo représentant la connexion de SQL Server source.
* *TargetConnection*. Objet AzDmsConnInfo représentant la connexion d’Azure SQL Database Managed Instance cible.
* *SourceCred*. Objet [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) pour la connexion au serveur source.
* *TargetCred*. Objet [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) pour la connexion au serveur cible.
* *SelectedDatabase*. Objet AzDataMigrationSelectedDB qui représente le mappage de base de données source et cible.
* *BackupFileShare*. Objet FileShare représentant le partage réseau local qu’Azure Database Migration Service peut utiliser pour les sauvegardes de bases de données source.
* *BackupBlobSasUri*. L’URI SAP qui donne à Azure Database Migration Service l’accès au conteneur du compte de stockage dans lequel le service charge les fichiers de sauvegarde. Découvrez comment obtenir l’URI SAP du conteneur d’objets blob.
* *SelectedLogins*. Liste des connexions sélectionnées pour la migration.
* *SelectedAgentJobs*. Liste des travaux d’agents sélectionnés pour la migration.

##### <a name="additional-parameters"></a>Paramètres supplémentaires

Le `New-AzDataMigrationTask` cmdlet attend également les paramètres qui sont uniques pour le type de migration, en mode hors connexion ou en ligne, que vous effectuez.

* **Les migrations hors connexion**. Pour les migrations hors connexion, le `New-AzDataMigrationTask` applet de commande attend également les paramètres suivants :

  * *SelectedLogins*. Liste des connexions sélectionnées pour la migration.
  * *SelectedAgentJobs*. Liste des travaux d’agents sélectionnés pour la migration.

* **Migrations en ligne**. Pour les migrations en ligne, le `New-AzDataMigrationTask` applet de commande attend également les paramètres suivants.

* *AzureActiveDirectoryApp*. Application d’Active Directory.
* *StorageResourceID*. ID de ressource de compte de stockage.

#### <a name="create-and-start-an-offline-migration-task"></a>Créer et démarrer une tâche de migration hors connexion

L’exemple suivant crée et démarre une tâche de migration hors connexion nommée **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Créer et démarrer une tâche de migration en ligne

L’exemple suivant crée et démarre une tâche de migration en ligne nommée **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Surveiller la migration

Pour surveiller la migration, procédez comme suit.

1. Consolider tous les détails de migration dans une variable appelée $CheckTask.

    Pour combiner les détails de migration, tels que des propriétés, l’état et les informations de base de données associées à la migration, utilisez l’extrait de code suivant :

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Utilisez le `$CheckTask` variable pour obtenir l’état actuel de la tâche de migration.

    Pour utiliser le `$CheckTask` variable d’obtenir l’état actuel de la tâche de migration, vous pouvez surveiller la tâche de migration en cours d’exécution en interrogeant la propriété d’état de la tâche, comme indiqué dans l’exemple suivant :

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Effectuer les basculement (migrations en ligne uniquement)

Avec une migration en ligne, une sauvegarde complète et une restauration de bases de données est effectuée, et puis le travail se poursuit sur la restauration des journaux de Transaction stocké dans le BackupFileShare.

Lorsque la base de données dans une instance managée de base de données SQL Azure est mis à jour avec les données les plus récentes et est synchronisée avec la base de données source, vous pouvez effectuer une migration à basculement.

L’exemple suivant se termine le cutover\migration. Les utilisateurs appeler cette commande à leur discrétion.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Suppression de l’instance du Service de Migration de base de données Azure

Une fois la migration terminée, vous pouvez supprimer l’instance de Service de Migration de base de données Azure :

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations sur les scénarios de migration supplémentaires (paires source/cible), consultez le Microsoft [Guide de Migration de base de données](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le Service de Migration de base de données Azure dans l’article [quel est le Service de Migration de base de données Azure ?](https://docs.microsoft.com/azure/dms/dms-overview).
