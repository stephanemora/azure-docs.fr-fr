---
title: Scripts PowerShell pour les opérations de débit (RU/s) sur une base de données ou un conteneur de l’API SQL Azure Cosmos DB
description: Scripts PowerShell pour les opérations de débit (RU/s) sur une base de données ou un conteneur de l’API SQL Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: e55b93d3790efe01a796860bd3607f2fcc6ba5f6
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281984"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Opérations de débit (RU/s) avec PowerShell sur une base de données ou un conteneur de l’API Core (SQL) Azure Cosmos DB

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

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
| [Get-AzCosmosDBSqlDatabaseThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabasethroughput) | Obtient le débit provisionné sur une base de données de l’API Core (SQL) Azure Cosmos DB. |
| [Get-AzCosmosDBSqlContainerThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainerthroughput) | Obtient le débit provisionné sur un conteneur de l’API Core (SQL) Azure Cosmos DB. |
| [Update-AzCosmosDBSqlDatabaseThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Met à jour la valeur du débit d’une base de données de l’API Core (SQL) Azure Cosmos DB. |
| [Update-AzCosmosDBSqlContainerThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Met à jour la valeur du débit d’un conteneur de l’API Core (SQL) Azure Cosmos DB. |
| [Invoke-AzCosmosDBSqlDatabaseThroughputMigration](https://docs.microsoft.com/powershell/module/az.cosmosdb/invoke-azcosmosdbsqldatabasethroughputmigration) | Migre le débit d’une base de données de l’API Core (SQL) Azure Cosmos DB. |
| [Invoke-AzCosmosDBSqlContainerThroughputMigration](https://docs.microsoft.com/powershell/module/az.cosmosdb/invoke-azcosmosdbsqlcontainerthroughputmigration) | Migre le débit d’un conteneur de l’API Core (SQL) Azure Cosmos DB. |
|**Groupes de ressources Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).
