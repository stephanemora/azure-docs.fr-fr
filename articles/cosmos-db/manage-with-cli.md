---
title: Gérer les ressources Azure Cosmos DB à l’aide de l'interface Azure CLI
description: Utilisez l'interface Azure CLI pour gérer votre compte, votre base de données et vos conteneurs Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 4/8/2019
ms.author: mjbrown
ms.openlocfilehash: 1d19e58b2d1381725de490b68d9e4d00a2ca4cb6
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495479"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Gérer les ressources Azure Cosmos à l’aide d’Azure CLI

Le guide suivant décrit les commandes courantes pour automatiser la gestion de vos comptes Azure Cosmos DB, les bases de données et les conteneurs à l’aide d’Azure CLI. Des pages d’informations de référence pour toutes les commandes Azure Cosmos DB CLI sont disponibles dans les [Informations de référence sur Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Vous trouverez également des exemples dans [Exemples Azure CLI pour Azure Cosmos DB](cli-samples.md), notamment sur la création et la gestion de comptes, bases de données et conteneurs Cosmos DB pour les API MongoDB, Gremlin, Cassandra et Table.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Création d’un compte Azure Cosmos DB

Pour créer un compte Azure Cosmos DB avec l’API SQL, la cohérence de Session dans les régions est et ouest des États-Unis, exécutez la commande suivante :

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations EastUS=0 WestUS=1 \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Le nom du compte Azure Cosmos doit être en minuscule.

## <a name="create-a-database"></a>Créer une base de données

Pour créer une base de données Cosmos DB, exécutez la commande suivante :

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Créez un conteneur.

Pour créer un conteneur Cosmos DB avec RU/s de 400 et une clé de partition, exécutez la commande suivante :

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>Modifier le débit d’un conteneur

Pour modifier le débit d’un conteneur Cosmos DB à 1000 RU/s, exécutez la commande suivante :

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Répertorier les clés de compte

Pour obtenir les clés pour votre compte Cosmos, exécutez la commande suivante :

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Répertorier les chaînes de connexion

Pour obtenir les chaînes de connexion pour votre compte Cosmos, exécutez la commande suivante :

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Régénérer la clé de compte

Pour régénérer une nouvelle clé primaire pour votre compte Cosmos, exécutez la commande suivante :

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez :

- [Installation de l’interface de ligne de commande Azure](/cli/azure/install-azure-cli)
- [Référence de l’interface CLI Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Exemples supplémentaires de la CLI Azure pour Azure Cosmos DB](cli-samples.md)
