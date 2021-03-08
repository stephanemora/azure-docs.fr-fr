---
title: 'Azure SQL Database : Gérer la rétention des sauvegardes à long terme'
description: Apprenez à stocker et à restaurer les sauvegardes automatisées pour Azure SQL Database dans Stockage Azure (pendant 10 ans maximum) à l’aide du Portail Microsoft Azure et de PowerShell
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: fad19d360f7c476ba71a9bbe00b58387b92f8ac4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690552"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gérer la conservation à long terme des sauvegardes Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database vous permet de configurer une stratégie de [conservation des sauvegardes à long terme](long-term-retention-overview.md) (LTR) afin de conserver automatiquement les sauvegardes dans des conteneurs de stockage Blob Azure distincts pendant une durée maximale de 10 ans. Vous pouvez ensuite récupérer une base de données à l’aide de ces sauvegardes via le portail Azure ou PowerShell. Les stratégies de conservation à long terme sont également prises en charge pour [Azure SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md).

## <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Les sections suivantes vous montrent comment utiliser le portail Azure pour définir des stratégies de conservation à long terme, gérer les sauvegardes de conservation à long terme disponibles et effectuer une restauration à partir d’une sauvegarde disponible.

### <a name="configure-long-term-retention-policies"></a>Configurer des stratégies de rétention à long terme

Vous pouvez configurer SQL Database pour [conserver des sauvegardes automatisées](long-term-retention-overview.md) sur une période plus longue que la période de rétention associée à votre niveau de service.

1. Dans le portail Azure, accédez à votre serveur, puis sélectionnez **Sauvegardes**. Sélectionnez l’onglet **Stratégies de conservation** pour modifier vos paramètres de conservation de sauvegarde.

   ![expérience des stratégies de conservation](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. Sous l’onglet Stratégies de conservation, sélectionnez la ou les bases de données sur lesquelles vous souhaitez définir ou modifier des stratégies de conservation des sauvegardes à long terme. Les bases de données non sélectionnées ne sont pas affectées.

   ![sélectionner la base de données pour configurer les stratégies de conservation de sauvegarde](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. Dans le volet **Configurer les stratégies**, spécifiez la période de conservation souhaitée pour les sauvegardes hebdomadaires, mensuelles ou annuelles. Choisissez une période de conservation de « 0 » pour indiquer qu’aucune conservation de sauvegarde à long terme ne doit être définie.

   ![volet configurer des stratégies](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. Sélectionnez **Appliquer** pour appliquer les paramètres de conservation choisis à toutes les bases de données sélectionnées.

> [!IMPORTANT]
> Lorsque vous activez une stratégie de rétention des sauvegardes à long terme, la première sauvegarde peut ne devenir visible et disponible pour une restauration qu’au bout de 7 jours. Pour en savoir plus sur la cadence des sauvegardes LTR, consultez la section relative à la [rétention des sauvegardes à long terme](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Afficher des sauvegardes et restaurer à partir d’une sauvegarde

Affichez les sauvegardes qui sont conservées pour une base de données spécifique avec une stratégie de conservation à long terme et restaurez à partir de ces sauvegardes.

1. Dans le portail Azure, accédez à votre serveur, puis sélectionnez **Sauvegardes**. Pour afficher les sauvegardes LTR disponibles pour une base de données spécifique, sélectionnez **Gérer** sous la colonne des sauvegardes LTR disponibles. Un volet s’affiche avec la liste des sauvegardes LTR disponibles pour la base de données sélectionnée.

   ![expérience des sauvegardes disponibles](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. Dans le volet **Sauvegardes LTR disponibles**, passez en revue les sauvegardes disponibles. Vous pouvez sélectionner une sauvegarde à partir de laquelle effectuer la restauration ou la supprimer.

   ![afficher les sauvegardes LTR disponibles](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. Pour effectuer une restauration à partir d’une sauvegarde LTR disponible, sélectionnez la sauvegarde à partir de laquelle vous souhaitez effectuer la restauration, puis **Restaurer**.

   ![restaurer à partir d’une sauvegarde LTR disponible](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. Choisissez un nom pour votre nouvelle base de données, puis sélectionnez **Vérifier + créer** pour passer en revue les détails de votre restauration. Sélectionnez **Créer** pour restaurer votre base de données à partir de la sauvegarde choisie.

   ![configurer les détails de la restauration](./media/long-term-backup-retention-configure/restore-ltr.png)

1. Dans la barre d’outils, cliquez sur l’icône de notification pour afficher l’état du travail de restauration.

   ![progression du travail de restauration](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Lorsque le travail de restauration est terminé, ouvrez la page **Bases de données SQL** pour visualiser la base de données nouvellement restaurée.

> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour [extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Utilisation de PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm.

Les sections suivantes vous montrent comment utiliser PowerShell pour configurer la rétention des sauvegardes à long terme, afficher des sauvegardes dans le stockage Azure et restaurer à partir d’une sauvegarde dans le stockage Azure.

### <a name="azure-roles-to-manage-long-term-retention"></a>Rôles Azure pour gérer la conservation à long terme

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

Les autorisations Azure RBAC peuvent être accordées dans l’étendue de l’*abonnement* ou du *groupe de ressources*. Toutefois, pour accéder aux sauvegardes LTR appartenant à un serveur abandonné, l’autorisation doit être accordée dans l’étendue de l’*abonnement* de ce serveur.

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
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
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
> La suppression de sauvegardes de rétention à long terme n’est pas réversible. Pour supprimer une sauvegarde LTR une fois que le serveur a été supprimé, vous devez disposer de l’autorisation Étendue de l’abonnement. Vous pouvez configurer des notifications sur chaque suppression dans Azure Monitor en filtrant sur l’opération « Supprime une sauvegarde de conservation à long terme ». Le journal d’activité contient des informations sur la personne qui a effectué la requête et quand. Consultez [Créer des alertes de journal d’activité](../../azure-monitor/alerts/alerts-activity-log.md) pour obtenir des instructions détaillées.

### <a name="restore-from-ltr-backups"></a>Restaurer à partir de sauvegardes de rétention à long terme

Cet exemple montre comment restaurer à partir d’une sauvegarde de rétention à long terme. Notez que cette interface n’a pas changé, mais que le paramètre d’ID de ressource requiert désormais l’ID de ressource de sauvegarde LTR.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Pour restaurer à partir d’une sauvegarde LTR après la suppression du serveur ou du groupe de ressources, vous devez disposer d’autorisations délimitées au niveau de l’abonnement du serveur, cet abonnement devant être actif. Vous devez également omettre le paramètre facultatif -ResourceGroupName.

> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante. Consultez [Restauration dans le temps](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Limites
- Lors de la restauration à partir d’une sauvegarde LTR, la propriété d’échelle lecture est désactivée. Pour activer la propriété échelle lecture sur la base de données restaurée, mettez à jour la base de données après sa création.
- Vous devez spécifier l’objectif de niveau de service cible quand vous effectuez une restauration à partir d’une sauvegarde LTR créée au moment où la base de données se trouvait dans un pool élastique. 

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez la page [Sauvegardes automatiques](automated-backups-overview.md).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](long-term-retention-overview.md).
