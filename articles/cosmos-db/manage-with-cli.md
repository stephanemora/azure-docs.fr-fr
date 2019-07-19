---
title: Gérer les ressources Azure Cosmos DB à l’aide de l'interface Azure CLI
description: Utilisez l'interface Azure CLI pour gérer votre compte, votre base de données et vos conteneurs Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 82d7cdf0c9519bb8a682445e666d46d6fd7bfbd7
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550939"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Gérer les ressources Azure Cosmos à l’aide d’Azure CLI

Le guide suivant décrit les commandes courantes permettant d’automatiser la gestion de vos comptes, bases de données et conteneurs Azure Cosmos DB à l’aide de l’interface Azure CLI. Des pages d’informations de référence pour toutes les commandes Azure Cosmos DB CLI sont disponibles dans les [Informations de référence sur Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Vous trouverez également des exemples dans [Exemples Azure CLI pour Azure Cosmos DB](cli-samples.md), notamment sur la création et la gestion de comptes, bases de données et conteneurs Cosmos DB pour les API MongoDB, Gremlin, Cassandra et Table.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Création d’un compte Azure Cosmos DB

Pour créer un compte Azure Cosmos DB avec prise en charge de l’API SQL et de la cohérence de session dans les régions USA Est et USA Ouest, exécutez la commande suivante :

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Le nom du compte Azure Cosmos doit être en minuscule.

## <a name="create-a-database"></a>Créer une base de données

Exécutez la commande suivante pour créer la base de données Cosmos DB :

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Créez un conteneur.

Pour créer un conteneur Cosmos DB avec un taux de RU/s de 400 et une clé de partition, exécutez la commande suivante :

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

Pour modifier le débit d’un conteneur Cosmos DB afin de passer à un taux de RU/s de 1 000, exécutez la commande suivante :

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

Pour obtenir les clés de votre compte Cosmos, exécutez la commande suivante :

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Répertorier les chaînes de connexion

Pour obtenir les chaînes de connexion de votre compte Cosmos, exécutez la commande suivante :

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Régénérer la clé de compte

Pour générer à nouveau une clé primaire pour votre compte Cosmos, exécutez la commande suivante :

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
- [Référence de ligne de Commande](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Autres exemples Azure CLI pour Azure Cosmos DB](cli-samples.md)
