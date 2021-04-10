---
title: 'Démarrage rapide : Suspendre et reprendre le calcul dans un pool SQL dédié (anciennement SQL DW) avec Azure PowerShell'
description: Vous pouvez utiliser Azure PowerShell pour suspendre et reprendre le pool SQL dédié (anciennement SQL DW). ressources de calcul.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 03/20/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-azurepowershell
ms.openlocfilehash: 74c30a843ef5edd54c7cd19f3fd49acfe782f488
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602177"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Démarrage rapide : Suspendre et reprendre le calcul dans un pool SQL dédié (anciennement SQL DW) avec Azure PowerShell

Vous pouvez utiliser Azure PowerShell pour suspendre et reprendre les ressources de calcul du pool SQL dédié (anciennement SQL DW).
Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ce guide de démarrage rapide part du principe que vous disposez déjà d’un pool SQL dédié (anciennement SQL DW) que vous pouvez mettre en pause et reprendre. Si vous devez en créer un, vous pouvez utiliser [Créer et connecter – Portail](create-data-warehouse-portal.md) pour créer un pool SQL dédié (anciennement SQL DW) nommé **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) et suivez les instructions indiquées à l’écran.

```powershell
Connect-AzAccount
```

Pour voir l’abonnement que vous utilisez, exécutez [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Si vous devez utiliser un autre abonnement que celui par défaut, exécutez [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-dedicated-sql-pool-formerly-sql-dw-information"></a>Rechercher des informations sur les pools SQL dédiés (anciennement SQL DW)

Trouvez le nom de la base de données, le nom du serveur et le groupe de ressources pour le pool SQL dédié (anciennement SQL DW) que vous prévoyez de mettre en pause et reprendre.

Suivez ces étapes pour trouver les informations d’emplacement de votre pool SQL dédié (anciennement SQL DW) :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Cliquez sur **Azure Synapse Analytics (anciennement SQL DW)** dans la page de gauche du portail Azure.
1. Sélectionnez **mySampleDataWarehouse** dans la page **Azure Synapse Analytics (anciennement SQL DW)** . Le pool SQL s’ouvre.

    ![Nom du serveur et groupe de ressources](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Notez le nom du pool SQL dédié (anciennement SQL DW), à savoir le nom de la base de données. Notez également le nom du serveur et le groupe de ressources.
1. Utilisez uniquement la première partie du nom du serveur dans les applets de commande PowerShell. Dans l’image précédente, le nom complet du serveur est sqlpoolservername.database.windows.net. Nous utilisons **sqlpoolservername** comme nom de serveur dans l’applet de commande PowerShell.

## <a name="pause-compute"></a>Suspension du calcul

Pour réduire les coûts, vous pouvez interrompre et reprendre des ressources de calcul à la demande. Par exemple, si vous n’utilisez pas la base de données pendant la nuit et les week-ends, vous pouvez la suspendre à ces moments et la reprendre pendant la journée.

>[!NOTE]
>Aucune ressource de calcul ne vous sera facturée tant que la base de données restera suspendue. Le stockage, en revanche, continue à occasionner des frais.

Pour suspendre une base de données, utilisez la cmdlet [Suspend-AzureRmSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). L’exemple suivant suspend un pool SQL nommé **mySampleDataWarehouse** hébergé sur un serveur nommé **sqlpoolservername**. Le serveur est un groupe de ressources Azure nommé **myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

L’exemple suivant récupère la base de données dans l’objet $database. Il canalise ensuite l’objet vers [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Les résultats sont stockés dans l'objet resultDatabase. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Reprise du calcul

Pour démarrer une base de données, utilisez la cmdlet [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). L’exemple suivant démarre un entrepôt de données nommé **mySampleDataWarehouse** hébergé sur un serveur nommé **sqlpoolservername**. Le serveur est un groupe de ressources Azure nommé **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

L’exemple suivant récupère la base de données dans l’objet $database. Il canalise ensuite l’objet vers [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) et stocke les résultats dans $resultDatabase. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Vérifier l’état de fonctionnement du pool SQL

Pour vérifier l’état de votre pool SQL dédié (anciennement SQL DW), utilisez la cmdlet [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Les unités Data Warehouse Unit et les données stockées dans votre pool SQL dédié (anciennement SQL DW) vous sont facturées. Ces ressources de calcul et de stockage sont facturées séparément.

- Si vous souhaitez conserver les données dans le stockage, suspendez le calcul.
- Si vous voulez éviter des frais à venir, vous pouvez supprimer le pool SQL.

Suivez ces étapes pour nettoyer les ressources selon vos besoins.

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis cliquez sur votre pool SQL.

    ![Nettoyer les ressources](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pour suspendre le calcul, cliquez sur le bouton **Suspendre**. Quand le pool SQL est suspendu, un bouton **Démarrer** est visible.  Pour reprendre le calcul, cliquez sur **Démarrer**.

3. Pour supprimer le pool SQL afin de ne pas être facturé pour le calcul ou le stockage, cliquez sur **Supprimer**.

4. Pour supprimer le serveur SQL que vous avez créé, cliquez sur **sqlpoolservername.database.windows.net**, puis sur **Supprimer**.  N’oubliez pas que la suppression du serveur supprime également toutes les bases de données qui lui sont attribuées.

5. Pour supprimer le groupe de ressources, cliquez sur **myResourceGroup**, puis sur **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le pool SQL, lisez l’article [Charger des données dans un pool SQL dédié (anciennement SQL DW)](./load-data-from-azure-blob-storage-using-copy.md). Pour plus d’informations sur la gestion des capacités de calcul, consultez l’article [Vue d’ensemble de la gestion du calcul](sql-data-warehouse-manage-compute-overview.md).