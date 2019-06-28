---
title: Restaurer un entrepôt de données Azure SQL | Microsoft Docs
description: Guide pratique pour la restauration d’un entrepôt de données Azure SQ.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ebbcbcc3d0934800980b7d8e00895b11ff2747b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60310418"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Restauration d’un entrepôt de données Azure SQL 
Dans cet article, vous allez découvrir comment effectuer les opérations suivantes dans le portail Azure et PowerShell :

- Créer un point de restauration
- Effectuer une restauration à partir d’un point de restauration automatique ou d’un point de restauration défini par l’utilisateur
- Effectuer une restauration à partir d’une base de données supprimée
- Effectuer une restauration à partir d’une sauvegarde
- Créer une copie de votre entrepôt de données à partir d’un point de restauration défini par l’utilisateur

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Vérifiez votre capacité de DTU.** Chaque SQL Data Warehouse est hébergé par un serveur SQL (par exemple, myserver.database.windows.net) qui dispose d’un quota DTU par défaut.  Avant de pouvoir restaurer un entrepôt de données SQL, vérifiez que le quota DTU restant sur le serveur SQL est suffisant pour la base de données en cours de restauration. Pour savoir comment calculer la capacité DTU nécessaire ou pour demander davantage de capacité DTU, consultez la rubrique [Demander une modification du quota DTU][Request a DTU quota change].

## <a name="restore-through-powershell"></a>Effectuer une restauration par le biais de PowerShell

## <a name="install-powershell"></a>Installer PowerShell
Pour utiliser Azure PowerShell avec SQL Data Warehouse, vous devez installer Azure PowerShell.  Vous pouvez vérifier la version en exécutant **Get-Module -ListAvailable -Name Az**. Pour plus d’informations sur l’installation de la dernière version, consultez la page [Installation et configuration d’Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database-using-powershell"></a>Restaurer une base de données active ou interrompue à l’aide de PowerShell
Pour restaurer une base de données à partir d’un point de restauration, utilisez l’applet de commande PowerShell [Restore-AzSqlDatabase][Restore-AzSqlDatabase].

1. Ouvrez Windows PowerShell.

2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.

3. Sélectionnez l’abonnement contenant la base de données à restaurer.

4. Répertoriez les points de restauration pour la base de données.

5. Sélectionnez le point de restauration souhaité à l’aide de l’élément RestorePointCreationDate.

   > [!NOTE]
   > Quand vous effectuez une restauration, vous pouvez spécifier une valeur ServiceObjectiveName différente (DWU) ou un autre serveur résidant dans une autre région.

6. Restaurez la base de données au niveau du point de restauration souhaité.

7. Vérifiez que la base de données restaurée est en ligne.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Une fois la restauration terminée, vous pouvez configurer votre base de données restaurée en suivant les instructions de la section [Configurer votre base de données après récupération][Configure your database after recovery].
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points-using-powershell"></a>Copier votre entrepôt de données avec des points de restauration définis par l’utilisateur à l’aide de PowerShell
Pour restaurer une base de données à partir d’un point de restauration défini par l’utilisateur, utilisez l’applet de commande PowerShell [Restore-AzSqlDatabase][Restore-AzSqlDatabase].

1. Ouvrez Windows PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement contenant la base de données à restaurer.
4. Créez un point de restauration pour une copie immédiate de votre base de données.
5. Renommez votre base de données avec un nom temporaire.
6. Récupérez le point de restauration le plus récent par la valeur RestorePointLabel spécifiée.
7. Obtenez l’ID de ressource de la base de données pour lancer la restauration.
8. Restaurez la base de données au niveau du point de restauration souhaité.
9. Vérifiez que la base de données restaurée est en ligne.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database-using-powershell"></a>Restaurer une base de données supprimée à l’aide de PowerShell
Pour restaurer une base de données supprimée, utilisez l’applet de commande [Restore-AzSqlDatabase][Restore-AzSqlDatabase].

1. Ouvrez Windows PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement contenant la base de données supprimée à restaurer.
4. Accédez à la base de données supprimée.
5. Restaurez la base de données supprimée.
6. Vérifiez que la base de données restaurée est en ligne.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Une fois la restauration terminée, vous pouvez configurer votre base de données restaurée en suivant les instructions de la section [Configurer votre base de données après récupération][Configure your database after recovery].
>

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Restaurer à partir d’une région géographique Azure à l’aide de PowerShell
Pour récupérer une base de données, utilisez la cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase].

> [!NOTE]
> Vous pouvez effectuer une géorestauration vers Gen2 ! Pour ce faire, spécifiez une valeur ServiceObjectiveName Gen2 (par exemple, DW1000**c**) comme paramètre facultatif.
>

1. Ouvrez Windows PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement contenant la base de données à restaurer.
4. Obtenez la base de données à récupérer.
5. Créez la demande de récupération de la base de données.
6. Vérifiez l’état de la base de données affectée par la géo-restauration.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Pour configurer votre base de données une fois la restauration terminée, consultez la page [Configurer votre base de données après récupération][Configure your database after recovery].
>

La base de données récupérée sera compatible avec le chiffrement transparent des données si la base de données source l’est aussi.

## <a name="restore-through-the-azure-portal"></a>Restaurer par le biais du portail Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Créer un point de restauration défini par l’utilisateur à l’aide du portail Azure
1. Connectez-vous au [portail Azure][Azure portal].

2. Accédez à l’entrepôt de données SQL pour lequel vous voulez créer un point de restauration.

3. En haut du panneau Vue d’ensemble, sélectionnez **+Nouveau point de restauration**.

    ![Nouveau point de restauration](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Spécifiez un nom pour votre point de restauration.

    ![Nom du point de restauration](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Restaurer une base de données active ou interrompue à l’aide du portail Azure
1. Connectez-vous au [portail Azure][Azure portal].
2. Accédez à l’entrepôt de données SQL à partir duquel effectuer la restauration.
3. En haut du panneau Vue d’ensemble, sélectionnez **Restaurer**.

    ![ Présentation de la restauration](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Sélectionnez **Points de restauration automatiques** ou **Points de restauration définis par l’utilisateur**.

    ![Points de restauration automatiques](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Pour l’option Points de restauration définis par l’utilisateur, sélectionnez un **Point de restauration** ou **Créer un point de restauration défini par l’utilisateur**.

    ![Points de restauration définis par l’utilisateur](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Restaurer une base de données supprimée à l’aide du portail Azure
1. Connectez-vous au [portail Azure][Azure portal].
2. Accédez au serveur SQL sur lequel votre base de données supprimée était hébergée.
3. Sélectionnez l’icône Bases de données supprimées dans la table des matières.

    ![Bases de données supprimées](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)

4. Sélectionnez la base de données supprimée que vous souhaitez restaurer.

    ![Sélectionner Bases de données supprimées](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)

5. Spécifiez un nouveau nom de base de données.

    ![Spécifier un nom de base de données](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
