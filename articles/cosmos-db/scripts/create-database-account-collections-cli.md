---
title: Script Azure CLI - Créer un compte, une base de données et une collection d’API SQL Azure Cosmos DB | Microsoft Docs
description: Exemple de script Azure CLI - Créer un compte, une base de données et une collection d’API SQL Azure Cosmos DB
services: cosmos-db
documentationcenter: cosmosdb
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/06/2017
ms.author: sngun
ms.openlocfilehash: fb4bd66d209d15c983d11c53648e3dfd9c7dcb4b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443903"
---
# <a name="azure-cosmos-db-create-an-sql-api-account-using-cli"></a>Azure Cosmos DB : Créer un compte d’API SQL avec l’interface de ligne de commande

Cet exemple de script CLI crée une collection, une base de données et un compte d’API SQL Azure Cosmos DB.  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-account-database/create-cosmosdb-account-database.sh?highlight=15-35 "Create an Azure Cosmos DB SQL API account, database, and collection")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Crée un compte Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI d’Azure Cosmos DB dans la [documentation d’Azure Cosmos DB](../cli-samples.md).
