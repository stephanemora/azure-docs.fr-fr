---
title: Scripts PowerShell pour les opérations de débit (RU/s) sur une base de données ou un conteneur de l’API SQL Azure Cosmos DB
description: Scripts PowerShell pour les opérations de débit (RU/s) sur une base de données ou un conteneur de l’API SQL Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 73fdfa06fab42a5687e3aa354e3ba92937b92605
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684290"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Opérations de débit (RU/s) avec PowerShell sur une base de données ou un conteneur de l’API Core (SQL) Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Cet exemple nécessite Azure PowerShell Az 5.4.0 ou ultérieur. Exécutez `Get-Module -ListAvailable Az` pour voir quelles versions sont installées.
Si vous devez l’installer, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

Exécutez [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pour vous connecter à Azure.

## <a name="get-throughput"></a>GetThroughput

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-get.ps1 "Get throughput (RU/s) for Azure Cosmos DB Core (SQL) API database or container")]

## <a name="update-throughput"></a>Mettre à jour le débit

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-update.ps1 "Update throughput (RU/s) for an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="migrate-throughput"></a>Migrer le débit

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-migrate.ps1 "Migrate between standard and autoscale throughput on an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBSqlDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabasethroughput) | Obtient le débit provisionné sur une base de données de l’API Core (SQL) Azure Cosmos DB. |
| [Get-AzCosmosDBSqlContainerThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainerthroughput) | Obtient le débit provisionné sur un conteneur de l’API Core (SQL) Azure Cosmos DB. |
| [Update-AzCosmosDBSqlDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Met à jour la valeur du débit d’une base de données de l’API Core (SQL) Azure Cosmos DB. |
| [Update-AzCosmosDBSqlContainerThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Met à jour la valeur du débit d’un conteneur de l’API Core (SQL) Azure Cosmos DB. |
| [Invoke-AzCosmosDBSqlDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqldatabasethroughputmigration) | Migre le débit d’une base de données de l’API Core (SQL) Azure Cosmos DB. |
| [Invoke-AzCosmosDBSqlContainerThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqlcontainerthroughputmigration) | Migre le débit d’un conteneur de l’API Core (SQL) Azure Cosmos DB. |
|**Groupes de ressources Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).