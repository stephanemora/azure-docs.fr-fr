---
title: Exemples d’interface Azure CLI pour Azure Cosmos DB
description: Exemples d’interface Azure CLI pour Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 954215f04525e850151fdad93af6e7272b41b3df
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498461"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Exemples de CLI pour Azure Cosmos DB

Le tableau suivant comprend des liens vers des exemples de scripts Azure CLI pour Azure Cosmos DB. Utilisez les liens sur la droite pour accéder aux exemples spécifiques aux API. Les exemples communs sont les mêmes pour toutes les API. Des pages d’informations de référence pour toutes les commandes Azure Cosmos DB CLI sont disponibles dans les [Informations de référence sur Azure CLI](/cli/azure/cosmosdb). Vous trouverez également les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Ces exemples nécessitent Azure CLI version 2.9.1 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="common-samples"></a>Exemples communs

Ces exemples s’appliquent à toutes les API Azure Cosmos DB.

|Tâche | Description |
|---|---|
| [Ajouter des régions de basculement](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Ajoutez une région, changez la priorité de basculement, déclenchez un basculement manuel.|
| [Clés de compte et chaînes de connexion](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Listez les clés de compte, les clés en lecture seule, regénérez les clés et listez les chaînes de connexion.|
| [Sécuriser avec le pare-feu IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte Cosmos avec le pare-feu IP configuré.|
| [Sécuriser un nouveau compte avec des points de terminaison de service](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte Cosmos et sécurisez-le avec des points de terminaison de service.|
| [Sécuriser un compte existant avec des points de terminaison de service](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Mettez à jour un compte Cosmos pour le sécuriser avec des points de terminaison de service quand le sous-réseau est finalement configuré.|
|||

## <a name="core-sql-api-samples"></a>Exemples d’API Core (SQL)

|Tâche | Description |
|---|---|
| [Créer un compte, une base de données et un conteneur Azure Cosmos](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Crée un compte, une base de données et un conteneur Azure Cosmos DB pour l’API Core (SQL). |
| [Créer un compte, une base de données et un conteneur Azure Cosmos avec mise à l’échelle automatique](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Crée un compte, une base de données et un conteneur Azure Cosmos DB avec mise à l’échelle automatique pour l’API Core (SQL). |
| [Changer le débit](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Mettez à jour les unités de requête/s pour une base de données et un conteneur.|
| [Verrouiller des ressources contre la suppression](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources.|
|||

## <a name="mongodb-api-samples"></a>Exemples d’API MongoDB

|Tâche | Description |
|---|---|
| [Créer un compte, une base de données et une collection Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte, une base de données et une collection Azure Cosmos DB pour l’API MongoDB. |
| [Créer un compte et une base de données avec mise à l’échelle automatique et deux collections Azure Cosmos avec débit partagé](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Crée un compte et une base de données avec mise à l’échelle automatique et deux collections Azure Cosmos DB avec débit partagé pour l’API MongoDB. |
| [Changer le débit](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Mettez à jour les unités de requête/s pour une base de données et une collection.|
| [Verrouiller des ressources contre la suppression](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources.|
|||

## <a name="cassandra-api-samples"></a>Exemples d’API Cassandra

|Tâche | Description |
|---|---|
| [Créer un compte, un espace de clés et une table Azure Cosmos](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte, un espace de clés et une table Azure Cosmos pour l’API Cassandra. |
| [Créer un compte, un espace de clés et une table Azure Cosmos avec mise à l’échelle automatique](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Crée un compte, un espace de clés et une table Azure Cosmos DB avec mise à l’échelle automatique pour l’API Cassandra. |
| [Changer le débit](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Mettez à jour les unités de requête/s dans un espace de clés et une table.|
| [Verrouiller des ressources contre la suppression](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources.|
|||

## <a name="gremlin-api-samples"></a>Exemples d’API Gremlin

|Tâche | Description |
|---|---|
| [Créer un compte, une base de données et un graphe Azure Cosmos](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte, une base de données et un graphe Azure Cosmos DB pour l’API Gremlin. |
| [Créer un compte, une base de données et un graphe Azure Cosmos avec mise à l’échelle automatique](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Crée un compte, une base de données et un graphe Azure Cosmos DB avec mise à l’échelle automatique pour l’API Gremlin. |
| [Changer le débit](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Mettez à jour les unités de requête/s pour une base de données et un graphe.|
| [Verrouiller des ressources contre la suppression](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources.|
|||

## <a name="table-api-samples"></a>Exemples d’API Table

|Tâche | Description |
|---|---|
| [Créer un compte et une table Azure Cosmos](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte et une table Azure Cosmos DB pour l’API Table. |
| [Créer un compte et une table Azure Cosmos avec mise à l’échelle automatique](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Crée un compte et une table Azure Cosmos DB avec mise à l’échelle automatique pour l’API Table. |
| [Changer le débit](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Mettez à jour les unités de requête/s pour une table.|
| [Verrouiller des ressources contre la suppression](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources.|
|||
