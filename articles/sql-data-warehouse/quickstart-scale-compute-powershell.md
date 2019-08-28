---
title: 'Démarrage rapide : Effectuer un scale-out du calcul dans Azure SQL Data Warehouse - PowerShell | Microsoft Docs'
description: Mettez à l’échelle le calcul dans Azure SQL Data Warehouse avec PowerShell. Montez en charge le calcul pour améliorer les performances, ou réduisez-le pour diminuer les coûts.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 193aec8de2a6ee34da666a013dcb0d49d85c08b0
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574843"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Démarrage rapide : Mettre à l’échelle le calcul dans Azure SQL Data Warehouse avec PowerShell

Mettez à l’échelle le calcul dans Azure SQL Data Warehouse avec PowerShell. [Augmentez le calcul](sql-data-warehouse-manage-compute-overview.md) pour améliorer les performances, ou réduisez-le pour diminuer les coûts.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ce démarrage rapide part du principe que vous disposez déjà d’un entrepôt de données SQL que vous pouvez mettre à l’échelle. Si vous devez en créer un, utilisez la section [Créer et connecter - Portail](create-data-warehouse-portal.md) pour créer un entrepôt de données nommé **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) et suivez les instructions indiquées à l’écran.

```powershell
Connect-AzAccount
```

Pour voir l’abonnement que vous utilisez, exécutez [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Si vous devez utiliser un autre abonnement que celui par défaut, exécutez [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Rechercher des informations sur l’entrepôt de données

Recherchez le nom de la base de données, le nom du serveur et le groupe de ressources de l’entrepôt de données que vous souhaitez suspendre et reprendre.

Suivez ces étapes pour rechercher des informations sur l’emplacement de votre entrepôt de données.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Cliquez sur **Entrepôts de données SQL Data Warehouse** sur la page de gauche du portail Azure.
3. Sélectionnez **mySampleDataWarehouse** sur la page **Entrepôts de données SQL Data Warehouse**. L’entrepôt de données s’ouvre.

    ![Nom du serveur et groupe de ressources](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Notez le nom de l’entrepôt de données qui sera utilisé comme nom de base de données. N’oubliez pas qu’un entrepôt de données est un type de base de données. Notez également le nom du serveur et le groupe de ressources. Vous les utiliserez dans les commandes de suspension et de reprise.
5. Si votre serveur est foo.database.windows.net, utilisez uniquement la première partie comme nom du serveur dans les applets de commande PowerShell. Dans l’image précédente, le nom complet est newserver-20171113.database.windows.net. Nous utilisons **newserver-20180430** comme nom de serveur dans la cmdlet PowerShell.

## <a name="scale-compute"></a>Mise à l’échelle des ressources de calcul

Dans SQL Data Warehouse, vous pouvez augmenter ou réduire les ressources de calcul en ajustant les unités Data Warehouse Unit. Le guide [Créer et connecter – Portail](create-data-warehouse-portal.md) a permis de créer **mySampleDataWarehouse** et de l’initialiser avec 400 DWU. Les étapes suivantes ajustent les DWU de **mySampleDataWarehouse**.

Pour modifier les unités de l’entrepôt de données, utilisez la cmdlet PowerShell [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase). L’exemple suivant définit les unités de l’entrepôt de données sur DW300c pour la base de données **mySampleDataWarehouse** qui est hébergée dans le groupe de ressources **myResourceGroup** sur le serveur **mynewserver-20180430**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Vérifiez l’état de l’entrepôt de données

Pour afficher l’état actuel de l’entrepôt de données, utilisez la cmdlet PowerShell [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase). Il obtient l’état de la base de données **mySampleDataWarehouse** dans le groupe de ressources **myResourceGroup** et le serveur **mynewserver-20180430.database.windows.net**.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
$database
```

Ce qui donnera quelque chose comme ceci :

```powershell
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Vous pouvez voir l’**État** de la base de données dans la sortie. Dans ce cas, vous pouvez voir que la base de données est en ligne.  Lorsque vous exécutez cette commande, vous devriez recevoir une valeur d’état En ligne, Suspension, Reprise, Mise à l’échelle ou Suspendu.

Pour afficher l’état proprement dit, utilisez la commande suivante :

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Étapes suivantes
Vous savez maintenant mettre à l’échelle le calcul pour votre entrepôt de données. Pour en savoir plus sur Azure SQL Data Warehouse, continuez avec le didacticiel de chargement des données.

> [!div class="nextstepaction"]
>[Charger des données dans un entrepôt SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
