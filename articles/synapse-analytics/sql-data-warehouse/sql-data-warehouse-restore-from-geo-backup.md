---
title: Restaurer un entrepôt de données géosauvegardé
description: Guide pratique de géorestauration d'un pool SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3c8d78c164cefbab03d9d3fa783c75ded9773d38
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075816"
---
# <a name="geo-restore-for-sql-pool"></a>Géorestauration pour un pool SQL

Dans cet article, vous allez apprendre à restaurer votre pool SQL à partir géosauvegarde via le portail Azure et PowerShell.

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Vérifiez votre capacité de DTU.** Chaque pool SQL est hébergé par un [serveur SQL logique](../../azure-sql/database/logical-servers.md) (par exemple, myserver.database.windows.net) qui dispose d'un quota de DTU par défaut. Vérifiez que le quota DTU restant sur le serveur SQL est suffisant pour la base de données en cours de restauration. Pour savoir comment calculer la capacité DTU nécessaire ou pour demander davantage de capacité DTU, consultez [Request a DTU quota change](sql-data-warehouse-get-started-create-support-ticket.md)(Demander une modification du quota DTU).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Restaurer à partir d’une région géographique Azure à l’aide de PowerShell

Pour effectuer une restauration à partir d’une sauvegarde géo-redondante, utilisez les cmdlets [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) et [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

> [!NOTE]
> Vous pouvez effectuer une géorestauration vers Gen2 ! Pour ce faire, spécifiez une valeur ServiceObjectiveName Gen2 (par exemple, DW1000**c**) comme paramètre facultatif.
>

1. Avant de commencer, veillez à [installer Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Ouvrez PowerShell.
3. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
4. Sélectionnez l’abonnement contenant l’entrepôt de données à restaurer.
5. Obtenez l’entrepôt de données à récupérer.
6. Créez la requête de récupération de l’entrepôt de données.
7. Vérifiez l’état de l’entrepôt de données affectée par la géo-restauration.
8. Pour configurer votre entrepôt de données une fois la restauration terminée, consultez [Configurer votre base de données après récupération]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
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

Suivez les étapes décrites ci-dessous pour restaurer un pool SQL à partir d’une géosauvegarde :

1. Connectez-vous à votre compte [Portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ Créer une ressource**.

   ![Nouveau DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Cliquez sur **Bases de données**, puis sur **Azure Synapse Analytics (anciennement SQL DW)** .

   ![Nouveau DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Renseignez les informations demandées dans l' onglet **Bases**, puis cliquez sur **Suivant : Paramètres supplémentaires**.

   ![Concepts de base](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Pour **utiliser le paramètre de données existant**, sélectionnez **Sauvegarde** et sélectionnez la sauvegarde appropriée dans les options de défilement vers le dessous. Cliquez sur **Revoir + créer**.

   ![sauvegarde](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Une fois l’entrepôt de données restauré, vérifiez que l'**état** est En ligne.

## <a name="next-steps"></a>Étapes suivantes

- [Restaurer un pool SQL existant](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurer un pool SQL supprimé](sql-data-warehouse-restore-deleted-dw.md)
