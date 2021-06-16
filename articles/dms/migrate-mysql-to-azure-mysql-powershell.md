---
title: 'PowerShell : Exécuter une migration hors connexion à partir d’une base de données MySQL vers Azure Database pour MySQL avec DMS'
titleSuffix: Azure Database Migration Service
description: Apprenez à migrer une base de données MySQL locale vers Azure Database pour MySQL à l’aide d’Azure Database Migration Service par le biais d’un script PowerShell.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: eea4a539c8a2b17a9a6280a8f847e68542d1a8d5
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950642"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>Migrer MySQL vers Azure Database pour MySQL hors connexion avec PowerShell et Azure Database Migration Service

Dans cet article, vous migrez une base de données MySQL restaurée dans une instance locale vers Azure Database pour MySQL à l’aide de la fonctionnalité de migration hors connexion d’Azure Database Migration Service par le biais de Microsoft Azure PowerShell. L’article documente une collection de scripts PowerShell qui peuvent être exécutés en séquence pour effectuer la migration hors connexion d’une base de données MySQL vers Azure. Vous pouvez télécharger l’intégralité du script PowerShell décrit dans ce tutoriel à partir de notre [dépôt GitHub](https://github.com/Azure/azure-mysql/tree/master/Azure%20DMS%20-%20MySQL%20Offline%20Migration%20Script).


> [!NOTE]
> Actuellement, il n’est pas possible d’exécuter une migration de base de données complète à l’aide du module Az.DataMigration. Entre temps, l’exemple de script PowerShell est fourni « en l’état ». Il vous permet d’utiliser l’[API REST DMS](/rest/api/datamigration/tasks/get) et d’automatiser la migration. Ce script sera modifié ou déprécié, une fois que sa prise en charge officielle sera ajoutée au module Az.DataMigration et à Azure CLI. 

> [!NOTE]
> Amazon Relational Database Service (RDS) pour MySQL et Amazon Aurora (MySQL) sont également pris en charge en tant que sources pour la migration.

> [!IMPORTANT]
> Pour les migrations en ligne, vous pouvez utiliser des outils open source comme [MyDumper/MyLoader](https://centminmod.com/mydumper.html) avec [réplication des données entrantes](../mysql/concepts-data-in-replication.md).


L’article vous aide à automatiser le scénario dans lequel les noms des bases de données source et cible peuvent être identiques ou différents et, dans le cadre de la migration, soit l’ensemble, soit une partie des tables de la base de données cible ont besoin d’être migrées, en sachant qu’elles ont le même nom et la même structure. Bien que les articles partent du principe que la source est une instance de base de données MySQL et la cible Azure Database pour MySQL, la procédure peut être utilisée pour effectuer une migration depuis une source Azure Database pour MySQL vers une même cible, en modifiant simplement le nom du serveur source et les informations d’identification. De plus, la migration de serveurs MySQL d’une version antérieure (v5.6 et versions ultérieures) vers des versions ultérieures est également prise en charge.

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
>
> * Migrer le schéma de la base de données.
> * Créez un groupe de ressources.
> * Créer une instance Azure Database Migration Service.
> * Créer un projet de migration dans une instance Azure Database Migration Service.
> * Configurer le projet de migration pour utiliser la fonctionnalité de migration hors connexion pour MySQL.
> * Exécuter la migration.

## <a name="prerequisites"></a>Prérequis

Pour effectuer cette procédure, vous avez besoin de :

* Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free).
* Une base de données MySQL locale de version 5.6 ou supérieure. Si ce n’est pas le cas, téléchargez et installez [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 ou une version ultérieure.
* [Créez une instance dans Azure Database pour MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Consultez l’article [Utiliser MySQL Workbench pour se connecter et interroger des données](../mysql/connect-workbench.md) pour plus d’informations sur la connexion et la création d’une base de données à l’aide de l’application Workbench. La version d’Azure Database pour MySQL doit être supérieure ou égale à la version locale de MySQL. Par exemple, MySQL 5.7 peut migrer vers Azure Database pour MySQL 5.7 ou sa mise à niveau vers la version 8.  
* Créez un réseau virtuel Microsoft Azure pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité site à site à vos serveurs sources locaux via [ExpressRoute](../expressroute/expressroute-introduction.md) ou un [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Pour plus d’informations sur la création d’un réseau virtuel, consultez la [documentation sur le réseau virtuel](../virtual-network/index.yml), en particulier les articles sur le démarrage rapide, qui fournissent des informations pas à pas.

    > [!NOTE]
    > Pendant la configuration du réseau virtuel, si vous utilisez ExpressRoute avec le peering réseau à Microsoft, ajoutez le [point de terminaison](../virtual-network/virtual-network-service-endpoints-overview.md) de service *Microsoft.Sql* au sous-réseau dans lequel doit être provisionné le service. Cette configuration est nécessaire car Azure Database Migration Service ne dispose pas d’une connectivité Internet.

* Vérifiez que les règles de groupe de sécurité réseau de votre réseau virtuel ne bloquent pas le port de sortie 443 de ServiceTag pour Storage et AzureMonitor. Pour plus d’informations sur le filtrage du trafic de groupe de sécurité réseau de réseau virtuel, consultez l’article [Filtrer le trafic avec les groupes de sécurité réseau](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Ouvrez votre pare-feu Windows pour autoriser les connexions à partir de Réseau virtuel afin de permettre à Azure Database Migration Service d’accéder au serveur MySQL source via le port TCP 3306 (par défaut).
* Quand vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour autoriser les connexions à partir de Réseau virtuel afin de permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.
* Créez une [règle de pare-feu](../azure-sql/database/firewall-configure.md) au niveau du serveur ou [configurez des points de terminaison de service de réseau virtuel](../mysql/howto-manage-vnet-using-portal.md) pour la cible Azure Database pour MySQL afin d’autoriser Réseau virtuel pour Azure Database Migration Service à accéder aux bases de données cibles.
* Le serveur MySQL source doit être sur une édition MySQL Community prise en charge. Pour déterminer la version de l’instance MySQL, exécutez la commande suivante dans l’utilitaire MySQL ou MySQL Workbench :

    ```
    SELECT @@version;
    ```

* Azure Database pour MySQL ne prend en charge que les tables InnoDB. Pour convertir les tables MyISAM en InnoDB, consultez l’article [Convertir des tables MyISAM en InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)
* L’utilisateur doit disposer des privilèges nécessaires à la lecture des données sur la base de données source.
* Le guide utilise PowerShell v7.1 avec PSEdition Core qui peut être installé conformément au [guide d’installation](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true).
* Téléchargez et installez les modules suivants à partir de PowerShell Gallery en utilisant l’[applet de commande PowerShell Install-Module](/powershell/module/powershellget/Install-Module). Veillez à ouvrir la fenêtre de commande PowerShell avec l’option Exécuter en tant qu’administrateur :
    * Az.Resources
    * Az.Network
    * Az.DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

## <a name="migrate-database-schema"></a>Migrer le schéma de la base de données

Pour transférer tous les objets de base de données comme les schémas de table, les index et les procédures stockées, nous avons besoin d’extraire le schéma de la base de données source et de l’appliquer à la base de données cible. Pour extraire le schéma, vous pouvez utiliser mysqldump avec le paramètre `--no-data`. Pour cela, vous avez besoin d’un ordinateur capable de se connecter à la fois à la base de données MySQL source et au service Azure Database pour MySQL cible.

Pour exporter le schéma à l’aide de mysqldump, exécutez la commande suivante :

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Par exemple :

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Pour importer le schéma dans le service Azure Database pour MySQL cible, exécutez la commande suivante :

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Par exemple :

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Si votre schéma contient des clés étrangères, le chargement de données en parallèle pendant la migration est géré par la tâche de migration. Il n’est pas nécessaire de supprimer des clés étrangères pendant la migration du schéma.

Si la base de données contient des déclencheurs, l’intégrité des données est appliquée dans la cible avant la migration complète des données depuis la source. Nous vous recommandons de désactiver les déclencheurs dans toutes les tables au niveau de la cible pendant la migration, puis de les activer une fois la migration terminée.

Exécutez le script suivant dans MySQL Workbench sur la base de données cible pour extraire le script de suppression et le script d’ajout de déclencheurs.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Exécutez la requête de suppression de déclencheur (colonne DropQuery) générée dans le résultat pour supprimer les déclencheurs dans la base de données cible. La requête d’ajout de déclencheur peut être enregistrée afin d’être utilisée à l’issue de la migration des données.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Connexion à un abonnement Microsoft Azure

Utilisez la [commande PowerShell Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pour vous connecter à votre abonnement Azure à l’aide de PowerShell, conformément aux instructions données dans l’article [Se connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps).

Le script suivant définit l’abonnement par défaut pour la session PowerShell à l’issue de la connexion et crée une fonction de journalisation d’assistance pour les journaux de console mis en forme.

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration

L’inscription du fournisseur de ressources doit être effectuée sur chaque abonnement Azure une seule fois. Sans cette inscription, vous ne pourrez pas créer une instance d’**Azure Database Migration Service**.

Inscrivez le fournisseur de ressources à l’aide de la commande [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider). Le script suivant inscrit le fournisseur de ressources nécessaire pour **Azure Database Migration Service**.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources avant de créer des ressources DMS.

Créez un groupe de ressources à l’aide de la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* dans la région *USA Ouest 2* sous l’abonnement par défaut *mySubscription*.

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Créer une instance Azure Database Migration Service

Vous pouvez créer une instance d’Azure Database Migration Service à l’aide de l’applet de commande [New-AzDataMigrationService](/powershell/module/az.datamigration/new-azdatamigrationservice). Cette commande attend les paramètres obligatoires suivants :
* *Nom du groupe de ressources Azure*. Vous pouvez utiliser la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) pour créer le groupe de ressources Azure comme indiqué précédemment, et fournir son nom en tant que paramètre.
* *Nom du service*. Chaîne qui correspond au nom de service unique à donner à l’instance Azure Database Migration Service. 
* *Emplacement*. Spécifie l’emplacement du service. Indiquez un emplacement de centre de données Azure, tel que USA Ouest ou Asie Sud-Est.
* *Référence SKU*. Ce paramètre correspond au nom de référence SKU DMS. Les noms de référence SKU actuellement pris en charge sont *Standard_1vCore*, *Standard_2vCores* *Standard_4vCores* et *Premium_4vCores*.
* *Identificateur de sous-réseau virtuel*. Vous pouvez utiliser la commande [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) pour obtenir les informations d’un sous-réseau. 

Le script suivant s’attend à ce que le réseau virtuel *myVirtualNetwork* existe avec un sous-réseau nommé *default*, puis crée une instance de Database Migration Service portant le nom *myDmService* sous le groupe de ressources créé à l’**étape 3** et dans la même région.

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois l’instance Azure Database Migration Service créée, vous allez générer un projet de migration. Un projet de migration spécifie le type de migration à effectuer.

Le script suivant crée un projet de migration nommé *myfirstmysqlofflineproject* pour la migration hors connexion de MySQL vers Azure Database pour MySQL sous l’instance de Database Migration Service créée à l’**étape 4** et dans la même région.

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Créer un objet d’informations de connexion de base de données pour les connexions source et cible

Après avoir créé le projet de migration, vous allez créer les informations de connexion de base de données. Ces informations de connexion seront utilisées pour la connexion aux serveurs source et cible pendant le processus de migration.

Le script suivant prend le nom du serveur, un nom d’utilisateur et un mot de passe pour les instances MySQL source et cible, puis crée les objets d’informations de connexion. Le script invite l’utilisateur à entrer le mot de passe pour les instances MySQL source et cible. Pour les scripts silencieux, les informations d’identification peuvent être extraites d’Azure Key Vault. 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>Extraire la liste des noms de tables de la base de données cible

La liste des tables d’une base de données peut être extraite à l’aide d’une tâche de migration et d’informations de connexion. La liste des tables est extraite à la fois de la base de données source et de la base de données cible afin d’effectuer un mappage et une validation appropriés. 

Le script suivant prend les noms des bases de données source et cible, puis extrait la liste des tables des bases de données à l’aide de la tâche de migration *GetUserTablesMySql*. 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>Générer un mappage des tables selon la configuration utilisateur

Dans le cadre de la configuration de la tâche de migration, vous allez créer un mappage entre les tables sources et cibles. Ce mappage se situe au niveau des noms des tables, mais le principe est que la structure des tables mappées (nombre de colonnes, noms des colonnes, types de données, etc.) est exactement la même.

Le script suivant crée un mappage basé sur la liste des tables cibles et sources extraites à l’**étape 7**. Pour un chargement de données partiel, l’utilisateur peut fournir une liste de tables à filtrer. Si aucune entrée utilisateur n’est fournie, toutes les tables cibles sont mappées. Le script vérifie aussi si une table portant le même nom existe dans la source ou non. Si le nom de la table n’existe pas dans la source, alors la table cible est ignorée pour la migration. 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>Créer et configurer les entrées de la tâche de migration

Après avoir généré le mappage des tables, vous allez créer les entrées pour une tâche de migration de type *Migrate.MySql.AzureDbForMySql* et configurer les propriétés.

Le script suivant crée la tâche de migration et définit les connexions, les noms des bases de données et le mappage des tables.

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>Configurer des paramètres de réglage des performances

Dans le cadre du module PowerShell, quelques paramètres facultatifs pouvant être paramétrés en fonction de l’environnement sont disponibles. Ces paramètres peuvent servir à améliorer les performances de la tâche de migration. Tous ces paramètres sont facultatifs et leur valeur par défaut est NULL.

> [!NOTE]
> Les configurations suivantes des performances ont montré un débit accru pendant la migration sur la référence SKU Premium.
> * WriteDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 seconds
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

Le script suivant prend les valeurs utilisateur des paramètres et définit les paramètres dans les propriétés de la tâche de migration.

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>Création et exécution de la tâche de migration

Une fois l’entrée pour la tâche configurée, la tâche est créée et exécutée sur l’agent. Le script déclenche l’exécution de la tâche et attend la fin de la migration.

Le script suivant appelle la tâche de migration configurée et attend qu’elle se termine.

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>Suppression de Database Migration Service

Vous pouvez réutiliser la même instance de Database Migration Service pour plusieurs migrations une fois que vous l’avez créée. Si vous n’envisagez pas de continuer à utiliser l’instance de Database Migration Service, vous pouvez supprimer le service à l’aide de la commande [Remove-AzDataMigrationService](/powershell/module/az.datamigration/remove-azdatamigrationservice?).

Le script suivant supprime l’instance d’Azure Database Migration Service et ses projets associés.

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les problèmes connus et les limitations lors des migrations avec DMS, consultez l’article [Problèmes courants : Azure Database Migration Service](./known-issues-troubleshooting-dms.md).
* Pour résoudre les problèmes de connectivité des bases de données sources lors de l’utilisation de DMS, consultez l’article [Problèmes de connexion aux bases de données sources](./known-issues-troubleshooting-dms-source-connectivity.md).
* Pour plus d’informations sur Azure Database Migration Service, consultez l’article [Qu’est-ce qu’Azure Database Migration Service ?](./dms-overview.md).
* Pour plus d’informations sur Azure Database pour MySQL, voir l’article [Qu’est-ce qu’Azure Database pour MySQL ?](../mysql/overview.md).
* Pour accéder à un tutoriel sur l’utilisation de DMS par le biais du portail, consultez l’article [Tutoriel : Migration de MySQL vers Azure Database pour MySQL hors connexion à l’aide de DMS](./tutorial-mysql-azure-mysql-offline-portal.md).