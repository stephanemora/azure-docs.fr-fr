---
title: Exemples Azure CLI pour l’API SQL (Core) Azure Cosmos DB
description: Exemples Azure CLI pour l’API SQL (Core) Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 39550c3da7a5ed129e26dec3d33f19c65d76cdfa
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560533"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Exemples Azure CLI pour l’API (SQL) Core Azure Cosmos DB

Le tableau suivant comporte des liens vers des exemples de scripts Azure CLI pour l’API (SQL) Core Azure Cosmos DB. Des pages d’informations de référence pour toutes les commandes Azure Cosmos DB CLI sont disponibles dans les [Informations de référence sur Azure CLI](/cli/azure/cosmosdb). Pour obtenir des exemples Azure CLI pour les autres API Azure Cosmos DB, consultez [API Cassandra](cli-samples-cassandra.md), [API MongoDB](cli-samples-mongodb.md), [API Gremlin](cli-samples-gremlin.md) et [API Table](cli-samples-table.md). Vous trouverez tous les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

|Tâche | Description |
|---|---|
| [Créer un compte, une base de données et un conteneur Azure Cosmos](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte, une base de données et un conteneur Azure Cosmos DB pour l’API SQL (Core). |
| [Changer le débit](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Mettez à jour les unités de requête/s pour une base de données et un conteneur.|
| [Ajouter des régions de basculement](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Ajoutez une région, changez la priorité de basculement, déclenchez un basculement manuel.|
| [Clés de compte et chaînes de connexion](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Listez les clés de compte, les clés en lecture seule, regénérez les clés et listez les chaînes de connexion.|
| [Sécuriser avec le pare-feu IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte Cosmos avec le pare-feu IP configuré.|
| [Sécuriser un nouveau compte avec des points de terminaison de service](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte Cosmos et sécurisez-le avec des points de terminaison de service.|
| [Sécuriser un compte existant avec des points de terminaison de service](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Mettez à jour un compte Cosmos pour le sécuriser avec des points de terminaison de service quand le sous-réseau est finalement configuré.|
| [Verrouiller des ressources contre la suppression](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources.|
|||
