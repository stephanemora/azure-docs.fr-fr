---
title: Scripts Azure CLI pour les opérations de débit (RU/s) sur des ressources de l’API Table Azure Cosmos DB
description: Scripts Azure CLI pour les opérations de débit (RU/s) sur des ressources de l’API Table Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 6bf2e638d462769f86d78f24a525cc913042155d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075957"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-table-for-azure-cosmos-db-table-api"></a>Opérations de débit (RU/s) avec Azure CLI sur une table de l’API Table Azure Cosmos DB
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cette rubrique vous impose d’exécuter Azure CLI version 2.12.1 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

Ce script crée une table d’API Table, puis met à jour le débit de la table. Le script migre ensuite du débit standard à un débit avec mise à l’échelle automatique, puis il lit la valeur de ce débit après sa migration.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Throughput operations for Table API.")]

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
| [az cosmosdb table create](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Crée une table d’API Table Azure Cosmos. |
| [az cosmosdb table throughput update](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-update) | Met à jour le débit d’une table de l’API Table Azure Cosmos. |
| [az cosmosdb table throughput migrate](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-migrate) | Migre le débit d’une table de l’API Table Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface CLI Azure Cosmos DB, consultez la [documentation sur l’interface CLI Azure Cosmos DB](/cli/azure/cosmosdb).

Vous trouverez tous les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
