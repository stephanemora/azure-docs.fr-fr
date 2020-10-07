---
title: 'Démarrage rapide : Créer un pool Synapse SQL avec Azure PowerShell'
description: Créez rapidement un pool Synapse SQL avec une règle de pare-feu au niveau du serveur à l’aide d’Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse    , devx-track-azurepowershell
ms.openlocfilehash: 5408944f16509f83c30b9ee066d6f0a93dab95f0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91567653"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-powershell"></a>Démarrage rapide : Créer un pool Synapse SQL avec Azure PowerShell

Créez un pool SQL synapse (entrepôt de données) dans Azure Synapse Analytics à l’aide d’Azure PowerShell.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

> [!IMPORTANT]
> La création d’un pool SQL peut donner lieu à un nouveau service facturable.  Pour plus d’informations, consultez [Tarification Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) et suivez les instructions indiquées à l’écran.

```powershell
Connect-AzAccount
```

Pour voir l’abonnement que vous utilisez, exécutez [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Si vous devez utiliser un autre abonnement que celui par défaut, exécutez [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Créer des variables

Définissez des variables à utiliser dans les scripts de ce démarrage rapide.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Un groupe de ressources est un conteneur dans lequel les ressources Azure sont déployées et gérées comme groupe. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Créer un serveur

Créez un [serveur SQL logique](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) avec la commande [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Un serveur contient un groupe de bases de données gérées en tant que groupe. L’exemple suivant illustre la création d’un serveur nommé de façon aléatoire dans votre groupe de ressources avec un utilisateur administrateur nommé `ServerAdmin` et un mot de passe `ChangeYourAdminPassword1`. Remplacez les valeurs prédéfinies par ce que vous souhaitez.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Créez une [règle de pare-feu au niveau du serveur](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) avec la commande [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Une règle de pare-feu au niveau du serveur permet à une application externe, telle que SQL Server Management Studio ou l’utilitaire SQLCMD, de se connecter à un pool SQL à travers le pare-feu du service de pool SQL.

Dans l’exemple suivant, le pare-feu n’est ouvert qu’à d’autres ressources Azure. Pour activer la connectivité externe, remplacez l’adresse IP par une adresse correspondant à votre environnement. Pour ouvrir toutes les adresses IP, utilisez 0.0.0.0 comme adresse IP de début et 255.255.255.255 comme adresse de fin.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Les points de terminaison SQL communiquent via le port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pourrez pas vous connecter à votre serveur, sauf si votre service informatique ouvre le port 1433.
>

## <a name="create-a-sql-pool"></a>Créer un pool SQL

Dans l’exemple suivant, un pool SQL est créé à l’aide des variables préalablement définies.  L’exemple spécifie que l’objectif du service est DW100c, ce qui correspond à un point de départ peu coûteux pour votre pool SQL.

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Les paramètres obligatoires sont :

* **RequestedServiceObjectiveName** : quantité de valeurs [Data Warehouse Unit](what-is-a-data-warehouse-unit-dwu-cdwu.md) que vous demandez. L’augmentation de cette quantité augmente les coûts de calcul. Pour obtenir la liste des valeurs prises en charge, consultez les [limites de mémoire et de concurrence](memory-concurrency-limits.md).
* **DatabaseName** : nom du pool SQL que vous créez.
* **ServerName** : nom du serveur que vous utilisez pour la création.
* **ResourceGroupName** : groupe de ressources que vous utilisez. Pour trouver des groupes de ressources disponibles dans votre abonnement, utilisez Get-AzureResource.
* **Edition** : l’édition doit être « DataWarehouse » pour créer un pool SQL.

Les paramètres facultatifs sont :

* **CollationName** : s’il n’est pas spécifié, le classement par défaut est SQL_Latin1_General_CP1_CI_AS. Le classement ne peut pas être modifié sur une base de données.
* **MaxSizeBytes** : par défaut, la taille maximale d’une base de données est de 240 To. La taille maximale limite les données rowstore. Il y a un stockage illimité pour les données en colonnes.

Pour plus d’informations sur les options de paramètre, consultez [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres tutoriels de démarrage rapide de cette collection reposent sur ce démarrage rapide.

> [!TIP]
> Si vous prévoyez de continuer avec d’autres tutoriels de démarrage rapide, ne nettoyez pas les ressources créées dans ce guide de démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées lors de ce guide de démarrage rapide dans le portail Azure.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un pool SQL, une règle de pare-feu et vous êtes connecté à votre pool SQL. Pour en savoir plus, consultez l’article [Charger des données dans un pool SQL](load-data-from-azure-blob-storage-using-polybase.md).
