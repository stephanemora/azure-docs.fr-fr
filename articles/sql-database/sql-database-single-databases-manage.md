---
title: Créer, gérer des serveurs et bases de données uniques
description: Découvrez la création et la gestion de serveurs et bases de données uniques SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 02c4d7ba545282e3654f3889dd8000af33c728c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79232413"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>Créer et gérer des serveurs et bases de données uniques SQL Database dans Azure SQL Database

Vous pouvez créer et gérer des serveurs et bases de données uniques SQL Database via le portail Azure, PowerShell, Azure CLI, l’API REST et Transact-SQL.

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Portail Azure : Gérer des serveurs et bases de données uniques SQL Database

Vous pouvez créer le groupe de ressources de la base de données Azure SQL en avance ou lors de la création du serveur lui-même. Il existe plusieurs méthodes pour accéder à un nouveau formulaire de serveur SQL : en créant un nouveau serveur SQL ou dans le cadre de la création d’une base de données.

### <a name="create-a-blank-sql-database-server"></a>Créer un serveur SQL Database vide

Pour créer un serveur SQL Database à l’aide du [portail Azure](https://portal.azure.com), accédez à un formulaire de serveur SQL vide (serveur logique).  

### <a name="create-a-blank-or-sample-sql-single-database"></a>Créer un exemple de base de données unique SQL ou une base de données SQL vide

Pour créer une base de données unique SQL Azure à l’aide du [portail Azure](https://portal.azure.com), accédez à un formulaire SQL Database vide et renseignez les informations demandées. Vous pouvez créer le groupe de ressources de la base de données Azure SQL et le serveur SQL Database en avance ou lors de la création de la base de données unique elle-même. Vous pouvez créer une base de données vide ou créer un exemple de base de données reposant sur Adventure Works LT.

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Pour plus d’informations sur la sélection du niveau tarifaire de votre base de données, consultez [Modèle d’achat DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat vCore](sql-database-service-tiers-vcore.md).

Pour créer une option Managed Instance, consultez [Créer une option Managed Instance](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-database-server"></a>Gérer un serveur SQL Database existant

Pour gérer un serveur SQL Database existant, accédez au serveur à l’aide de plusieurs méthodes, à partir d’une page de base de données SQL spécifique, de la page **Serveurs SQL** ou de la page **Toutes les ressources**, par exemple.

Pour gérer une base de données existante, accédez à la page **Bases de données SQL** page, puis cliquez sur la base de données que vous souhaitez gérer. La capture d’écran suivante montre comment commencer à définir un pare-feu au niveau du serveur pour une base de données, à partir de la page **Vue d’ensemble** d’une base de données.

   ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Pour configurer les propriétés de performance d’une base de données, consultez [Modèle d’achat DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat vCore](sql-database-service-tiers-vcore.md).
> [!TIP]
> Pour un guide de démarrage rapide du portail Azure, consultez [Créer une base de données Azure SQL à l’aide du portail Azure](sql-database-single-database-get-started.md).

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>PowerShell : Gérer des serveurs et bases de données uniques SQL Database

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm.

Pour créer et gérer des serveurs Azure SQL Database, des bases de données uniques et mises en pool, ainsi que des pare-feu de serveur SQL Database avec Azure PowerShell, utilisez les applets de commande PowerShell suivantes. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Pour obtenir des exemples de script PowerShell, consultez [Utiliser PowerShell pour créer une base de données unique Azure SQL et configurer une règle de pare-feu pour le serveur SQL Database](scripts/sql-database-create-and-configure-database-powershell.md) et [Utiliser PowerShell pour surveiller et mettre à l’échelle une base de données SQL](scripts/sql-database-monitor-and-scale-database-powershell.md).

| Applet de commande | Description |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Crée une base de données |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Obtient une ou plusieurs bases de données|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Définit les propriétés d’une base de données, ou déplace une base de données existante dans un pool élastique|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Supprime une base de données|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Crée un groupe de ressources|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Crée un serveur|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Renvoie des informations concernant les serveurs|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Modifie les propriétés d’un serveur|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Supprime un serveur|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Crée une règle de pare-feu au niveau du serveur |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Obtient les règles de pare-feu d’un serveur|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Modifie une règle de pare-feu sur un serveur|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Supprime une règle de pare-feu d’un serveur|
| New-AzSqlServerVirtualNetworkRule | Crée une [*règle de réseau virtuel*](sql-database-vnet-service-endpoint-rule-overview.md), basée sur un sous-réseau qui est un point de terminaison de service de réseau virtuel. |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>Azure CLI : Gérer des serveurs et bases de données uniques SQL Database

Pour créer et gérer un serveur, des bases de données et des pare-feux SQL Azure avec [Azure CLI](/cli/azure), utilisez les commandes [Azure CLI SQL Database](/cli/azure/sql/db) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows. Pour créer et gérer des pools élastiques, consultez [Pools élastiques](sql-database-elastic-pool.md).

> [!TIP]
> Pour un démarrage rapide d’Azure CLI, consultez [Exemples Azure CLI pour Azure SQL Database](sql-database-cli-samples.md). Pour obtenir des exemples de scripts Azure CLI, consultez [Utiliser CLI pour créer une seule base de données Azure SQL et configurer une règle de pare-feu](scripts/sql-database-create-and-configure-database-cli.md) et [Utiliser CLI pour surveiller et mettre à l’échelle une base de données SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
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

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>Transact-SQL : Gérer des serveurs et bases de données uniques SQL Database

Pour créer et gérer le serveur, les bases de données et les pare-feux SQL Azure avec Transact-SQL, utilisez les commandes T-SQL suivantes. Vous pouvez entrer ces commandes à l’aide du portail Azure, de [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), de [Visual Studio Code](https://code.visualstudio.com/docs), ou de tout autre programme pouvant se connecter à un serveur Azure SQL Database et transmettre des commandes Transact-SQL. Pour gérer des pools élastiques, consultez la page [Pools élastiques](sql-database-elastic-pool.md).

> [!TIP]
> Pour un guide de démarrage rapide en utilisant SQL Server Management Studio sur Microsoft Windows, consultez [Azure SQL Database : Utiliser SQL Server Management Studio pour se connecter et interroger des données](sql-database-connect-query-ssms.md). Pour un guide de démarrage rapide en utilisant Visual Studio Code sur macOS, Linux ou Windows, consultez [Azure SQL Database : Utiliser Visual Studio Code pour se connecter et interroger des données](sql-database-connect-query-vscode.md).
> [!IMPORTANT]
> Vous ne pouvez pas créer ou supprimer un serveur à l’aide de Transact-SQL.

| Commande | Description |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Crée une base de données unique. Vous devez être connecté à la base de données master pour créer une base de données.|
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Modifie une base de données Azure SQL. |
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Supprime une base de données.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Renvoie l’édition (niveau de service), l’objectif de service (niveau tarifaire) et, le cas échéant, le nom du pool élastique Azure SQL Database ou d’un entrepôt de données Azure SQL Data Warehouse. Si vous êtes connecté à la base de données MASTER d’un serveur Azure SQL Database, retourne les informations sur toutes les bases de données. Pour Azure SQL Data Warehouse, vous devez être connecté à la base de données MASTER.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retourne la consommation de mémoire, d’E/S et d’UC d’une base de données Azure SQL Database. Il existe une ligne pour chaque période de 15 secondes, même s'il n'y a aucune activité dans la base de données.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Renvoie les données de stockage et l’utilisation d’UC pour une base de données Azure SQL Database. Les données sont collectées et agrégées dans des intervalles de cinq minutes.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contient des statistiques pour les événements de connectivité de base de données SQL Database, ce qui fournit une vue d’ensemble du nombre d’échecs et de réussites de connexion de base de données. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Renvoie les interblocages les échecs de connexion et les réussites de connexion de base de données Azure SQL Database. Vous pouvez utiliser ces informations pour suivre ou dépanner votre activité de base de données avec SQL Database.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Crée ou met à jour les paramètres de pare-feu au niveau du serveur pour votre serveur SQL Database. Cette procédure stockée est uniquement disponible dans la base de données master à la connexion du principal au niveau du serveur. Une règle de pare-feu au niveau du serveur peut uniquement être créée à l’aide de Transact-SQL, après que la première règle de pare-feu au niveau du serveur a été créée par un utilisateur disposant des autorisations au niveau d’Azure|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Renvoie des informations sur les paramètres de pare-feu au niveau du serveur associés à votre base de données Microsoft Azure SQL.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Supprime les paramètres de pare-feu au niveau du serveur de votre serveur SQL Database. Cette procédure stockée est uniquement disponible dans la base de données master à la connexion du principal au niveau du serveur.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Crée ou met à jour les règles de pare-feu au niveau de la base de données pour votre base de données pour votre instance Azure SQL Database ou SQL Data Warehouse. Les règles de pare-feu d’une base de données peuvent être configurées pour la base de données MASTER, ainsi que pour les bases de données utilisateur sur SQL Database. Les règles de pare-feu d’une base de données sont utiles lors de l’utilisation d’utilisateurs de base de données autonome. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Renvoie des informations sur les paramètres de pare-feu au niveau de la base de données associés à votre base de données Microsoft Azure SQL. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Supprime les paramètres de pare-feu au niveau base de données de votre base de données Azure SQL ou de votre entrepôt de données SQL Data Warehouse. |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>API REST : Gérer des serveurs et bases de données uniques SQL Database

Pour créer et gérer un serveur, des bases de données et des pare-feux Azure SQL, utilisez les requêtes suivantes de l’API REST.

| Commande | Description |
| --- | --- |
|[Servers - Create or update](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Crée ou met à jour un serveur.|
|[Serveurs - Delete](https://docs.microsoft.com/rest/api/sql/servers/delete)|Supprime un serveur SQL.|
|[Serveurs - Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Obtient un serveur.|
|[Serveurs - List](https://docs.microsoft.com/rest/api/sql/servers/list)|Retourne la liste des serveurs d’un abonnement.|
|[Servers - List by resource Group](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Retourne une liste de serveurs dans un groupe de ressources.|
|[Serveurs - Update](https://docs.microsoft.com/rest/api/sql/servers/update)|Met à jour un serveur existant.|
|[Databases - Create or update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Crée une base de données ou met à jour une base de données existante.|
|[Bases de données - Supprimer](https://docs.microsoft.com/rest/api/sql/databases/delete)|Supprime une base de données.|
|[Bases de données - Obtenir](https://docs.microsoft.com/rest/api/sql/databases/get)|Obtient une base de données.|
|[Databases - List by elastic pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Renvoie une liste des bases de données dans un pool élastique.|
|[Databases - List by server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retourne une liste de bases de données d’un serveur.|
|[Bases de données - Mettre à jour](https://docs.microsoft.com/rest/api/sql/databases/update)|Met à jour une base de données existante.|
|[Firewall rules - Create or update](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Crée ou met à jour une règle de pare-feu.|
|[Firewall rules - Delete](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Supprime une règle de pare-feu.|
|[Firewall rules - Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Obtient une règle de pare-feu.|
|[Firewall rules - List by server](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Retourne une liste de règles de pare-feu.|

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la migration d’une base de données SQL Server, consultez la section [Migrer une base de données Azure SQL](sql-database-single-database-migrate.md).
- Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md).
