---
title: Gérer la rétention des sauvegardes à long terme Azure SQL Database | Microsoft Docs
description: Découvrez comment stocker des sauvegardes automatisées dans le stockage SQL Azure, puis les restaurer
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 24d453dc705eb2d0ee7cb77f2ec247845247d0a8
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113629"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gérer la rétention des sauvegardes à long terme Azure SQL Database

Vous pouvez configurer la base de données SQL Azure avec une stratégie de [rétention des sauvegardes à long terme](sql-database-long-term-retention.md) pour conserver automatiquement des sauvegardes dans le stockage Blob Azure pendant 10 ans maximum. Vous pouvez ensuite récupérer une base de données à l’aide de ces sauvegardes via le portail Azure ou PowerShell.

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Utiliser le portail Azure pour configurer des stratégies de rétention à long terme et restaurer des sauvegardes

Les sections suivantes vous montrent comment utiliser le portail Azure pour configurer la rétention à long terme, afficher des sauvegardes dans une rétention à long terme et restaurer la sauvegarde à partir d’une rétention à long terme.

### <a name="configure-long-term-retention-policies"></a>Configurer des stratégies de rétention à long terme

Vous pouvez configurer SQL Database pour [conserver des sauvegardes automatisées](sql-database-long-term-retention.md) sur une période plus longue que la période de rétention associée à votre niveau de service. 

1. Dans le portail Azure, sélectionnez votre serveur SQL, puis cliquez sur **Gérer les sauvegardes**. Dans l’onglet **Configure policies** (Configurer des stratégies), sélectionnez la base de données sur laquelle vous souhaitez définir ou modifier des stratégies de rétention des sauvegardes à long terme.

   ![lien gérer les sauvegardes](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Dans le volet **Configure policies** (Configurer des stratégies), indiquez si vous souhaitez conserver des sauvegardes à une fréquence hebdomadaire, mensuelle ou annuelle, et spécifiez la période de rétention pour chacune. 

   ![configurer des stratégies](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Lorsque vous avez terminé, cliquez sur **Appliquer**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Afficher des sauvegardes et restaurer à partir d’une sauvegarde à l’aide du portail Azure

Affichez les sauvegardes qui sont conservées pour une base de données spécifique avec une stratégie de rétention à long terme, et restaurez à partir de ces sauvegardes. 

1. Dans le portail Azure, sélectionnez votre serveur SQL, puis cliquez sur **Gérer les sauvegardes**. Dans l’onglet **Sauvegardes disponibles**, sélectionnez la base de données pour laquelle vous souhaitez afficher les sauvegardes disponibles.

   ![sélectionner la base de données](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. Dans le volet **Sauvegardes disponibles**, passez en revue les sauvegardes disponibles. 

   ![afficher les sauvegardes](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Sélectionnez la sauvegarde à partir de laquelle vous souhaitez restaurer, puis spécifiez le nouveau nom de la base de données.

   ![restauration](./media/sql-database-long-term-retention/ltr-restore.png)

5. Cliquez sur **OK** pour restaurer votre base de données à partir de la sauvegarde dans le stockage SQL Azure vers une nouvelle base de données.

6. Dans la barre d’outils, cliquez sur l’icône de notification pour visualiser l’état du travail de restauration.

   ![progression du travail de restauration à partir du coffre](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Lorsque le travail de restauration est terminé, ouvrez la page **Bases de données SQL** pour visualiser la base de données nouvellement restaurée.

> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour [extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Utiliser PowerShell pour configurer des stratégies de rétention à long terme et restaurer des sauvegardes

Les sections suivantes vous montrent comment utiliser PowerShell pour configurer la rétention des sauvegardes à long terme, afficher des sauvegardes dans le stockage SQL Azure et restaurer à partir d’une sauvegarde dans le stockage SQL Azure.

> [!IMPORTANT]
> L’API de rétention à long terme V2 est prise en charge dans les versions de PowerShell suivantes :
- [AzureRM.Sql-4.5.0](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0) ou version ultérieure
- [AzureRM-6.1.0](https://www.powershellgallery.com/packages/AzureRM/6.1.0) ou version ultérieure
> 

### <a name="create-an-ltr-policy"></a>Créer une stratégie de rétention à long terme

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Afficher des stratégies de rétention à long terme
Cet exemple montre comment répertorier les stratégies de rétention à long terme au sein d’un serveur

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Effacer une stratégie de rétention à long terme
Cet exemple montre comment effacer une stratégie de rétention à long terme d’une base de données

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Afficher des sauvegardes de rétention à long terme

Cet exemple montre comment répertorier les sauvegardes de rétention à long terme au sein d’un serveur. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Supprimer des sauvegardes de rétention à long terme

Cet exemple montre comment supprimer une sauvegarde de rétention à long terme de la liste des sauvegardes.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Restaurer à partir de sauvegardes de rétention à long terme
Cet exemple montre comment restaurer à partir d’une sauvegarde de rétention à long terme. Notez que cette interface n’a pas changé, mais que le paramètre d’ID de ressource requiert désormais l’ID de ressource de sauvegarde de rétention à long terme. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante. Consultez [Restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez la page [Sauvegardes automatiques](sql-database-automated-backups.md).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).
