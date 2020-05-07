---
title: 'Base de données unique : Gérer la rétention des sauvegardes à long terme'
description: Découvrez comment stocker et restaurer des sauvegardes automatisées pour une base de données unique ou mise en pool Azure SQL Database dans un stockage Azure (jusqu’à 10 ans) à l’aide du portail Azure et de PowerShell
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/14/2020
ms.openlocfilehash: 1fedf889fa8d39a926067eb227b895d4fe1e3c39
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509344"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gérer la conservation à long terme des sauvegardes Azure SQL Database

Dans Azure SQL Database, vous pouvez configurer une base de données unique ou mise en pool avec une stratégie de [conservation des sauvegardes à long terme](sql-database-long-term-retention.md) (LTR) afin de conserver automatiquement les sauvegardes de base de données dans des conteneurs de stockage Blob Azure séparés pendant une durée maximale de 10 ans. Vous pouvez ensuite récupérer une base de données à l’aide de ces sauvegardes via le portail Azure ou PowerShell.

> [!IMPORTANT]
> Pour Azure SQL Database Managed Instance, consultez [Prise en charge de LTR pour Managed Instance](sql-database-long-term-retention.md#managed-instance-support).

## <a name="using-azure-portal"></a>En passant par le portail Azure

Les sections suivantes vous montrent comment utiliser le portail Azure pour configurer la rétention à long terme, afficher des sauvegardes dans une rétention à long terme et restaurer la sauvegarde à partir d’une rétention à long terme.

### <a name="configure-long-term-retention-policies"></a>Configurer des stratégies de rétention à long terme

Vous pouvez configurer SQL Database pour [conserver des sauvegardes automatisées](sql-database-long-term-retention.md) sur une période plus longue que la période de rétention associée à votre niveau de service.

1. Dans le portail Azure, sélectionnez votre serveur SQL, puis cliquez sur **Gérer les sauvegardes**. Dans l’onglet **Configurer les stratégies**, cochez la case pour la base de données sur laquelle vous souhaitez définir ou modifier des stratégies de rétention des sauvegardes à long terme. Si la case à cocher située en regard de la base de données n’est pas sélectionnée, les modifications de la stratégie ne s’appliqueront pas à cette base de données.  

   ![lien gérer les sauvegardes](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Dans le volet **Configure policies** (Configurer des stratégies), indiquez si vous souhaitez conserver des sauvegardes à une fréquence hebdomadaire, mensuelle ou annuelle, et spécifiez la période de rétention pour chacune.

   ![configurer des stratégies](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Lorsque vous avez terminé, cliquez sur **Appliquer**.

> [!IMPORTANT]
> Lorsque vous activez une stratégie de rétention des sauvegardes à long terme, la première sauvegarde peut ne devenir visible et disponible pour une restauration qu’au bout de 7 jours. Pour en savoir plus sur la cadence des sauvegardes LTR, consultez la section relative à la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Afficher des sauvegardes et restaurer à partir d’une sauvegarde

Affichez les sauvegardes qui sont conservées pour une base de données spécifique avec une stratégie de rétention à long terme, et restaurez à partir de ces sauvegardes.

1. Dans le portail Azure, sélectionnez votre serveur SQL, puis cliquez sur **Gérer les sauvegardes**. Dans l’onglet **Sauvegardes disponibles**, sélectionnez la base de données pour laquelle vous souhaitez afficher les sauvegardes disponibles.

   ![sélectionner la base de données](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

1. Dans le volet **Sauvegardes disponibles**, passez en revue les sauvegardes disponibles.

   ![afficher les sauvegardes](./media/sql-database-long-term-retention/ltr-available-backups.png)

1. Sélectionnez la sauvegarde à partir de laquelle vous souhaitez restaurer, puis spécifiez le nouveau nom de la base de données.

   ![restauration](./media/sql-database-long-term-retention/ltr-restore.png)

1. Cliquez sur **OK** pour restaurer votre base de données à partir de la sauvegarde dans le stockage SQL Azure vers une nouvelle base de données.

1. Dans la barre d’outils, cliquez sur l’icône de notification pour visualiser l’état du travail de restauration.

   ![progression du travail de restauration](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

1. Lorsque le travail de restauration est terminé, ouvrez la page **Bases de données SQL** pour visualiser la base de données nouvellement restaurée.

> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour [extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Utilisation de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm.

Les sections suivantes vous montrent comment utiliser PowerShell pour configurer la rétention des sauvegardes à long terme, afficher des sauvegardes dans le stockage SQL Azure et restaurer à partir d’une sauvegarde dans le stockage SQL Azure.

### <a name="rbac-roles-to-manage-long-term-retention"></a>Rôles RBAC pour gérer la rétention à long terme

Pour **Get-AzSqlDatabaseLongTermRetentionBackup** et **Restore-AzSqlDatabase**, vous devez avoir l’un des rôles suivants :

- Rôle Propriétaire de l’abonnement
- Rôle Contributeur de SQL Server
- Rôle personnalisé avec les autorisations suivantes :

   Microsoft.Sql/locations/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

Pour **Remove-AzSqlDatabaseLongTermRetentionBackup**, vous devez avoir l’un des rôles suivants :

- Rôle Propriétaire de l’abonnement
- Rôle personnalisé avec l’autorisation suivante :

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> Le rôle Contributeur de SQL Server n’a pas l’autorisation de supprimer les sauvegardes LTR.

Les autorisations RBAC peuvent être accordées dans l’étendue de l’*abonnement* ou du *groupe de ressources*. Toutefois, pour accéder aux sauvegardes LTR appartenant à un serveur abandonné, l’autorisation doit être accordée dans l’étendue de l’*abonnement* de ce serveur.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Créer une stratégie de rétention à long terme

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Afficher des stratégies de rétention à long terme

Cet exemple montre comment répertorier les stratégies de rétention à long terme au sein d’un serveur

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | `
    Get-AzSqlDatabaseLongTermRetentionPolicy -Current

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -Current
```

### <a name="clear-an-ltr-policy"></a>Effacer une stratégie de rétention à long terme

Cet exemple montre comment effacer une stratégie de rétention à long terme d’une base de données

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Afficher des sauvegardes de rétention à long terme

Cet exemple montre comment répertorier les sauvegardes de rétention à long terme au sein d’un serveur.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Supprimer des sauvegardes de rétention à long terme

Cet exemple montre comment supprimer une sauvegarde de rétention à long terme de la liste des sauvegardes.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> La suppression de sauvegardes de rétention à long terme n’est pas réversible. Pour supprimer une sauvegarde LTR une fois que le serveur a été supprimé, vous devez disposer de l’autorisation Étendue de l’abonnement. Vous pouvez configurer des notifications sur chaque suppression dans Azure Monitor en filtrant sur l’opération « Supprime une sauvegarde de conservation à long terme ». Le journal d’activité contient des informations sur la personne qui a effectué la requête et quand. Consultez [Créer des alertes de journal d’activité](../azure-monitor/platform/alerts-activity-log.md) pour obtenir des instructions détaillées.

### <a name="restore-from-ltr-backups"></a>Restaurer à partir de sauvegardes de rétention à long terme

Cet exemple montre comment restaurer à partir d’une sauvegarde de rétention à long terme. Notez que cette interface n’a pas changé, mais que le paramètre d’ID de ressource requiert désormais l’ID de ressource de sauvegarde de rétention à long terme.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Pour effectuer une restauration à partir d’une sauvegarde LTR après la suppression du serveur, vous devez disposer d’autorisations étendues à l’abonnement du serveur, cet abonnement devant être actif. Vous devez également omettre le paramètre facultatif -ResourceGroupName.

> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante. Consultez [Restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez la page [Sauvegardes automatiques](sql-database-automated-backups.md).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).
