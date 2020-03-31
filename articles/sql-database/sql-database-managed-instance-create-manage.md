---
title: Informations de référence sur l’API de gestion pour Managed instance
description: Apprenez à créer et à gérer des instances Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 713217a933c646cc4d04759f5697bbc0312827ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79232509"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Informations de référence sur l’API de gestion pour Azure SQL Database Managed Instance

Vous pouvez créer et gérer des instances Azure SQL Database Managed Instance via le portail Azure, PowerShell, Azure CLI, l’API REST et Transact-SQL. Dans cet article, vous trouverez une vue d’ensemble des fonctions et de l’API que vous pouvez utiliser pour créer et configurer des instances managées.

## <a name="azure-portal-create-a-managed-instance"></a>Portail Azure : Créer une instance gérée

Pour un guide de démarrage rapide vous expliquant comment créer une instance Azure SQL Database Managed Instance, consultez [Démarrage rapide : Créer une instance Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell : créer et gérer des instances gérées

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm.

Pour créer et gérer des instances gérées avec Azure PowerShell, utilisez les cmdlets PowerShell suivantes. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Pour obtenir des exemples de scripts PowerShell, consultez [Quick-start script: Create Azure SQL Managed Instance using PowerShell library](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Applet de commande | Description |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Crée une instance Azure SQL Database Managed Instance |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Retourne des informations sur l’instance Azure SQL Database Managed Instance|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Définit les propriétés d’une instance Azure SQL Database Managed Instance|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Supprime une instance Azure SQL Database Managed Instance|
|[Nouvelle AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Crée une base de données Azure SQL Database Managed Instance|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Retourne des informations sur la base de données Azure SQL Managed Instance|
|[Nouvelle AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Supprime une base de données Azure SQL Database Managed Instance|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Restaure une base de données Azure SQL Database Managed Instance|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI : créer et gérer des instances gérées

Pour créer et gérer des instances gérées avec [Azure CLI](/cli/azure), utilisez les commandes [Azure CLI SQL Managed Instance](/cli/azure/sql/mi) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows.

> [!TIP]
> Pour un guide de démarrage rapide Azure CLI, consultez [Working with SQL Managed Instance using Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Applet de commande | Description |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Crée une instance managée|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Liste les instances managées disponibles|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Obtient les détails concernant une instance managée|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Met à jour une instance managée|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Supprime une instance managée|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Crée une base de données managée|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Liste les bases de données managées disponibles|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Restaure une base de données managée|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Supprime une base de données managée|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL : Créer et gérer des bases de données d’instance

Pour créer et gérer une base de données d’instance après avoir créé l’instance managée, utilisez les commandes T-SQL suivantes. Vous pouvez émettre ces commandes via le portail Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) ou tout autre programme capable de se connecter à un serveur Azure SQL Database et de transmettre des commandes Transact-SQL.

> [!TIP]
> Pour des guides de démarrage rapide vous expliquant comment configurer et vous connecter à une instance managée en utilisant SQL Server Management Studio sur Microsoft Windows, consultez [Démarrage rapide : Configurer la machine virtuelle Azure pour qu’elle se connecte à Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vm.md) et [Démarrage rapide : Configurer une connexion point à site sur Azure SQL Database Managed Instance à partir d’un emplacement local](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Vous ne pouvez pas créer ou supprimer une instance managée à l’aide de Transact-SQL.

| Commande | Description |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Crée une base de données Managed Instance. Vous devez être connecté à la base de données master pour créer une base de données.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modifie une base de données Azure SQL Managed Instance.|

## <a name="rest-api-create-and-manage-managed-instances"></a>API REST : créer et gérer des instances gérées

Pour créer et gérer des instances gérées, utilisez ces requêtes d’API REST.

| Commande | Description |
| --- | --- |
|[Instances managées - Créer ou Mettre à jour](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Crée ou met à jour une instance managée.|
|[Instances managées - Supprimer](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Supprime une instance managée.|
|[Instances managées - Obtenir](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Obtient une instance managée.|
|[Instances managées - Lister](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Retourne la liste des instances managées d’un abonnement.|
|[Instances managées - Lister par groupe de ressources](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Retourne la liste des instances managées d’un groupe de ressources.|
|[Instances managées - Mettre à jour](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Met à jour une instance managée.|

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la migration d’une base de données SQL Server, consultez la section [Migrer une base de données Azure SQL](sql-database-single-database-migrate.md).
- Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md).
