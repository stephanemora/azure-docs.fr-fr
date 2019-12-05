---
title: 'Instance gérée : limite de restauration dans le temps'
description: Restaurer une base de données SQL dans une instance gérée à un point antérieur dans le temps.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: b106b1da5d012309e8d92c8e9555ee3982602e12
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707662"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Restaurer une base de données SQL dans une instance gérée à un point antérieur dans le temps

Utilisez la limite de restauration dans le temps (PITR) pour créer une base de données en tant que copie d’une autre base de données à un moment donné dans le passé. Cet article explique comment effectuer une limite de restauration dans le temps d’une base de données dans une instance gérée Azure SQL Database.

La limite de restauration dans le temps est utile dans les scénarios de récupération, tels que les incidents provoqués par des erreurs, les données chargées de façon incorrecte ou la suppression de données cruciales. Vous pouvez également l’utiliser simplement à des fins de test ou d’audit. Les fichiers de sauvegarde sont conservés pendant une période comprise entre 7 et 35 jours, en fonction des paramètres de votre base de données.

La limite de restauration dans le temps peut :

- Restaurer une base de données à partir d’une base de données existante.
- Restaurer une base de données à partir d’une base de données supprimée.

En outre, pour une instance gérée, la limite de restauration dans le temps peut également :

- Restaurer une base de données dans la même instance managée.
- Restaurer une base de données vers une autre instance managée.

> [!NOTE]
> La limite de restauration dans le temps d’une instance managée entière n’est pas possible. Cet explique uniquement ce qui est possible : une limite de restauration dans le temps d’une base de données hébergée sur une instance gérée.

## <a name="limitations"></a>Limites

Lorsque vous restaurez d’une instance gérée vers une autre, les deux instances doivent se trouver dans le même abonnement et la même région. La restauration inter-régions et inter-abonnements ne sont actuellement pas prises en charge.

> [!WARNING]
> Tenez compte de la taille de stockage de votre instance gérée. Selon la taille des données à restaurer, vous risquez de manquer de stockage d’instance. S’il n’y a pas assez d’espace pour les données restaurées, utilisez une approche différente.

Le tableau suivant présente les scénarios de limite de restauration dans le temps pour une instance gérée :

|           |Restaurer la base de données existante sur la même instance gérée| Restaurer la base de données existante sur une autre instance gérée|Restaurer la base de données déposée sur la même instance gérée|Restaurer la base de données déposée sur une autre instance gérée|
|:----------|:----------|:----------|:----------|:----------|
|**Portail Azure**| OUI|Non |Non|Non|
|**Interface de ligne de commande Azure**|OUI |OUI |Non|Non|
|**PowerShell**| OUI|OUI |OUI|OUI|

## <a name="restore-an-existing-database"></a>Restaurer une base de données existante

Restaurez une base de données existante sur la même instance à l’aide du portail Azure, de PowerShell ou d’Azure CLI. Pour restaurer une base de données sur une autre instance, utilisez PowerShell ou Azure CLI en spécifiant les propriétés du groupe de ressources et de l’instance gérée cibles. Si vous spécifiez pas ces paramètres, la base de données sera restaurée par défaut sur l’instance existante. Le portail Azure ne prend pas actuellement en charge la restauration vers une autre instance.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Accédez à votre instance gérée et sélectionnez la base de données à restaurer.
3. Sélectionnez **Restaurer** dans la page de la base de données :

    ![Restaurer une base de données à l’aide du portail Azure](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Dans la page **Restaurer**, sélectionnez le point pour la date et l’heure auquel restaurer la base de données.
5. Sélectionnez **Confirmer** pour restaurer votre base de données. Cette action démarre le processus de restauration qui crée une nouvelle base de données et est rempli avec les données de la base de données d’origine au point spécifié dans le temps. Pour plus d’informations sur le processus de récupération, consultez [Heure de récupération](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Si Azure PowerShell n’est pas encore installé, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Pour restaurer la base de données à l’aide de PowerShell, spécifiez vos valeurs pour les paramètres de la commande suivante. Exécutez ensuite la commande :

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Pour restaurer la base de données vers une autre instance gérée, spécifiez également le nom du groupe de ressources et le nom de l’instance gérée cibles :  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Pour plus d’informations, consultez [.Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Si Azure CLI n’est pas encore installé, consultez [Installer l’interface de ligne de commande Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Pour restaurer la base de données à l’aide d’Azure CLI, spécifiez vos valeurs pour les paramètres de la commande suivante. Exécutez ensuite la commande :

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Pour restaurer la base de données vers une autre instance gérée, spécifiez également le nom du groupe de ressources et le nom de l’instance gérée cibles :  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Pour obtenir une explication détaillée des paramètres disponibles, consultez la [documentation CLI pour la restauration d’une base de données dans une instance gérée](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>restauration d’une base de données supprimée.

La restauration d’une base de données supprimée ne peut être effectuée qu’en utilisant PowerShell. La base de données peut être restaurée sur la même instance ou sur une autre instance.

Pour restaurer la base de données supprimée à l’aide de PowerShell, spécifiez vos valeurs pour les paramètres de la commande suivante. Exécutez ensuite la commande :

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Pour restaurer la base de données supprimée sur une autre instance, modifiez le nom du groupe de ressources et le nom de l’instance gérée. Vérifiez également que le paramètre d’emplacement correspond à l’emplacement du groupe de ressources et de l’instance gérée.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-an-existing-database"></a>Remplacer une base de données existante

Pour remplacer une base de données existante, vous devez :

1. Déposez la base de données existante que vous souhaitez remplacer.
2. Renommez la base de données restaurée dans le temps par le nom de la base de données qui a été déposée.

### <a name="drop-the-original-database"></a>Déposer la base de données d’origine

Vous pouvez déposer une base de données à l’aide du portail Azure, de PowerShell ou de Azure CLI.

Vous pouvez également déposer la base de données en vous connectant directement à l’instance gérée, en lançant SQL Server Management Studio (SSMS) et en exécutant la commande Transact-SQL (T-SQL) suivant :

```sql
DROP DATABASE WorldWideImporters;
```

Utilisez l’une des méthodes suivantes pour vous connecter à votre base de données d’instance gérée :

- [SSMS/Azure Data Studio via une machine virtuelle Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Point à site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Point de terminaison public](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Dans le portail Azure, sélectionnez la base de données à partir de l’instance gérée et sélectionnez **Supprimer**.

   ![Supprimer une base de données à l’aide du portail Azure](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez la commande PowerShell suivante pour supprimer une base de données existante d’une instance managée :

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Utilisez la commande Azure CLI suivante pour supprimer une base de données existante d’une instance managée :

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Modifier le nouveau nom de base de données pour qu’il corresponde au nom de la base de données d’origine

Connectez-vous directement à l’instance gérée et démarrez SQL Server Management Studio. Vous pouvez alors exécuter la requête Transact-SQL (T-SQL) suivante. La requête remplacera le nom de la base de données restaurée par celui de la base de données déposée que vous envisagez de remplacer.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Utilisez l’une des méthodes suivantes pour vous connecter à votre base de données d’instance gérée :

- [Machine virtuelle Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Point à site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Point de terminaison public](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [sauvegardes automatisées](sql-database-automated-backups.md).
