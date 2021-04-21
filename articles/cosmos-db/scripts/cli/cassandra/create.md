---
title: Créer un espace de clés et une table Cassandra pour Azure Cosmos DB
description: Créer un espace de clés et une table Cassandra pour Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 7faa2ebf644b53ad29ead4f9e5766a583b5bf6a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786821"
---
# <a name="create-an-azure-cosmos-cassandra-api-account-keyspace-and-table-using-azure-cli"></a>Créer un compte, un espace de clés et une table d’API Cassandra Azure Cosmos avec Azure CLI
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.9.1 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/create.sh "Create an Azure Cosmos DB Cassandra API account, keyspace, and table.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Crée un compte Azure Cosmos DB. |
| [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az_cosmosdb_cassandra_keyspace_create) | Crée un espace de clés Cassandra Azure Cosmos. |
| [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az_cosmosdb_cassandra_table_create) | Crée une table Cassandra Azure Cosmos. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface CLI Azure Cosmos DB, consultez la [documentation sur l’interface CLI Azure Cosmos DB](/cli/azure/cosmosdb).

Vous trouverez tous les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
