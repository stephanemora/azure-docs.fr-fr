---
title: Applets de commande PowerShell pour Azure SQL Data Warehouse
description: Recherchez les principaux applets de commande PowerShell pour Azure SQL Data Warehouse, y compris comment suspendre et reprendre une base de données.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e62f12123ae9af4f5e09d19622ba1558c2f43184
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790670"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Applets de commande PowerShell et API REST pour SQL Data Warehouse
De nombreuses tâches d’administration de SQL Data Warehouse peuvent être gérées à l’aide d’applets de commande Azure PowerShell ou d’API REST.  Voici quelques exemples d’utilisation des commandes PowerShell pour automatiser les tâches courantes dans SQL Data Warehouse.  Pour obtenir de bons exemples REST, consultez l’article [Gérer l’évolutivité avec REST][Manage scalability with REST].

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Prise en main des applets de commande Azure PowerShell
1. Ouvrez Windows PowerShell.
2. À l’invite de PowerShell, exécutez les commandes suivantes pour vous connecter à Azure Resource Manager et sélectionnez votre abonnement.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Exemple : suspendre une base de données SQL Data Warehouse
Une base de données appelée « Database02 » et hébergée sur un serveur appelé « Server01 » est interrompue.  Le serveur est un groupe de ressources Azure appelé « ResourceGroup1 ».

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Une variante, cet exemple dirige l’objet récupéré à [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase].  En conséquence, la base de données est interrompue. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Exemple : démarrer une base de données SQL Data Warehouse
Les opérations d’une base de données appelée « Database02 » et hébergée sur un serveur « Server01 » sont reprises. Le serveur est hébergé dans un groupe de ressources appelé « ResourceGroup1 ».

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Une variante, dans cet exemple une base de données appelée « Database02 » est récupérée d’un serveur appelé « Server01 » hébergé dans un groupe de ressources appelé « ResourceGroup1 ». Elle dirige l’objet récupéré à [Resume-AzSqlDatabase][Resume-AzSqlDatabase].

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Si votre serveur est nommé foo.database.windows.net, utilisez « foo » en tant que nom du serveur dans les applets de commande PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Autres applets de commande PowerShell prises en charge
Ces applets de commande PowerShell sont prises en charge avec Azure SQL Data Warehouse.

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoints][Get-AzSqlDatabaseRestorePoints]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Resume-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’exemples PowerShell, consultez :

* [Création de SQL Data Warehouse à l’aide de PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Restauration de base de données][Database restore]

Pour d’autres tâches pouvant être automatisées avec PowerShell, consultez [Applets de commande Azure SQL Database][Azure SQL Database Cmdlets]. Notez que certaines applets de commande Azure SQL Database ne sont pas prises en charge pour Azure SQL Data Warehouse.  Pour obtenir la liste des tâches pouvant être automatisées avec REST, consultez [Opérations pour Azure SQL Database][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/az.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoints
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
