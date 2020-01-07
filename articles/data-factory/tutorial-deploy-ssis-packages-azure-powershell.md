---
title: Provisionner Azure-SSIS Integration Runtime avec PowerShell
description: Découvrez comment provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory avec PowerShell afin de pouvoir déployer et exécuter des packages SSIS dans Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: f374bd386996cd02ab7e8bff975f757aec1a0bfc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439384"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory avec PowerShell

Ce tutoriel décrit les différentes étapes de provisionnement d’un runtime d’intégration (IR) Azure SQL Server Integration Services (SSIS) dans Azure Data Factory. Azure-SSIS IR prend en charge l’exécution de packages déployés dans le catalogue SSIS (SSISDB) hébergé par le serveur Azure SQL Database/Managed Instance (modèle de déploiement de projet) et de ceux déployés dans les systèmes de fichiers/les partages de fichiers/Azure Files (modèle de déploiement de package). Une fois qu’Azure-SSIS IR est provisionné, vous pouvez utiliser des outils familiers, comme SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS), et des utilitaires en ligne de commande, comme `dtinstall`/`dtutil`/`dtexec`, pour déployer et exécuter vos packages dans Azure. Dans ce tutoriel, vous effectuez les étapes suivantes :

> [!NOTE]
> Cet article utilise Azure PowerShell pour configurer un runtime d’intégration Azure-SSIS. Pour utiliser le Portail Azure/l’application ADF afin de provisionner un runtime d’intégration Azure SSIS, consultez [Tutoriel : Approvisionnement de votre IR Azure-SSIS](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un runtime d’intégration Azure-SSIS
> * Démarrer le runtime d’intégration Azure-SSIS
> * Passer en revue le script complet
> * Déployer des packages SSIS

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer. Pour obtenir des informations conceptuelles sur le runtime d’intégration (IR) Azure-SSIS, consultez [Vue d’ensemble du runtime d’intégration Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Serveur SQL Database (facultatif)** . Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. ADF crée à son tour une base de données SSISDB sur ce serveur de base de données. Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux des exécutions dans SSISDB sans dépasser les régions Azure. 
    - En fonction du serveur de base de données sélectionné, SSISDB peut être créée à votre place en tant que base de données unique, faisant partie d’un pool élastique ou dans une instance gérée de type Managed Instance, et accessible sur un réseau public ou en rejoignant un réseau virtuel. Pour obtenir de l’aide sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer une base de données Azure SQL Database unique, un pool élastique et une instance managée](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). Si vous utilisez Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales, vous devez joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. Consultez [Créer un runtime d’intégration Azure-SSIS IR dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur de base de données. Cela ne s’applique pas quand vous utilisez le serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance dans un réseau virtuel pour héberger SSISDB. Pour en savoir plus, consultez [Sécuriser votre base de données Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pour savoir comment activer ce paramètre à l’aide de PowerShell, consultez [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Ajoutez l’adresse IP de l’ordinateur client ou une plage d’adresses IP qui inclut l’adresse IP de l’ordinateur client à la liste d’adresses IP client dans les paramètres de pare-feu du serveur de base de données. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Vous pouvez vous connecter au serveur de base de données à l’aide de l’authentification SQL avec vos informations d’identification d’administrateur de serveur ou l’authentification Azure Active Directory (AAD) avec l’identité managée de votre ADF.  Pour cette dernière, vous devez ajouter l’identité managée de votre ADF dans un groupe AAD avec autorisations d’accès au serveur de base de données. Consultez [Créer un runtime d’intégration Azure-SSIS avec l’authentification AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Vérifiez que votre serveur de base de données n’a pas déjà une base de données SSISDB. Le provisionnement d’un runtime d’intégration Azure-SSIS ne prend pas en charge l’utilisation d’une base de données SSISDB existante.
- **Azure PowerShell**. Suivez les instructions de la rubrique [Guide pratique pour installer et configurer Azure PowerShell](/powershell/azure/install-Az-ps) si vous souhaitez exécuter un script PowerShell pour provisionner votre runtime d’intégration Azure-SSIS.

> [!NOTE]
> - Pour obtenir la liste des régions Azure dans lesquelles ADF et Azure-SSIS IR sont actuellement disponibles, consultez [Disponibilité d’Azure Data Factory et de SSIS IR par région](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Lancer Windows PowerShell ISE

Démarrez **Windows PowerShell ISE** avec des privilèges administratifs. 

## <a name="create-variables"></a>Créer des variables

Copiez et collez le script suivant. Spécifiez des valeurs pour les variables. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Se connecter et sélectionner un abonnement

Ajoutez le code suivant au script pour vous connecter et sélectionner votre abonnement Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Valider la connexion au serveur de base de données

Ajoutez le script suivant pour valider votre serveur Azure SQL Database. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Pour créer une base de données Azure SQL Database comme partie du script, consultez l’exemple suivant : 

Définissez des valeurs pour les variables qui n’ont pas déjà été définies. Par exemple :  SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../azure-resource-manager/management/overview.md) avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe.

Si votre groupe de ressources existe déjà, ne copiez pas ce code dans votre script. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Exécutez la commande suivante pour créer la fabrique de données :

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Créer un runtime d’intégration

Exécutez les commandes suivantes pour créer un runtime d’intégration Azure-SSIS qui exécute des packages SSIS dans Azure.

Si vous n’utilisez pas SSISDB, vous pouvez omettre les paramètres CatalogServerEndpoint, CatalogPricingTier et CatalogAdminCredential.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Démarrer le runtime d’intégration

Exécutez les commandes suivantes pour démarrer le runtime d’intégration Azure-SSIS.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes.
>
> Si vous utilisez SSISDB, le service ADF se connecte à votre serveur de base de données pour préparer SSISDB. 
> 
> Quand vous provisionnez un runtime d’intégration Azure-SSIS, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants fournissent la connectivité aux fichiers Excel/Access et à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Vous pouvez également installer des composants supplémentaires ; consultez [Configuration personnalisée pour Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md) pour en savoir plus.

## <a name="full-script"></a>Script complet

Le script PowerShell de cette section configure une instance Azure-SSIS qui exécute des packages SSIS. Après avoir exécuté ce script avec succès, vous pouvez déployer et exécuter des packages SSIS dans Azure.

1. Lancez l’environnement d’écriture de scripts intégré de Windows PowerShell (ISE).
2. Dans ISE, exécutez la commande suivante à partir de l’invite de commande.  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Copiez le script PowerShell de cette section et collez-le dans ISE.
4. Fournissez les valeurs appropriées à tous les paramètres au début du script.
5. Exécutez le script. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Surveiller et gérer le runtime d’intégration Azure-SSIS

Consultez les articles suivants pour plus d’informations sur la surveillance et la gestion d’un runtime d’intégration (IR) Azure-SSIS. 

- [Monitor Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller un runtime d’intégration Azure-SSIS)
- [Manage Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS)

## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS

Si vous utilisez SSISDB, vous pouvez déployer vos packages dedans et les exécuter sur le runtime d’intégration Azure-SSIS à l’aide des outils SSDT/SSMS qui se connectent à votre serveur de base de données par le biais de son point de terminaison de serveur. Pour un serveur Azure SQL Database/une instance gérée avec un point de terminaison public, le format de point de terminaison est `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342`, respectivement. Si vous n’utilisez pas SSISDB, vous pouvez déployer vos packages dans les systèmes de fichiers/partages de fichiers/Azure Files et les exécuter sur le runtime d’intégration Azure-SSIS à l’aide des utilitaires en ligne de commande `dtinstall`/`dtutil`/`dtexec`. Pour plus d’informations, consultez [Déployer des packages SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Dans les deux cas, vous pouvez également exécuter vos packages déployés sur le runtime d’intégration Azure-SSIS à l’aide de l’activité Exécuter le package SSIS dans des pipelines ADF ; consultez [Appeler une exécution de package SSIS comme activité ADF de première classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) pour en savoir plus.

Consultez aussi les articles suivants de la documentation relative à SSIS : 

- [Déployer, exécuter et surveiller des packages SSIS dans Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Se connecter à SSISDB dans Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Planifier les exécutions de packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un runtime d’intégration Azure-SSIS
> * Démarrer le runtime d’intégration Azure-SSIS
> * Passer en revue le script complet
> * Déployer des packages SSIS

Pour en savoir plus sur la personnalisation de votre runtime d’intégration Azure-SSIS, passez à l’article suivant :

> [!div class="nextstepaction"]
>[Personnaliser le runtime d’intégration Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)