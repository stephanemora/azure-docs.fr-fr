---
title: Informations de référence sur l’API de gestion pour Azure SQL Database Managed Instance | Microsoft Docs
description: Apprenez à créer et à gérer des instances Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 7fb6917e129c015536143a707fd2a89fc5423a99
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323078"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Informations de référence sur l’API de gestion pour Azure SQL Database Managed Instance

Vous pouvez créer et gérer des instances Azure SQL Database Managed Instance via le portail Azure, PowerShell, Azure CLI, l’API REST et Transact-SQL. Dans cet article, vous trouverez une vue d’ensemble des fonctions et de l’API que vous pouvez utiliser pour créer et configurer des instances managées.

## <a name="azure-portal-create-a-managed-instance"></a>Portail Azure : créer une instance managée

Pour un guide de démarrage rapide vous expliquant comment créer une instance Azure SQL Database Managed Instance, consultez [Démarrage rapide : Créer une instance Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-a-managed-instance"></a>PowerShell : créer et gérer une instance managée

Pour créer et gérer le serveur, les bases de données et les pare-feux SQL Azure avec Azure PowerShell, utilisez les applets de commande PowerShell suivants. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-azurerm-ps).

> [!TIP]
> Pour obtenir des exemples de scripts PowerShell, consultez [Quick-start script: Create Azure SQL Managed Instance using PowerShell library](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Applet de commande | Description |
| --- | --- |
|[New-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstance)|Crée une instance Azure SQL Database Managed Instance |
|[Get-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Get-AzureRmSqlInstance)|Retourne des informations sur l’instance Azure SQL Database Managed Instance|
|[Set-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlInstance)|Définit les propriétés d’une instance Azure SQL Database Managed Instance|
|[Remove-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlInstance)|Supprime une instance Azure SQL Database Managed Instance|

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Azure CLI : gérer des bases de données et des serveurs logiques

Pour créer et gérer un serveur, des bases de données et des pare-feu Azure SQL avec [Azure CLI](/cli/azure), utilisez les commandes [Azure CLI SQL Managed Instance](/cli/azure/sql/mi) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows.

> [!TIP]
> Pour un guide de démarrage rapide Azure CLI, consultez [Working with SQL Managed Instance using Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
>

| Applet de commande | Description |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-create) |Crée une instance managée|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-list)|Liste les instances managées disponibles|
|[az sql mi show](/cli/azure/sql/db#az-sql-mi-show)|Obtient les détails concernant une instance managée|
|[az sql mi update](/cli/azure/sql/db#az-sql-mi-update)|Met à jour une instance managée|
|[az sql mi delete](/cli/azure/sql/db#az-sql-mi-delete)|Supprime une instance managée|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL : gérer des bases de données et des serveurs logiques

Pour créer et gérer une base de données Azure SQL Database Managed Instance après avoir créé l’instance managée, utilisez les commandes T-SQL suivantes. Vous pouvez émettre ces commandes via le portail Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) ou tout autre programme capable de se connecter à un serveur Azure SQL Database et de transmettre des commandes Transact-SQL.

> [!TIP]
> Pour des guides de démarrage rapide vous expliquant comment configurer et vous connecter à une instance managée en utilisant SQL Server Management Studio sur Microsoft Windows, consultez [Démarrage rapide : Configurer la machine virtuelle Azure pour qu’elle se connecte à Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vm.md) et [Démarrage rapide : Configurer une connexion point à site sur Azure SQL Database Managed Instance à partir d’un emplacement local](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Vous ne pouvez pas créer ou supprimer une instance managée à l’aide de Transact-SQL.

| Commande | Description |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Crée une base de données Managed Instance. Vous devez être connecté à la base de données MASTER pour créer une base de données.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modifie une base de données Azure SQL Managed Instance.|

## <a name="rest-api-manage-logical-servers-and-databases"></a>API REST : gérer des bases de données et des serveurs logiques

Pour créer et gérer un serveur une instance Azure SQL Database Managed Instance, utilisez les requêtes d’API REST suivantes.

| Commande | Description |
| --- | --- |
|[Instances managées - Créer ou Mettre à jour](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Crée ou met à jour une instance managée.|
|[Instances managées - Supprimer](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Supprime une instance managée.|
|[Instances managées - Obtenir](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Obtient une instance managée.|
|[Instances managées - Lister](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Retourne la liste des instances managées d’un abonnement.|
|[Instances managées - Lister par groupe de ressources](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Retourne la liste des instances managées d’un groupe de ressources.|
|[Instances managées - Mettre à jour](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Met à jour une instance managée.|

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la migration d’une base de données SQL Server, consultez la section [Migrer une base de données SQL](sql-database-cloud-migrate.md).
- Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md).
