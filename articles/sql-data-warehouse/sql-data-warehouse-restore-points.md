---
title: Points de restauration définis par l’utilisateur
description: Comment créer un point de restauration Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bf092b5b6c6eb88b565a940de56d614426e34d8e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759599"
---
# <a name="user-defined-restore-points"></a>Points de restauration définis par l’utilisateur

Dans cet article, vous allez apprendre à créer un nouveau point de restauration défini par l’utilisateur pour Azure SQL Data Warehouse à l’aide de PowerShell et de Portail Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Créer des points de restauration définis par l’utilisateur via PowerShell

Pour créer un point de restauration défini par l’utilisateur, utilisez le cmdlet [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) de PowerShell.

1. Avant de commencer, veillez à [installer Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Ouvrez PowerShell.
3. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
4. Sélectionnez l’abonnement contenant la base de données à restaurer.
5. Créez un point de restauration pour une copie immédiate de votre entrepôt de données.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Consultez la liste de tous les points de restauration existants.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Créer des points de restauration définis par l’utilisateur via le portail Azure

Les points de restauration définis par l’utilisateur peuvent également être créés via le portail Azure.

1. Connectez-vous à votre compte [Portail Azure](https://portal.azure.com/).

2. Accédez à l’entrepôt SQL Data Warehouse pour lequel vous voulez créer un point de restauration.

3. Sélectionnez **Vue d’ensemble** dans le volet gauche, puis sélectionnez **+ nouveau point de restauration**. Si le nouveau bouton de point de restauration n’est pas activé, assurez-vous que l’entrepôt de données n’est pas suspendu.

    ![Nouveau point de restauration](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Spécifiez un nom pour votre point de restauration défini par l'utilisateur, puis cliquez sur **Appliquer**. Les points de restauration définis par l’utilisateur ont une période de rétention par défaut de sept jours.

    ![Nom du point de restauration](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Étapes suivantes

- [Restaurer un entrepôt de données existant](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurer un entrepôt de données supprimé](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurer à partir d’un entrepôt de données géo-sauvegardé](sql-data-warehouse-restore-from-geo-backup.md)

