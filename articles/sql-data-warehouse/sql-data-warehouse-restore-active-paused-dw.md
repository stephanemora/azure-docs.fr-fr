---
title: Restaurer un entrepôt de données existant
description: Guide pratique pour la restauration d’un Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d0bcf9ca6373984989d24efd2af4ffbbb19c5548
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759682"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Restaurer un Azure SQL Data Warehouse existant

Dans cet article, vous allez apprendre à restaurer un Azure SQL Data Warehouse existant à partir du portail Azure et de PowerShell :

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Vérifiez votre capacité de DTU.** Chaque SQL Data Warehouse est hébergé par un serveur SQL (par exemple, myserver.database.windows.net) qui dispose d’un quota DTU par défaut. Vérifiez que le quota DTU restant sur le serveur SQL est suffisant pour la base de données en cours de restauration. Pour savoir comment calculer la capacité DTU nécessaire ou pour demander davantage de capacité DTU, consultez [Request a DTU quota change](sql-data-warehouse-get-started-create-support-ticket.md)(Demander une modification du quota DTU).

## <a name="before-you-begin"></a>Avant de commencer

1. Assurez-vous d’[installer Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Utilisez un point de restauration existant à partir duquel vous souhaitez effectuer la restauration. Si vous souhaitez créer une nouvelle restauration, consultez [le didacticiel pour créer un nouveau point de restauration défini par l’utilisateur](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Restaurer un entrepôt de données existant via PowerShell

Pour restaurer un entrepôt de données à partir d’un point de restauration, utilisez le cmdlet [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) de PowerShell.

1. Ouvrez PowerShell.

2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.

3. Sélectionnez l’abonnement contenant la base de données à restaurer.

4. Répertoriez les points de restauration pour l’entrepôt de données.

5. Sélectionnez le point de restauration souhaité à l’aide de l’élément RestorePointCreationDate.

6. Restaurez l’entrepôt de données sur le point de restauration souhaité à l’aide du cmdlet [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) de PowerShell.
        1. Pour restaurer le SQL Data Warehouse sur un autre serveur logique, veillez à spécifier le nom du serveur logique.  Ce serveur logique peut également se trouver dans un groupe de ressources et une région différents.
        2. Pour effectuer une restauration vers un autre abonnement, utilisez le bouton « Déplacer » pour déplacer le serveur logique vers un autre abonnement.

7. Vérifiez que l’entrepôt de données restauré est en ligne.

8. Une fois la restauration terminée, vous pouvez configurer votre entrepôt de données récupéré en suivant [Configurer votre base de données après récupération](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Restaurer un entrepôt de données existant via le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Accédez à l’entrepôt SQL Data Warehouse à partir duquel vous souhaitez effectuer la restauration.
3. En haut du panneau Vue d’ensemble, sélectionnez **Restaurer**.

    ![ Présentation de la restauration](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Sélectionnez **Points de restauration automatiques** ou **Points de restauration définis par l’utilisateur**. Si l’entrepôt de données n’a pas de points de restauration automatique, patientez quelques heures ou créez un point de restauration défini par l’utilisateur avant la restauration. Pour les points de restauration définis par l’utilisateur, sélectionnez un point existant ou créez-en un nouveau. Pour le **serveur**, vous pouvez choisir un serveur logique dans un groupe de ressources et une région différents ou en créer un nouveau. Après avoir fourni tous les paramètres, cliquez sur**Vérifier + Restaurer**.

    ![Points de restauration automatiques](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Étapes suivantes
- [Restaurer un entrepôt de données supprimé](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurer à partir d’un entrepôt de données géo-sauvegardé](sql-data-warehouse-restore-from-geo-backup.md)

 