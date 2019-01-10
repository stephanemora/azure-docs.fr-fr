---
title: Gérer les ressources Azure Cosmos DB à l’aide de l'interface Azure CLI
description: Utilisez l'interface Azure CLI pour gérer votre compte, votre base de données et vos conteneurs Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: d45f5b5c5945796e30c86b2e3ef48d6b8e693b99
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038974"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-cli"></a>Gérer les ressources Azure Cosmos DB à l’aide de l'interface Azure CLI

Le guide suivant décrit les commandes permettant d’automatiser la gestion de vos comptes, bases de données et conteneurs Azure Cosmos DB à l’aide de l'interface Azure CLI. Il présente également les commandes à utiliser pour la mise à l’échelle du débit des conteneurs. Des pages d’informations de référence pour toutes les commandes Azure Cosmos DB CLI sont disponibles dans les [Informations de référence sur Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Vous trouverez également des exemples dans [Exemples Azure CLI pour Azure Cosmos DB](cli-samples.md), notamment sur la création et la gestion de comptes, bases de données et conteneurs Cosmos DB pour les API MongoDB, Gremlin, Cassandra et Table.

Cet exemple de script CLI crée un conteneur, une base de données et un compte d’API SQL Azure Cosmos DB.  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Création d’un compte Azure Cosmos DB

Pour créer un compte Azure Cosmos DB avec prise en charge de l'API SQL, de la cohérence de session et de la fonction multimaître dans les régions USA Est et USA Ouest, ouvrez Azure CLI ou Cloud Shell et exécutez la commande suivante :

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>Créer une base de données

Pour créer une base de données Cosmos DB, ouvrez Azure CLI ou Cloud Shell et exécutez la commande suivante :

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>Créez un conteneur.

Pour créer un conteneur Cosmos DB avec une RU/s de 1 000 et une clé de partition, ouvrez Azure CLI ou Cloud Shell et exécutez la commande suivante :

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>Modifier le débit d’un conteneur

Pour modifier le débit d'un conteneur Cosmos DB afin de passer à une RU/s de 400, ouvrez Azure CLI ou Cloud Shell et exécutez la commande suivante :

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>Répertorier les clés de compte

Lorsque vous créez un compte Azure Cosmos DB, le service génère deux clés d’accès maître qui peuvent être utilisées pour l’authentification lors de l’accès au compte Azure Cosmos DB. En fournissant deux clés d’accès, Azure Cosmos DB vous permet de régénérer les clés sans interruption de votre compte Azure Cosmos DB. Des clés en lecture seule pour l’authentification des opérations en lecture seule sont également disponibles. Il existe deux clés en lecture et écriture (primaire et secondaire) et deux clés en lecture seule (primaire et secondaire). Pour obtenir les clés de votre compte, exécutez la commande suivante :

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>Répertorier les chaînes de connexion

La chaîne de connexion permettant de connecter votre application au compte Cosmos DB peut être récupérée à l'aide de la commande suivante.

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>Régénérer la clé de compte

Vous devez modifier périodiquement les clés d'accès à votre compte Azure Cosmos DB pour garantir la sécurité des connexions. Deux clés d’accès vous sont affectées afin de vous permettre de conserver des connexions au compte Azure Cosmos DB à l’aide d’une clé d’accès lorsque vous régénérez l’autre clé.

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez :

- [Installation de l’interface de ligne de commande Azure](/cli/azure/install-azure-cli)
- [Référence de ligne de Commande](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Autres exemples Azure CLI pour Azure Cosmos DB](cli-samples.md)
