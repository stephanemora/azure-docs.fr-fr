---
title: Créer un verrou de ressource pour une base de données et une collection pour l’API MongoDB pour Azure Cosmos DB
description: Créer un verrou de ressource pour une base de données et une collection pour l’API MongoDB pour Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: 3a8e1f31419d4ab14283418fbeb6391ae7167e5b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87049006"
---
# <a name="create-a-resource-lock-for-azure-cosmos-dbs-api-for-mongodb-using-azure-cli"></a>Créer un verrou de ressource pour l’API Azure Cosmos DB pour MongoDB à l’aide d’Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cette rubrique vous demande d’exécuter Azure CLI version 2.6.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Les verrous de ressources ne fonctionnent pas pour les modifications apportées par les utilisateurs qui se connectent à l’aide d’un SDK MongoDB, de Mongoshell, d’outils ou du portail Azure, sauf si le compte Cosmos DB est d’abord verrouillé avec la propriété `disableKeyBasedMetadataWriteAccess` activée. Pour en savoir plus sur l’activation de cette propriété, consultez [Prévention des modifications à partir des SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/lock.sh "Create a resource lock for an Azure Cosmos DB MongoDB API database and collection.")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | Crée un verrou. |
| [az lock list](/cli/azure/lock#az-lock-list) | Liste les informations sur les verrous. |
| [az lock show](/cli/azure/lock#az-lock-show) | Affiche les propriétés d’un verrou. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | Supprime un verrou. |

## <a name="next-steps"></a>Étapes suivantes

-[Verrouiller des ressources pour empêcher des modifications inattendues](../../../../azure-resource-manager/management/lock-resources.md)

-[Documentation sur l’interface CLI Azure Cosmos DB](/cli/azure/cosmosdb).

-[Dépôt GitHub de l’interface CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
