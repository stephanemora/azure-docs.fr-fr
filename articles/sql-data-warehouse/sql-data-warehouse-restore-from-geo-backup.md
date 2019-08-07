---
title: Restaurer une base de données Azure SQL Data Warehouse à partir d’une sauvegarde géo-redondante | Microsoft Docs
description: Guide pratique pour la restauration géo-redondante d’Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 768646522c2589c302fd7a58031d4ebdb7fcdc12
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68425876"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Restauration géo-redondante d’Azure SQL Data Warehouse

Dans cet article, vous allez apprendre à restaurer votre entrepôt de données à partir d’une sauvegarde géo-redondante via le portail Azure et PowerShell.

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Vérifiez votre capacité de DTU.** Chaque SQL Data Warehouse est hébergé par un serveur SQL (par exemple, myserver.database.windows.net) qui dispose d’un quota DTU par défaut. Vérifiez que le quota DTU restant sur le serveur SQL est suffisant pour la base de données en cours de restauration. Pour savoir comment calculer la capacité DTU nécessaire ou pour demander davantage de capacité DTU, consultez [Request a DTU quota change][Request a DTU quota change](Demander une modification du quota DTU).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Restaurer à partir d’une région géographique Azure à l’aide de PowerShell

Pour effectuer une restauration à partir d’une sauvegarde géo-redondante, utilisez le cmdlet [Get-AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] and [Restore-AzSqlDatabase][Restore-AzSqlDatabase].

> [!NOTE]
> Vous pouvez effectuer une géorestauration vers Gen2 ! Pour ce faire, spécifiez une valeur ServiceObjectiveName Gen2 (par exemple, DW1000**c**) comme paramètre facultatif.
>

1. Avant de commencer, veillez à [installer Azure PowerShell][Install Azure PowerShell].
2. Ouvrez PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement contenant l’entrepôt de données à restaurer.
4. Obtenez l’entrepôt de données à récupérer.
5. Créez la requête de récupération de l’entrepôt de données.
6. Vérifiez l’état de l’entrepôt de données affectée par la géo-restauration.
7. Pour configurer votre entrepôt de données une fois la restauration terminée, consultez [Configurer votre base de données après récupération][Configure your database after recovery].

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

La base de données récupérée sera compatible avec le chiffrement transparent des données si la base de données source l’est aussi.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Restaurer à partir d’une région géographique Azure à l’aide du portail Azure

Suivez les étapes décrites ci-dessous pour restaurer Azure SQL Data Warehouse à partir d’une sauvegarde géo-redondante :

1. Connectez-vous à votre compte[Portail Azure][Azure portal].
1. Cliquez sur **+ créer une ressource** et recherchez SQL Data Warehouse, puis cliquez sur **Créer**.

    ![Nouveau DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Renseignez les informations demandées dans l' onglet **Bases**, puis cliquez sur **Suivant : Paramètres supplémentaires**.

    ![Concepts de base](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. Pour **utiliser le paramètre de données existant**, sélectionnez **Sauvegarde** et sélectionnez la sauvegarde appropriée dans les options de défilement vers le dessous. Cliquez sur **Revoir + créer**.
 
   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. Une fois l’entrepôt de données restauré, vérifiez que l'**état** est En ligne.

## <a name="next-steps"></a>Étapes suivantes
- [Restaurer un entrepôt de données existant][Restore an existing data warehouse]
- [Restaurer un entrepôt de données supprimé][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
