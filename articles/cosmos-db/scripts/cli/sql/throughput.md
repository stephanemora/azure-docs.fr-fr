---
title: Scripts Azure CLI pour les opérations de débit (RU/s) sur des ressources de l’API Core (SQL) Azure Cosmos DB
description: Scripts Azure CLI pour les opérations de débit (RU/s) sur des ressources de l’API Core (SQL) Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: e48a167327a83483b44d88fc73a0254df6f85c55
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087347"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Opérations de débit (RU/s) avec Azure CLI sur une base de données ou un conteneur de l’API Core (SQL) Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cette rubrique vous impose d’exécuter Azure CLI version 2.12.1 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

Ce script crée une base de données d’API Core (SQL) avec un débit partagé et un conteneur d’API Core (SQL) avec un débit dédié, puis met à jour le débit des deux ressources. Le script migre ensuite du débit standard à un débit avec mise à l’échelle automatique, puis lit la valeur de ce débit après sa migration.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Crée un compte Azure Cosmos DB. |
| [az cosmosdb sql database create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Crée une base de données Core (SQL) Azure Cosmos. |
| [az cosmosdb sql container create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Crée un conteneur Core (SQL) Azure Cosmos. |
| [az cosmosdb sql database throughput update](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Met à jour le débit d’une base de données Core (SQL) Azure Cosmos. |
| [az cosmosdb sql container throughput update](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Met à jour le débit d’un conteneur Core (SQL) Azure Cosmos. |
| [az cosmosdb sql database throughput migrate](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-migrate) | Migre le débit d’une base de données Core (SQL) Azure Cosmos. |
| [az cosmosdb sql container throughput migrate](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-migrate) | Migre le débit d’un conteneur Core (SQL) Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface CLI Azure Cosmos DB, consultez la [documentation sur l’interface CLI Azure Cosmos DB](/cli/azure/cosmosdb).

Vous trouverez tous les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
