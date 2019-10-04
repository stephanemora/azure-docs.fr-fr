---
title: Instance managée SQL Database - Limite de restauration dans le temps | Microsoft Docs
description: Comment restaurer une base de données dans une instance managée SQL à un point antérieur dans le temps.
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
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861996"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>Restaurer une base de données d’instance managée SQL à un point antérieur dans le temps

La limite de restauration dans le temps (PITR) vous permet de créer une base de données en tant que copie d’une autre base de données à un moment donné dans le passé. Cet article explique comment effectuer une restauration dans le temps d’une base de données dans une instance managée.

La limite de restauration dans le temps peut être utilisée dans les scénarios de récupération, comme les incidents provoqués par des erreurs, les données chargées de manière incorrecte, la suppression des données critiques et d’autres problèmes, ainsi qu’à des fins de test ou d’audit. Selon les paramètres de votre base de données, les fichiers de sauvegarde sont conservés pendant une période comprise entre 7 et 35 jours.

La limite de restauration dans le temps peut être utilisée pour :

- Restaurer une base de données à partir d’une base de données existante.
- Restaurer une base de données à partir d’une base de données supprimée.

En outre, avec une instance managée, la limite de restauration dans le temps peut être utilisée pour : 

- Restaurer une base de données dans la même instance managée.
- Restaurer une base de données vers une autre instance managée.


> [!NOTE]
> La limite de restauration dans le temps d’une instance managée entière n’est pas possible. Ce qui est possible et expliqué dans cet article, c’est une limite de restauration dans le temps d’une base de données hébergée sur une instance managée.


## <a name="limitations"></a>Limites

Lors de la restauration vers une autre instance managée, les deux instances doivent se trouver dans le même abonnement et la même région. Les restaurations inter-régions et inter-abonnements ne sont actuellement pas prises en charge.

> [!WARNING]
> Soyez attentif à la taille de stockage de votre instance managée : en fonction de la taille de la restauration des données, vous risquez de manquer de stockage d’instance. S’il n’y a pas assez d’espace pour les données restaurées, utilisez une autre approche.

Le tableau suivant présente les scénarios de récupération jusqu’à une date et heure pour une instance managée :

|           |Restaurer une BD existante| Restaurer une BD existante|Restaurer une BD supprimée| Restaurer une BD supprimée|
|:----------|:----------|:----------|:----------|:----------|
|Destination| Même MI|Autre MI |Même MI|Autre MI |
|Portail Azure| OUI|Non |Non|Non|
|D’Azure CLI|OUI |OUI |Non|Non|
|PowerShell| OUI|OUI |OUI|OUI|


## <a name="restore-existing-database"></a>Restaurer une base de données existante

Restaurez une base de données existante sur la même instance à l’aide du portail Azure, de PowerShell ou d’Azure CLI. Restaurez une base de données sur une autre instance à l’aide de PowerShell ou d’Azure CLI en spécifiant les propriétés du groupe de ressources et de l’instance managée cibles. Si ces paramètres ne sont pas spécifiés, la base de données sera restaurée par défaut sur l’instance existante. La restauration vers une autre instance n’est pas prise en charge actuellement par le portail Azure. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à votre instance managée et sélectionnez la base de données à restaurer. 
1. Sélectionnez **Restaurer** dans la page de la base de données. 

    ![Restaurer une base de données existante](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. Dans la page **Restaurer**, sélectionnez le point pour la date et l’heure dans l’historique auquel restaurer la base de données.
1. Sélectionnez **Confirmer** pour restaurer votre base de données. Cela démarre le processus de restauration, qui crée une nouvelle base de données et est rempli avec les données de la base de données d’origine au point voulu dans le temps. Pour plus d’informations sur le temps de récupération, consultez [temps de récupération](sql-database-recovery-using-backups.md#recovery-time). 

1. Recherchez une instance managée
1. Sélectionnez la base de données à restaurer
1. Dans l’écran de la base de données, cliquez sur l’action Restaurer
1. Dans l’écran Restaurer, sélectionnez la date et l’heure du point de l’historique vers lequel vous restaurez la base de données.
1. Après confirmation, le processus de restauration démarre et, selon la taille de la base de données, la nouvelle base de données est créée et remplie avec les données de la base de données d’origine au point voulu dans le temps. Pour la durée du processus de restauration, consultez l’article sur la récupération à l’aide de sauvegardes.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Si Azure PowerShell n’est pas encore installé, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Pour restaurer la base de données à l’aide de PowerShell, mettez à jour les paramètres avec vos valeurs et exécutez la commande suivante :

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

Pour restaurer la base de données vers une autre instance managée, définissez le nom du groupe de ressources cible et le nom de l’instance managée cible.  

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

Pour restaurer la base de données à l’aide d’Azure CLI, mettez à jour les paramètres avec vos valeurs et exécutez la commande suivante :


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


Pour restaurer la base de données vers une autre instance managée, définissez le nom du groupe de ressources cible et le nom de l’instance managée cible.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Pour obtenir une explication détaillée des paramètres disponibles, consultez [CLI instance managée](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore). 

---

## <a name="restore-a-deleted-database"></a>restauration d’une base de données supprimée. 
 
La restauration d’une base de données supprimée ne peut être effectuée qu’avec PowerShell. La base de données peut être restaurée sur la même instance ou sur une autre instance. 

Pour restaurer une base de données supprimée à l’aide de PowerShell, mettez à jour les paramètres avec vos valeurs et exécutez la commande suivante :

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

Pour restaurer la base de données supprimée sur une autre instance, modifiez le nom du groupe de ressources et le nom de l’instance managée.

Le paramètre d’emplacement doit correspondre à l’emplacement du groupe de ressources et de l’instance managée.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>Remplacer une base de données existante 
 
Pour remplacer une base de données existante, vous devez également effectuer les opérations suivantes :

1. SUPPRIMEZ la base de données existante que vous souhaitez remplacer.
1. Renommez la base de données restaurée jusqu’à une date et heure par le nom de la base de données qui a été supprimée. 


### <a name="drop-original-database"></a>SUPPRIMER la base de données d’origine 
 
La suppression de la base de données peut être effectuée à l’aide du portail Azure, de PowerShell ou d’Azure CLI. 

Vous pouvez également supprimer la base de données en vous connectant directement à l’instance managée, en lançant SQL Server Management Studio (SSMS) et en exécutant la commande Transact-SQL (T-SQL) ci-dessous.

```sql
DROP DATABASE WorldWideImporters;
```

Utilisez l’une des méthodes suivantes pour vous connecter à votre base de données d’instance managée : 

- [Machine virtuelle SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Point à site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Point de terminaison public](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Dans le portail Azure, sélectionnez la base de données à partir de l’instance managée et sélectionnez **Supprimer**.

   ![Restaurer une base de données existante](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

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


### <a name="alter-new-database-name-to-original"></a>MODIFIER le nom de la nouvelle base de données pour le remplacer par le nom d’origine

Connectez-vous directement à l’instance managée, lancez SQL Server Management Studio, puis exécutez la requête Transact-SQL (T-SQL) suivante pour remplacer le nom de la base de données restaurée par celui de la base de données supprimée que vous souhaitiez remplacer. 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


Utilisez l’une des méthodes suivantes pour vous connecter à votre base de données d’instance managée : 

- [Machine virtuelle SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Point à site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Point de terminaison public](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [conservation à long terme](sql-database-long-term-retention.md) et les [sauvegardes automatisées](sql-database-automated-backups.md). 
