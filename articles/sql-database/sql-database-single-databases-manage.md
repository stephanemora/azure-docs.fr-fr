---
title: Créer et gérer des serveurs et des singletons SQL Azure | Microsoft Docs
description: En savoir plus sur la création et la gestion de serveurs logiques et de singletons.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/19/2018
ms.openlocfilehash: ac3ce8a417a63ce0a91339c38c2a5ae8b3013228
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466781"
---
# <a name="create-and-manage-logical-servers-and-single-databases-in-azure-sql-database"></a>Créer et gérer des serveurs logiques et des singletons dans Azure SQL Database

Vous pouvez créer et gérer des serveurs logiques de base de données et des singletons SQL Azure avec le Portail Azure, PowerShell, Azure CLI, l’API REST et Transact-SQL.

## <a name="azure-portal-manage-logical-servers-and-databases"></a>Portail Azure : gérer des serveurs logiques et des bases de données

Vous pouvez créer le groupe de ressources de la base de données SQL Azure en avance ou lors de la création du serveur lui-même. Il existe plusieurs méthodes pour accéder à un nouveau formulaire de serveur SQL : en créant un nouveau serveur SQL ou dans le cadre de la création d’une base de données.

### <a name="create-a-blank-sql-server-logical-server"></a>Créer un serveur SQL vide (serveur logique)

Pour créer un serveur Azure SQL Database (sans base de données) à l’aide du [portail Azure](https://portal.azure.com), accédez à un formulaire de serveur SQL vide (serveur logique).  

### <a name="create-a-blank-or-sample-sql-database"></a>Créer un exemple de base de données SQL ou une base de données SQL vide

Pour créer une base de données SQL Azure à l’aide du [portail Azure](https://portal.azure.com), accédez à un formulaire SQL Database vide et renseignez les informations demandées. Vous pouvez créer le groupe de ressources de la base de données SQL Azure et le serveur logique en avance ou lors de la création de la base de données elle-même. Vous pouvez créer une base de données vide ou créer un exemple de base de données reposant sur Adventure Works LT.

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Pour plus d’informations sur la sélection du niveau tarifaire de votre base de données, consultez [Modèle d’achat basé sur des DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat basé sur des vCores](sql-database-service-tiers-vcore.md).

Pour créer une option Managed Instance, consultez [Créer une option Managed Instance](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-server"></a>Gérer un serveur SQL existant

Pour gérer un serveur existant, accédez au serveur à l’aide de plusieurs méthodes, à partir d’une page de base de données SQL spécifique, de la page **Serveurs SQL** ou de la page **Toutes les ressources**, par exemple.

Pour gérer une base de données existante, accédez à la page **Bases de données SQL** page, puis cliquez sur la base de données que vous souhaitez gérer. La capture d’écran suivante montre comment commencer à définir un pare-feu au niveau du serveur pour une base de données, à partir de la page **Vue d’ensemble** d’une base de données.

   ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Pour configurer les propriétés de performance d’une base de données, consultez [Modèle d’achat basé sur des DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat basé sur des vCores](sql-database-service-tiers-vcore.md).
> [!TIP]
> Pour un démarrage rapide du portail Azure, consultez la page[Créer une base de données SQL Azure à l’aide du portail Azure](sql-database-get-started-portal.md).

## <a name="powershell-manage-logical-servers-and-databases"></a>PowerShell : gérer des serveurs logiques et des bases de données

Pour créer et gérer le serveur, les bases de données et les pare-feux SQL Azure avec Azure PowerShell, utilisez les applets de commande PowerShell suivants. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-azurerm-ps).

> [!TIP]
> Pour un démarrage rapide de PowerShell, consultez la page [Créer une base de données SQL Azure unique à l’aide de PowerShell](sql-database-get-started-portal.md). Pour obtenir des exemples de scripts PowerShell, consultez [Utiliser PowerShell pour créer une base de données SQL Azure et configurer une règle de pare-feu](scripts/sql-database-create-and-configure-database-powershell.md) et [Surveillance et mise à l’échelle d’une instance SQL Database unique à l’aide de PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

| Applet de commande | Description |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crée une base de données |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtient une ou plusieurs bases de données|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Définit les propriétés d’une base de données, ou déplace une base de données existante dans un pool élastique|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Supprime une base de données|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Crée un groupe de ressources|
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Crée un serveur|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Renvoie des informations concernant les serveurs|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Modifie les propriétés d’un serveur|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Supprime un serveur|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Crée une règle de pare-feu au niveau du serveur |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Obtient les règles de pare-feu d’un serveur|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Modifie une règle de pare-feu sur un serveur|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Supprime une règle de pare-feu d’un serveur|
| New-AzureRmSqlServerVirtualNetworkRule | Crée une [*règle de réseau virtuel*](sql-database-vnet-service-endpoint-rule-overview.md), basée sur un sous-réseau qui est un point de terminaison de service de réseau virtuel. |

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Azure CLI : gérer des serveurs logiques et des bases de données

Pour créer et gérer un serveur, des bases de données et des pare-feux SQL Azure avec [Azure CLI](/cli/azure), utilisez les commandes [Azure CLI SQL Database](/cli/azure/sql/db) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows. Pour créer et gérer des pools élastiques, consultez [Pools élastiques](sql-database-elastic-pool.md).

> [!TIP]
> Pour un démarrage rapide d’Azure CLI, consultez la page [Créer une base de données SQL Azure unique à l’aide d’Azure CLI](sql-database-cli-samples.md). Pour obtenir des exemples de scripts Azure CLI, consultez [Utiliser CLI pour créer une seule base de données Azure SQL et configurer une règle de pare-feu](scripts/sql-database-create-and-configure-database-cli.md) et [Utiliser CLI pour surveiller et mettre à l’échelle une base de données SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Applet de commande | Description |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Crée une base de données|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|Répertorie toutes les bases de données et les entrepôts de données d’un serveur, ou toutes les bases de données d’un pool élastique|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Répertorie les objectifs de service disponibles et les limites de stockage|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Renvoie les données d’utilisation de la base de données|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Obtient une base de données ou un entrepôt de données|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Met à jour une base de données|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Supprime une base de données|
|[az group create](/cli/azure/group#az-group-create)|Crée un groupe de ressources|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Crée un serveur|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|Répertorie les serveurs|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Renvoie les données d’utilisation d’un serveur|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|Obtient un serveur|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|Met à jour un serveur|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|Supprime un serveur.|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Crée la règle de pare-feu d’un serveur|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Répertorie les règles de pare-feu sur un serveur|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Affiche les détails d’une règle de pare-feu|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Met à jour une règle de pare-feu|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Supprime une règle de pare-feu|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL : gérer des serveurs logiques et des bases de données

Pour créer et gérer le serveur, les bases de données et les pare-feux SQL Azure avec Transact-SQL, utilisez les commandes T-SQL suivantes. Vous pouvez entrer ces commandes à l’aide du portail Azure, de [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), de [Visual Studio Code](https://code.visualstudio.com/docs), ou de tout autre programme pouvant se connecter à un serveur Azure SQL Database et transmettre des commandes Transact-SQL. Pour gérer des pools élastiques, consultez la page [Pools élastiques](sql-database-elastic-pool.md).

> [!TIP]
> Pour un démarrage rapide à l’aide de SQL Server Management Studio sur Microsoft Windows, consultez la page [Azure SQL Database : utilisez SQL Server Management Studio pour vous connecter et interroger des données](sql-database-connect-query-ssms.md). Pour un démarrage rapide à l’aide de Visual Studio Code sur Mac OS, Linux ou Windows, consultez la page [Azure SQL Database : utilisez Visual Studio Code pour vous connecter et interroger des données](sql-database-connect-query-vscode.md).
> [!IMPORTANT]
> Vous ne pouvez pas créer ou supprimer un serveur à l’aide de Transact-SQL.

| Commande | Description |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crée une base de données. Vous devez être connecté à la base de données MASTER pour créer une base de données.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifie une base de données SQL Azure. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modifie un entrepôt de données Azure SQL Data Warehouse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Supprime une base de données.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Renvoie l’édition (niveau de service), l’objectif de service (niveau tarifaire) et, le cas échéant, le nom du pool élastique Azure SQL Database ou d’un entrepôt de données Azure SQL Data Warehouse. Si vous êtes connecté à la base de données MASTER d’un serveur Azure SQL Database, renvoie les informations au sujet de toutes les bases de données. Pour Azure SQL Data Warehouse, vous devez être connecté à la base de données MASTER.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retourne la consommation de mémoire, d’E/S et d’UC d’une base de données Azure SQL Database. Il existe une ligne pour toutes les 15 secondes, même s’il n’existe aucune activité dans la base de données.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Renvoie les données de stockage et l’utilisation d’UC pour une base de données Azure SQL Database. Les données sont collectées et agrégées par intervalles de cinq minutes.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contient des statistiques pour les événements de connectivité de base de données SQL Database, ce qui fournit une vue d’ensemble du nombre d’échecs et de réussites de connexion de base de données. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Renvoie les interblocages les échecs de connexion et les réussites de connexion de base de données Azure SQL Database. Vous pouvez utiliser ces informations pour suivre ou dépanner votre activité de base de données avec SQL Database.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Crée ou met à jour les paramètres de pare-feu au niveau du serveur pour votre serveur SQL Database. Cette procédure stockée est uniquement disponible dans la base de données MASTER pour la connexion principale au niveau du serveur. Une règle de pare-feu au niveau du serveur peut uniquement être créée à l’aide de Transact-SQL, après que la première règle de pare-feu au niveau du serveur a été créée par un utilisateur disposant des autorisations au niveau d’Azure|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Renvoie des informations sur les paramètres de pare-feu au niveau du serveur associés à votre Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Supprime les paramètres de pare-feu au niveau du serveur de votre serveur SQL Database. Cette procédure stockée est uniquement disponible dans la base de données MASTER pour la connexion principale au niveau du serveur.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Crée ou met à jour les règles de pare-feu au niveau de la base de données pour votre base de données pour votre Azure SQL Database ou votre entrepôt de données SQL Data Warehouse. Les règles de pare-feu d’une base de données peuvent être configurées pour la base de données MASTER, ainsi que pour les bases de données utilisateur sur SQL Database. Les règles de pare-feu d’une base de données sont utiles lors de l’utilisation d’utilisateurs de base de données autonome. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Renvoie des informations sur les paramètres de pare-feu au niveau de la base de données associés à votre Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Supprime les paramètres de pare-feu au niveau de la base de données de votre Azure SQL Database ou de votre entrepôt de données SQL Data Warehouse. |

## <a name="rest-api-manage-logical-servers-and-databases"></a>API REST : gérer des serveurs logiques et des bases de données

Pour créer et gérer un serveur, des bases de données et des pare-feux Azure SQL, utilisez les requêtes suivantes de l’API REST.

| Commande | Description |
| --- | --- |
|[Serveurs - Create ou Update](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Crée ou met à jour un serveur.|
|[Serveurs - Delete](https://docs.microsoft.com/rest/api/sql/servers/delete)|Supprime un serveur SQL.|
|[Serveurs - Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Obtient un serveur.|
|[Serveurs - List](https://docs.microsoft.com/rest/api/sql/servers/list)|Retourne une liste de serveurs.|
|[Serveurs - List By Resource Group](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Retourne une liste de serveurs dans un groupe de ressources.|
|[Serveurs - Update](https://docs.microsoft.com/rest/api/sql/servers/update)|Met à jour un serveur existant.|
|[Bases de données : Create ou Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Crée une base de données ou met à jour une base de données existante.|
|[Bases de données - Supprimer](https://docs.microsoft.com/rest/api/sql/databases/delete)|Supprime une base de données.|
|[Bases de données - Obtenir](https://docs.microsoft.com/rest/api/sql/databases/get)|Obtient une base de données.|
|[Bases de données - Lister par pool élastique](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Renvoie une liste des bases de données dans un pool élastique.|
|[Bases de données - Lister par serveur](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retourne une liste de bases de données d’un serveur.|
|[Bases de données - Mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/update)|Met à jour une base de données existante.|
|[Règles de pare-feu - Create ou Update](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Crée ou met à jour une règle de pare-feu.|
|[Règles de pare-feu - Delete](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Supprime une règle de pare-feu.|
|[Règles de pare-feu - Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Obtient une règle de pare-feu.|
|[Règles de pare-feu - List By Server](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Retourne une liste de règles de pare-feu.|

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la migration d’une base de données SQL Server, consultez la section [Migrer une base de données SQL](sql-database-cloud-migrate.md).
- Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md).
