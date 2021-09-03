---
title: Résidence sur une seule région
description: Guide pratique pour la configuration de la résidence sur une seule région pour un pool SQL dédié (anciennement SQL DW) dans Azure Synapse Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/15/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9fd0860fba93f2aa45616707c791aef498fb06e
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110580700"
---
# <a name="configure-single-region-residency-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Configurer une résidence sur une seule région pour un pool SQL dédié (anciennement SQL DW) dans Azure Synapse Analytics

Dans cet article, vous allez apprendre à approvisionner votre base de données sur un stockage localement redondant pour vous assurer que vos données restent au sein de la limite géographique. Cet article s’applique uniquement aux pools SQL dédiés qui résident dans une région où la paire régionale Azure est située en dehors du pays. En suivant les étapes décrites dans cet article, vous obtiendrez la plupart des données et toutes les sauvegardes ne seront pas répliquées dans une [région associée](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Notez que certaines métadonnées, telles que le nom du serveur logique, le nom de la base de données logique, le nom du groupe de ressources sont toujours répliquées dans une région associée par défaut et ne peuvent pas être modifiées. 

Pour obtenir une résidence sur une seule région, approvisionnez votre pool SQL dédié (anciennement SQL DW) sur le stockage localement redondant en sélectionnant « Non » pour l’option géo-redondante lors de la configuration de votre pool SQL. Si vous avez déjà approvisionné votre pool SQL et qu’il réside sur un stockage géo-redondant, vous pouvez restaurer votre pool SQL et sélectionner « Non » pour le stockage géo-redondant pendant le processus de restauration. Les instructions pour les deux scénarios sont détaillées ci-dessous. 

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>Approvisionner un pool SQL dédié (anciennement SQL DW) sur le stockage localement redondant via le Portail Azure

Procédez comme suit pour configurer un pool SQL dédié (anciennement SQL DW) sur un stockage localement redondant :

1. Connectez-vous à votre compte [Portail Azure](https://portal.azure.com/).
1. Recherchez **pool SQL dédié (anciennement SQL DW)** .

   ![Nouveau DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. Cliquez sur Ajouter et renseignez les informations demandées dans l'onglet **Bases***.

1. Si votre serveur se trouve dans une région (par exemple, Asie Sud-Est) et que la paire régionale se trouve dans une autre zone géographique, vous avez la possibilité de refuser la géo-redondance. 

   ![Résidence des données](./media/sql-data-warehouse-single-region-residency/data-residency-1.png)

1. Sélectionnez **Suivant : mise en réseau** pour configurer un point de terminaison privé ou public. 

1. Sélectionnez **Suivant : paramètres supplémentaires** pour restaurer à partir d’une sauvegarde, d’un échantillon ou créez une base de données vide.  

1. Sélectionnez **Suivant : Balises** pour configurer des balises et catégoriser votre pool SQL. 

1. Sélectionnez **Suivant : Vérifier + créer**. Assurez-vous que la redondance de stockage correcte a été choisie pour garantir la résidence des données. 

    ![Créer un pool de régions unique](./media/sql-data-warehouse-single-region-residency/data-residency-2.png)

1. Sélectionnez **Create** (Créer).  

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>Approvisionner un pool SQL dédié (anciennement SQL DW) sur le stockage localement redondant via PowerShell
Pour créer un nouveau pool SQL dédié via PowerShell, utilisez la cmdlet PowerShell [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

1. Avant de commencer, veillez à [installer Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Ouvrez PowerShell.
3. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
4. Sélectionnez l’abonnement contenant la base de données à restaurer.
5. Créer une nouvelle base de données sur un stockage localement redondant.
1. Assurez-vous que la base de données a été créée avec succès sur le stockage localement redondant.  

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName -BackupStorageRedundancy 'Local'
Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName 
```

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>Restaurer un pool SQL dédié (anciennement SQL DW) sur le stockage localement redondant via le Portail Azure
1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez au pool SQL dédié à partir duquel effectuer la restauration.

1. En haut du panneau Vue d’ensemble, sélectionnez **Restaurer**.

1. Sélectionnez le point de restauration à partir duquel vous souhaitez effectuer la restauration. 

1. Pour **Géo-redondance** sélectionnez « Non ». 

   ![Restaurer via le portail](./media/sql-data-warehouse-single-region-residency/data-residency-3.png)

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>Restaurer un pool SQL dédié (anciennement SQL DW) sur le stockage localement redondant via PowerShell
Pour créer un nouveau pool SQL dédié via PowerShell, utilisez la cmdlet PowerShell [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

1. Avant de commencer, veillez à [installer Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Ouvrez PowerShell.
3. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
4. Sélectionnez l’abonnement contenant la base de données à restaurer.
5. Listez les points de restauration pour le pool SQL dédié (anciennement SQL DW).
1. Sélectionnez le point de restauration souhaité à l’aide de l’élément RestorePointCreationDate.
1. Restaurez le pool SQL dédié (anciennement SQL DW)sur le point de restauration souhaité à l’aide de la cmdlet PowerShell Restore-AzSqlDatabase en spécifiant BackupStorageRedundnacy en tant que « Local ». 

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID -BackupStorageRedundancy 'Local'

# Verify the status of restored database
$RestoredDatabase.status
```


## <a name="next-steps"></a>Étapes suivantes

- [Restaurer un pool SQL dédié (anciennement SQL DW) existant](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurer un pool SQL dédié (anciennement SQL DW) supprimé](sql-data-warehouse-restore-deleted-dw.md)
