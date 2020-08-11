---
title: Exemples Azure PowerShell pour Azure Cosmos DB
description: Obtenir les exemples Azure PowerShell pour effectuer des tâches courantes dans Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 678e9490fe52a329365174e1a2283a475f3312e5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505013"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemples Azure PowerShell pour Azure Cosmos DB

Le tableau suivant comprend des liens vers des scripts Azure PowerShell couramment utilisés pour Azure Cosmos DB. Utilisez les liens sur la droite pour accéder aux exemples spécifiques aux API. Les exemples communs sont les mêmes pour toutes les API. Des pages d’informations de référence pour toutes les applets de commande PowerShell Azure Cosmos DB sont disponibles dans les [Informations de référence sur Azure PowerShell](/powershell/module/az.cosmosdb). Vérifiez régulièrement les mises à jour de `Az.CosmosDB`. Vous pouvez également dupliquer (fork) ces exemples PowerShell pour Cosmos DB à partir de notre dépôt GitHub : [Exemples PowerShell pour Cosmos DB sur GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Exemples communs

|Tâche | Description |
|---|---|
|[Mettre à jour un compte](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mettez à jour le niveau de cohérence par défaut d’un compte Cosmos DB. |
|[Mettre à jour les régions d’un compte](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mettez à jour les régions d’un compte Cosmos DB. |
|[Modifier la priorité de basculement ou déclencher un basculement](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Modifiez la priorité de basculement régional d’un compte Azure Cosmos ou déclenchez un basculement manuel. |
|[Clés de compte ou chaînes de connexion](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenez des clés primaires et secondaires, des chaînes de connexion, ou regénérez une clé de compte d’un compte Azure Cosmos DB. |
|[Créer un compte Cosmos avec un pare-feu IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte Azure Cosmos DB avec le pare-feu IP activé. |
|||

## <a name="core-sql-api-samples"></a>Exemples d’API Core (SQL)

|Tâche | Description |
|---|---|
|[Créer un compte, une base de données et un conteneur](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte, une base de données et un conteneur Azure Cosmos DB. |
|[Créer un compte, une base de données et un conteneur avec mise à l’échelle automatique](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un compte, une base de données et un conteneur Azure Cosmos DB avec mise à l’échelle automatique. |
|[Créer un conteneur avec une grande clé de partition](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un conteneur avec une grande clé de partition. |
|[Créer un conteneur sans stratégie d’index](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Créer un conteneur Azure Cosmos dont la stratégie d’index est désactivée.|
|[Lister ou obtenir des bases de données et des conteneurs](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Listez ou obtenez des bases de données et des conteneurs. |
|[Obtenir le débit](scripts/powershell/sql/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtient le débit d’une base de données ou d’un conteneur. |
|[Mettre à jour le débit](scripts/powershell/sql/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met à jour le débit d’une base de données ou d’un conteneur. |
|[Verrouiller des ressources contre la suppression](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources. |
|||

## <a name="cassandra-api-samples"></a>Exemples d’API Cassandra

|Tâche | Description |
|---|---|
|[Créer un compte, un espace de clés et une table](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un compte, un espace de clés et une table Azure Cosmos. |
|[Créer un compte, un espace de clés et une table avec mise à l’échelle automatique](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un compte, un espace de clés et une table Azure Cosmos avec mise à l’échelle automatique. |
|[Lister ou obtenir des espaces de clés et des tables](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Listez ou obtenez des espaces de clés et des tables. |
|[Obtenir le débit](scripts/powershell/cassandra/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtient le débit d’un espace de clés ou d’une table. |
|[Mettre à jour le débit](scripts/powershell/cassandra/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met à jour le débit d’un espace de clés ou d’une table. |
|[Verrouiller des ressources contre la suppression](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources. |
|||

## <a name="mongo-db-api-samples"></a>Exemples d’API Mongo DB

|Tâche | Description |
|---|---|
|[Créer un compte, une base de données et une collection](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte, une base de données et une collection Azure Cosmos. |
|[Créer un compte, une base de données et une collection avec mise à l’échelle automatique](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un compte, une base de données et une collection Azure Cosmos avec mise à l’échelle automatique. |
|[Lister ou obtenir des bases de données et des collections](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Listez ou obtenez des bases de données et des collections. |
|[Obtenir le débit](scripts/powershell/mongodb/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtient le débit d’une base de données ou d’une collection. |
|[Mettre à jour le débit](scripts/powershell/mongodb/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met à jour le débit d’une base de données ou d’une collection. |
|[Verrouiller des ressources contre la suppression](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources. |
|||

## <a name="gremlin-api-samples"></a>Exemples d’API Gremlin

|Tâche | Description |
|---|---|
|[Créer un compte, une base de données et un graphe](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte, une base de données et un graphe Azure Cosmos. |
|[Créer un compte, une base de données et un graphe avec mise à l’échelle automatique](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un compte, une base de données et un graphe Azure Cosmos avec mise à l’échelle automatique. |
|[Lister ou obtenir des bases de données et des graphes](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Listez ou obtenez des bases de données et des graphes. |
|[Obtenir le débit](scripts/powershell/gremlin/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtient le débit d’une base de données ou d’un graphe. |
|[Mettre à jour le débit](scripts/powershell/gremlin/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met à jour le débit d’une base de données ou d’un graphe. |
|[Verrouiller des ressources contre la suppression](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources. |
|||

## <a name="table-api-samples"></a>Exemples d’API Table

|Tâche | Description |
|---|---|
|[Créer un compte et une table](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte et une table Azure Cosmos. |
|[Créer un compte et une table avec mise à l’échelle automatique](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un compte et une table Azure Cosmos avec mise à l’échelle automatique. |
|[Lister ou obtenir des tables](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Listez ou obtenez des tables. |
|[Obtenir le débit](scripts/powershell/table/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtient le débit d’une table. |
|[Mettre à jour le débit](scripts/powershell/table/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met à jour le débit d’une table. |
|[Verrouiller des ressources contre la suppression](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources. |
|||
