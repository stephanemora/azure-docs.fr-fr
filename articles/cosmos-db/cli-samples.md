---
title: Exemples d’interface Azure CLI pour Azure Cosmos DB
description: Exemples de CLI Azure - Créer et gérer des bases de données, conteneurs, régions et pare-feu et comptes Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a6348024d4e84c27610f1294f916cca9a851b6b9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034197"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Exemples de CLI pour Azure Cosmos DB

Le tableau suivant comprend des liens vers des exemples de scripts Azure CLI pour Azure Cosmos DB. Des pages d’informations de référence pour toutes les commandes Azure Cosmos DB CLI sont disponibles dans les [Informations de référence sur Azure CLI](/cli/azure/cosmosdb).

| |  |
|---|---|
|**Créer un compte, une base de données et des conteneurs Azure Cosmos DB**||
| [Créer un compte Azure Cosmos DB à l’aide de l’API SQL](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Crée un compte Azure Cosmos DB unique, une base de données et un conteneur. |
| [Créer un compte Azure Cosmos DB à l’aide de l’API Cosmos DB pour MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un compte Azure Cosmos DB unique, une base de données et une collection. |
| [Créer un compte Azure Cosmos DB à l’aide de l’API Gremlin](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un compte Azure Cosmos DB unique, une base de données et un graphique. |
| [Créer un compte Azure Cosmos DB à l’aide de l’API Cassandra](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un compte Azure Cosmos DB unique et une base de données. |
| [Créer un compte Azure Cosmos DB à l’aide de l’API Table](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un compte Azure Cosmos DB unique, une base de données et une table. |
|**Mettre à l’échelle Azure Cosmos DB**||
| [Mettre à l’échelle le débit d’un conteneur](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Modifie le débit approvisionné sur un conteneur.|
| [Répliquer un compte de base de données Azure Cosmos DB dans plusieurs régions et configurer les priorités de basculement](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Réplique les données de compte globalement dans plusieurs régions avec une priorité de basculement spécifié.|
|**Sécuriser Azure Cosmos DB**||
| [Obtenir les clés de compte](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtient les clés primaire et secondaire d’écriture maître et les clés primaire et secondaire de lecture seule pour le compte.|
| [Obtenir la chaîne de connexion pour le compte Cosmos configuré à l’aide de l’API Azure Cosmos DB pour MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtient la chaîne de connexion pour connecter l’application MongoDB à votre compte Azure Cosmos DB.|
| [Régénération des clés de compte](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Régénérez les clés pour le compte.|
| [Créer un pare-feu](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Créez une stratégie de contrôle d’accès des IP entrantes pour limiter l’accès au compte à un ensemble de machines et/ou services cloud approuvés.|
|**Haute disponibilité et récupération d'urgence, sauvegarde et restauration**||
| [Configurer la stratégie de basculement](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Définit la priorité de basculement de chaque région dans laquelle le compte est répliqué.|
|**Connecter Azure Cosmos DB aux ressources**||
| [Connecter une application web à Azure Cosmos DB](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Créez et connectez une base de données Azure Cosmos DB et une application web Azure.|
|||
