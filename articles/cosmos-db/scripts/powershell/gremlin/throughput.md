---
title: Scripts PowerShell pour les opérations de débit (RU/s) de l’API Gremlin Azure Cosmos DB
description: Scripts PowerShell pour les opérations de débit (RU/s) de l’API Gremlin
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 50f3f05bd06101caa096b8126225c7b49ef1f3fa
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675486"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Opérations de débit (RU/s) avec PowerShell sur une base de données ou un graphe de l’API Gremlin Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Cet exemple nécessite Azure PowerShell Az 5.4.0 ou ultérieur. Exécutez `Get-Module -ListAvailable Az` pour voir quelles versions sont installées.
Si vous devez l’installer, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

Exécutez [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pour vous connecter à Azure.

## <a name="get-throughput"></a>GetThroughput

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-get.ps1 "Get throughput on a database or graph for Gremlin API")]

## <a name="update-throughput"></a>Mettre à jour le débit

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-update.ps1 "Update throughput on a database or graph for Gremlin API")]

## <a name="migrate-throughput"></a>Migrer le débit

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a database or graph for Gremlin API")]

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
| [Get-AzCosmosDBGremlinDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbgremlindatabasethroughput) | Obtient la valeur de débit de l’API Database Gremlin. |
| [Get-AzCosmosDBGremlinGraphThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbgremlingraphthroughput) | Obtient la valeur de débit de l’API Graph Gremlin. |
| [Update-AzCosmosDBGremlinDatabaseThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbgremlindatabasethroughput) | Met à jour la valeur de débit de l’API Database Gremlin. |
| [Update-AzCosmosDBGremlinGraphThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbgremlingraphthroughput) | Met à jour la valeur de débit de l’API Graph Gremlin. |
| [Invoke-AzCosmosDBGremlinDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbgremlindatabasethroughputmigration) | Migre le débit d’une base de données d’API Gremlin. |
| [Invoke-AzCosmosDBGremlinGraphThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbgremlingraphthroughputmigration) | Migre le débit d’un graphe d’API Gremlin. |
|**Groupes de ressources Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/).