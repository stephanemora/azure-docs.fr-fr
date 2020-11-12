---
title: Scripts Azure CLI pour les opérations de débit (RU/s) sur des ressources de l’API Azure Cosmos DB pour MongoDB
description: Scripts Azure CLI pour les opérations de débit (RU/s) sur des ressources de l’API Azure Cosmos DB pour MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 5dcd3b0404b83755a6d8187c38adf1281aa79845
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102151"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db-api-for-mongodb"></a>Opérations de débit (RU/s) avec Azure CLI sur une base de données ou un graphe de l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cette rubrique vous impose d’exécuter Azure CLI version 2.12.1 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

Ce script crée une base de données MongoDB avec un débit partagé et une collection avec un débit dédié, puis met à jour le débit pour les deux ressources. Le script migre ensuite du débit standard à un débit avec mise à l’échelle automatique, puis lit la valeur de ce débit après sa migration.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Throughput operations for Azure Cosmos DB API for MongoDB.")]

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
| [az cosmosdb mongodb database create](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Crée une base de données d’API MongoDB Azure Cosmos. |
| [az cosmosdb mongodb collection create](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Crée une collection d’API MongoDB Azure Cosmos. |
| [az cosmosdb mongodb database throughput update](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Mettez à jour les unités de requête/s pour une base de données d’API MongoDB Azure Cosmos. |
| [az cosmosdb mongodb collection throughput update](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Mettez à jour les unités de requête/s pour une collection d’API MongoDB Azure Cosmos. |
| [az cosmosdb mongodb database throughput migrate](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-migrate) | Migrez le débit d’une base de données. |
| [az cosmosdb mongodb collection throughput migrate](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-migrate) | Migrez le débit d’une collection. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface CLI Azure Cosmos DB, consultez la [documentation sur l’interface CLI Azure Cosmos DB](/cli/azure/cosmosdb).

Vous trouverez tous les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
