---
title: Restaurer un Azure SQL Data Warehouse supprimé | Microsoft Docs
description: Guide pratique pour restaurer un Azure SQL Data Warehouse supprimé.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 376a50a79858aee34aa71d172ca5836646a6651d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68425868"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>Restaurer un Azure SQL Data Warehouse supprimé

Dans cet article, vous allez apprendre à restaurer un Azure SQL Data Warehouse supprimé à partir du portail Azure et de PowerShell :

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Vérifiez votre capacité de DTU.** Chaque SQL Data Warehouse est hébergé par un serveur SQL (par exemple, myserver.database.windows.net) qui dispose d’un quota DTU par défaut.  Vérifiez que le quota DTU restant sur le serveur SQL est suffisant pour la base de données en cours de restauration. Pour savoir comment calculer la capacité DTU nécessaire ou pour demander davantage de capacité DTU, consultez [Request a DTU quota change][Request a DTU quota change](Demander une modification du quota DTU).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Restaurer un entrepôt de données supprimé via PowerShell

Pour restaurer un entrepôt de données supprimée, utilisez le cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase]. Si le serveur logique correspondant a également été supprimé, vous ne pouvez pas restaurer cet entrepôt de données.

1. Avant de commencer, veillez à [installer Azure PowerShell][Install Azure PowerShell].
2. Ouvrez PowerShell.
3. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
4. Sélectionnez l’abonnement qui contient l’entrepôt de données supprimé à restaurer.
5. Récupérez l’entrepôt de données supprimé spécifique.
6. Restaurez l’entrepôt de données supprimé
    1. Pour restaurer le SQL Data Warehouse sur un autre serveur logique, veillez à spécifier le nom de l’autre serveur logique.  Ce serveur logique peut également se trouver dans un groupe de ressources et une région différents.
    1. Pour effectuer une restauration vers un autre abonnement, utilisez le bouton [Déplacer][Move] pour déplacer le serveur logique vers un autre abonnement.
1. Vérifiez que l’entrepôt de données restauré est en ligne.
1. Une fois la restauration terminée, vous pouvez configurer votre entrepôt de données récupéré en suivant [Configurer votre base de données après récupération][Configure your database after recovery].

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>" 
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Restaurer une base de données supprimée à l’aide du portail Azure

1. Connectez-vous au [Portail Azure][Azure portal].
2. Accédez au serveur SQL sur lequel votre entrepôt de données supprimé était hébergé.
3. Sélectionnez l’icône **Bases de données supprimées** dans la table des matières.

    ![Bases de données supprimées](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Sélectionnez le SQL Data Warehouse supprimé que vous souhaitez restaurer.

    ![Sélectionner Bases de données supprimées](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Spécifiez un nouveau **nom pour la base de données** et cliquez sur **OK**

    ![Spécifier un nom de base de données](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Étapes suivantes
- [Restaurer un entrepôt de données existant][Restore an existing data warehouse]
- [Restaurer à partir d’un entrepôt de données géo-sauvegardé][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[support ticket]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket
[Move]:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
