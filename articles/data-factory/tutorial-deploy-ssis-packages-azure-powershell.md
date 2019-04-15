---
title: Provisionner le runtime d’intégration Azure-SSIS avec PowerShell | Microsoft Docs
description: Découvrez comment provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory avec PowerShell afin de pouvoir déployer et exécuter des packages SSIS dans Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 10/28/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: cba8af369beac935834da8d2073510e0f997648b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123247"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory avec PowerShell
Ce tutoriel décrit les différentes étapes d’approvisionnement du runtime d’intégration (IR) Azure-SSIS dans Azure Data Factory. Vous pouvez ensuite utiliser SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer des et exécuter des packages SQL Server Integration Services (SSIS) sur ce runtime dans Azure. Dans ce tutoriel, vous effectuez les étapes suivantes :

> [!NOTE]
> Cet article utilise Azure PowerShell pour configurer un runtime d’intégration Azure-SSIS. Pour utiliser l’interface utilisateur (IU) de Data Factory pour provisionner un runtime d’intégration Azure SSIS, consultez [Tutoriel : Créer un runtime d’intégration Azure SSIS](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un runtime d’intégration Azure-SSIS
> * Démarrer le runtime d’intégration Azure-SSIS
> * Déployer des packages SSIS
> * Passer en revue le script complet

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer. Pour obtenir des informations conceptuelles sur le runtime d’intégration (IR) Azure-SSIS, consultez [Vue d’ensemble du runtime d’intégration Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). 
- **Serveur de base de données SQL Azure**. Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. Ce serveur héberge la base de données du catalogue SSIS (SSISDB). Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux d’activité d’exécution dans SSISDB sans dépasser les régions Azure. 
    - En fonction du serveur de base de données sélectionné, SSISDB peut être créée à votre place en tant que base de données unique, faisant partie d’un pool élastique ou dans une instance gérée de type Managed Instance, et accessible sur un réseau public ou en rejoignant un réseau virtuel. Pour obtenir de l’aide sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer les bases de données uniques/pools élastiques Azure SQL Database et Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Si vous utilisez Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales, vous devez joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. Consultez [Créer un runtime d’intégration Azure-SSIS IR dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Vérifiez que le paramètre « **Autoriser l’accès aux services Azure** » est **activé** pour votre serveur de base de données SQL. Ce paramètre est non applicable quand vous utilisez Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance pour héberger SSISDB. Pour en savoir plus, consultez [Sécuriser votre base de données SQL Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pour savoir comment activer ce paramètre à l’aide de PowerShell, consultez [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule). 
    - Ajoutez l’adresse IP de l’ordinateur client ou une plage d’adresses IP qui inclut l’adresse IP de l’ordinateur client à la liste d’adresses IP client dans les paramètres de pare-feu du serveur de base de données. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données d’Azure SQL Database](../sql-database/sql-database-firewall-configure.md). 
    - Vous pouvez vous connecter au serveur de base de données à l’aide de l’authentification SQL avec vos identifiants administrateur du serveur ou l’authentification Azure Active Directory (AAD) avec l’identité managée de votre Azure Data Factory.  Pour cette dernière, vous devez ajouter l’identité managée de votre ADF dans un groupe AAD avec autorisations d’accès au serveur de base de données. Consultez [Créer un runtime d’intégration Azure-SSIS avec l’authentification AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Vérifiez que votre serveur Azure SQL Database ne dispose pas d’un catalogue SSIS (base de données SSISDB). L’approvisionnement du runtime d’intégration SSIS Azure ne prend pas en charge l’utilisation d’un catalogue SSIS existant. 
- **Azure PowerShell**. Suivez les instructions de la page [Installation et configuration d’Azure PowerShell](/powershell/azure/install-Az-ps). Vous utilisez PowerShell pour exécuter un script afin de configurer un runtime d’intégration Azure-SSIS qui exécute des packages SSIS dans le cloud. 

> [!NOTE]
> - Pour obtenir la liste des régions Azure dans lesquelles Data Factory et Azure-SSIS Integration Runtime sont actuellement disponibles, consultez [Disponibilité d’Azure Data Factory et de SSIS IR par région](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Lancer Windows PowerShell ISE
Démarrez **Windows PowerShell ISE** avec des privilèges administratifs. 

## <a name="create-variables"></a>Créer des variables
Copiez et collez le script suivant : Spécifiez des valeurs pour les variables. Pour obtenir la liste des **niveaux de tarification** pris en charge pour Azure SQL Database, consultez [Limites de ressources pour SQL Database](../sql-database/sql-database-resource-limits.md).

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported, but for other nodes, 1-8 are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"
```

## <a name="validate-the-connection-to-database"></a>Valider la connexion à la base de données
Ajoutez le script suivant pour valider votre serveur Azure SQL Database, `<servername>.database.windows.net`. 

```powershell
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
```

Pour créer une base de données SQL Azure comme partie du script, consultez l’exemple suivant : 

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

## <a name="log-in-and-select-subscription"></a>Se connecter et sélectionner un abonnement
Ajoutez le code suivant au script pour vous connecter et sélectionner votre abonnement Azure : 

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope`.

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
Exécutez la commande suivante pour créer un runtime d’intégration Azure-SSIS qui exécute des packages SSIS dans Azure : 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
  
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
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Démarrer le runtime d’intégration
Exécutez la commande suivante pour démarrer le runtime d’intégration Azure-SSIS : 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Cette commande dure de **20 à 30 minutes**. 

## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS
Utilisez maintenant SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer vos packages SSIS dans Azure. Connectez-vous à votre serveur SQL Azure qui héberge le catalogue SSIS (SSISDB). Le nom du serveur Azure SQL Database est au format : `<servername>.database.windows.net`. 

Consultez les articles suivants de la documentation relative à SSIS : 

- [Déployer, exécuter et surveiller un package SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Se connecter au catalogue SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Planifier l’exécution des packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>Script complet
Le script PowerShell de cette section configure une instance du runtime d’intégration Azure-SSIS dans le cloud qui exécute des packages SSIS. Après avoir exécuté ce script avec succès, vous pouvez déployer et exécuter des packages SSIS dans le cloud Microsoft Azure avec SSISDB hébergé dans la base de données SQL Azure.

1. Lancez l’environnement d’écriture de scripts intégré de Windows PowerShell (ISE).
2. Dans ISE, exécutez la commande suivante à partir de l’invite de commande.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Copiez le script PowerShell de cette section et collez-le dans ISE.
4. Fournissez les valeurs appropriées à tous les paramètres au début du script.
5. Exécutez le script. La commande `Start-AzDataFactoryV2IntegrationRuntime` en fin de script s’exécute pendant **20 à 30 minutes**.

> [!NOTE]
> - Le script se connecte à votre serveur Azure SQL Database pour préparer la base de données du catalogue SSIS (SSISDB).
> 
> - Si vous approvisionnez une instance d’un runtime d’intégration Azure-SSIS, les composants Azure Feature Pack pour SSIS et Access Redistributable sont également installés. Ces composants fournissent la connectivité aux fichiers Excel et Access et à diverses sources de données Azure, en plus des sources de données prises en charge par les composants intégrés. Vous pouvez également installer des composants supplémentaires. Pour plus d’informations, consultez [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Configuration personnalisée du runtime d’intégration Azure-SSIS).

Pour obtenir la liste des **niveaux de tarification** pris en charge pour Azure SQL Database, consultez [Limites de ressources pour SQL Database](../sql-database/sql-database-resource-limits.md). 

Pour obtenir la liste des régions Azure dans lesquelles Data Factory et Azure-SSIS Integration Runtime sont actuellement disponibles, consultez [Disponibilité d’Azure Data Factory et de SSIS IR par région](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported, but for other nodes, 1-8 are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"

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

Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
    
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
    
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
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-virtual-network"></a>Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel
Si vous utilisez Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance en jonction avec un réseau virtuel pour héberger SSISDB, vous devez également joindre votre runtime d’intégration Azure-SSIS Integration Runtime au même réseau virtuel. Azure Data Factory vous permet de joindre votre runtime d’intégration SSIS Azure à un réseau virtuel. Pour en savoir, consultez [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md).

Pour obtenir un script complet popur créer un runtime Azure-SSIS qui se joint à un réseau virtuel, consultez [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Surveiller et gérer le runtime d’intégration Azure-SSIS
Consultez les articles suivants pour plus d’informations sur la surveillance et la gestion d’un runtime d’intégration (IR) Azure-SSIS. 

- [Surveiller un runtime d’intégration Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Gérer un runtime d’intégration Azure-SSIS](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez appris à : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un runtime d’intégration Azure-SSIS
> * Démarrer le runtime d’intégration Azure-SSIS
> * Déployer des packages SSIS
> * Passer en revue le script complet

Pour en savoir plus sur la personnalisation de votre runtime d’intégration Azure-SSIS, passez à l’article suivant :

> [!div class="nextstepaction"]
>[Personnaliser le runtime d’intégration Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)
