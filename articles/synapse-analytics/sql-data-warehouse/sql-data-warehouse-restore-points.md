---
title: Points de restauration définis par l’utilisateur
description: Créer un point de restauration pour un pool SQL
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5c9b7eb1b03b6b6e3721c13f9ebf7da25dd2e376
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745063"
---
# <a name="user-defined-restore-points"></a>Points de restauration définis par l’utilisateur

Dans cet article, vous allez apprendre à créer un point de restauration défini par l'utilisateur pour un pool SQL dans Azure Synapse Analytics à l'aide de PowerShell et du portail Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Créer des points de restauration définis par l’utilisateur via PowerShell

Pour créer un point de restauration défini par l’utilisateur, utilisez le cmdlet [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) de PowerShell.

1. Avant de commencer, veillez à [installer Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
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

2. Accédez au pool SQL pour lequel vous souhaitez créer un point de restauration.

3. Sélectionnez **Vue d’ensemble** dans le volet gauche, puis sélectionnez **+ nouveau point de restauration**. Si le bouton Nouveau point de restauration n'est pas activé, assurez-vous que le pool SQL n'est pas suspendu.

    ![Nouveau point de restauration](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Spécifiez un nom pour votre point de restauration défini par l'utilisateur, puis cliquez sur **Appliquer**. Les points de restauration définis par l’utilisateur ont une période de rétention par défaut de sept jours.

    ![Nom du point de restauration](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Étapes suivantes

- [Restaurer un pool SQL existant](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurer un pool SQL supprimé](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurer à partir d’un pool SQL de géosauvegarde](sql-data-warehouse-restore-from-geo-backup.md)

