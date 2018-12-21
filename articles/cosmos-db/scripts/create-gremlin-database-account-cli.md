---
title: Script Azure CLI - Créer un compte, une base de données et un graphe d’API Gremlin Azure Cosmos DB
description: Exemple de script Azure CLI - Créer un compte, une base de données et un graphique d’API Gremlin Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: sample
ms.date: 10/26/2018
ms.openlocfilehash: 0e870b00cefc235a99c084c5a405b69c07187907
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956667"
---
# <a name="azure-cosmos-db-create-a-gremlin-api-account-using-azure-cli"></a>Azure Cosmos DB : Créer un compte d’API Gremlin avec Azure CLI

Cet exemple de script CLI crée un graphique, une base de données et un compte d’API Gremlin Azure Cosmos DB.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-gremlin-account/create-cosmosdb-gremlin-account.sh "Create an Azure Cosmos DB Gremlin API account, database, and graph")]

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
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | Crée une base de données Azure Cosmos DB. |
| [az cosmosdb collection create](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-create) | Crée un graphique Azure Cosmos DB pour Gremlin. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI d’Azure Cosmos DB dans la [documentation d’Azure Cosmos DB](../cli-samples.md).
