---
title: 'Démarrage rapide : créer un entrepôt de données SQL Azure - Azure Powershell | Microsoft Docs'
description: Créez rapidement un serveur logique SQL Database, une règle de pare-feu au niveau du serveur et un entrepôt de données avec Azure PowerShell.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/01/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 72ed9e921d96faea155c1da88dd32fcbd467d549
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413998"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Démarrage rapide : créer et interroger un entrepôt de données SQL Azure dans Azure PowerShell

Créez rapidement un entrepôt de données SQL Azure à l’aide d’Azure PowerShell.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

Ce didacticiel nécessite le module Azure PowerShell version 5.1.1 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour connaître la version dont vous disposez. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). 


> [!NOTE]
> La création d’un entrepôt SQL Data Warehouse peut entraîner un nouveau service facturable.  Pour en savoir plus, voir [SQL Data Warehouse - Tarification](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) et suivez les instructions à l’écran.

```powershell
Add-AzureRmAccount
```

Pour voir quel abonnement vous utilisez, exécutez [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Si vous devez utiliser un abonnement autre que l’abonnement par défaut, exécutez [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Créer des variables

Définissez des variables à utiliser dans les scripts de ce démarrage rapide.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Création d'un serveur logique

Créez un [serveur logique SQL Azure](../sql-database/sql-database-logical-servers.md) avec la commande [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver). Un serveur logique contient un groupe de bases de données gérées en tant que groupe. L’exemple suivant illustre la création d’un serveur nommé de façon aléatoire dans votre groupe de ressources avec un identifiant d’administrateur nommé `ServerAdmin` et un mot de passe `ChangeYourAdminPassword1`. Remplacez les valeurs prédéfinies par ce que vous souhaitez.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurer une règle de pare-feu du serveur

Créez une [règle de pare-feu au niveau du serveur SQL Azure](../sql-database/sql-database-firewall-configure.md) avec la commande [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule). Une règle de pare-feu au niveau du serveur permet à une application externe, comme SQL Server Management Studio ou l’utilitaire SQLCMD, de se connecter à un entrepôt de données SQL via le pare-feu du service SQL Data Warehouse. Dans l’exemple suivant, le pare-feu n’est ouvert qu’à d’autres ressources Azure. Pour activer la connectivité externe, remplacez l’adresse IP par une adresse correspondant à votre environnement. Pour ouvrir toutes les adresses IP, utilisez 0.0.0.0 comme adresse IP de début et 255.255.255.255 comme adresse de fin.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database et SQL Data Warehouse communiquent par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pourrez pas vous connecter à votre serveur SQL Azure, sauf si votre service informatique ouvre le port 1433.
>


## <a name="create-a-data-warehouse-with-sample-data"></a>Créer un entrepôt de données avec un exemple de données
Cet exemple crée un entrepôt de données à l’aide de variables préalablement définies.  Il spécifie que l’objectif du service est DW400, ce qui correspond à un point de départ peu coûteux pour votre entrepôt de données. 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Les paramètres obligatoires sont :

* **RequestedServiceObjectiveName** : quantité de valeurs [Data Warehouse Unit](what-is-a-data-warehouse-unit-dwu-cdwu.md) que vous demandez. L’augmentation de cette quantité augmente les coûts de calcul. Pour obtenir la liste des valeurs prises en charge, consultez les [limites de mémoire et de concurrence](memory-and-concurrency-limits.md).
* **DatabaseName**: nom de l’entrepôt SQL Data Warehouse que vous créez.
* **ServerName** : nom du serveur que vous utilisez pour la création.
* **ResourceGroupName**: groupe de ressources que vous utilisez. Pour trouver des groupes de ressources disponibles dans votre abonnement, utilisez Get-AzureResource.
* **Edition**: l’édition doit être définie sur « DataWarehouse » pour créer un entrepôt SQL Data Warehouse.

Les paramètres facultatifs sont :

- **CollationName** : le classement par défaut est COLLATE SQL_Latin1_General_CP1_CI_AS. Le classement ne peut pas être modifié sur une base de données.
- **MaxSizeBytes**: par défaut, la taille maximale d’une base de données est de 10 Go.

Pour plus d’informations sur les options de paramètre, consultez [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Supprimer les ressources

D’autres tutoriels de démarrage rapide de cette collection reposent sur ce démarrage rapide. 

> [!TIP]
> Si vous souhaitez continuer à utiliser d’autres tutoriels de démarrage rapide, ne nettoyez pas les ressources créées au cours de ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées au cours de ce démarrage rapide dans le portail Azure.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un entrepôt de données, créé une règle de pare-feu, vous vous êtes connecté à votre entrepôt de données et vous avez exécuté quelques requêtes. Pour en savoir plus sur Azure SQL Data Warehouse, continuez avec le didacticiel de chargement des données.
> [!div class="nextstepaction"]
>[Charger des données dans un entrepôt SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
