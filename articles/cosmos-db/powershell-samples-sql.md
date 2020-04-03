---
title: Exemples Azure PowerShell pour Azure Cosmos DB - API SQL (Core)
description: Procurez-vous les exemples Azure PowerShell pour effectuer diverses tâches courantes dans les comptes d’API SQL Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366180"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Exemples Azure PowerShell pour Azure Cosmos DB - API SQL (Core)

Le tableau suivant comprend des liens vers des scripts Azure PowerShell couramment utilisés avec l’API Azure Cosmos DB pour SQL (Core). Si vous souhaitez dupliquer (fork) ces exemples PowerShell pour Cosmos dB à partir de notre dépôt GitHub, accédez à [Exemples Cosmos DB PowerShell sur GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Pour obtenir d’autres exemples Cosmos DB PowerShell pour l’API SQL (Core) et pour parcourir la documentation, consultez [Gérer les ressources de l’API SQL Azure Cosmos DB à l’aide de PowerShell](manage-with-powershell.md). Pour obtenir des exemples Cosmos DB PowerShell pour d’autres API, consultez [API Cassandra](powershell-samples-cassandra.md), [API MongoDB](powershell-samples-mongodb.md), [API Gremlin](powershell-samples-gremlin.md)et [API Table](powershell-samples-table.md).

> [!NOTE]
> Les exemples utilisent les applets de commande de gestion [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Notez que les applets de commande `Az.CosmosDB` sont toujours en préversion et peuvent changer avant la mise en production. Vérifiez régulièrement les mises à jour de `Az.CosmosDB`.

| | |
|---|---|
|[Créer un compte, une base de données et un conteneur](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte, une base de données et un conteneur Azure Cosmos DB. |
|[Créer un conteneur avec une grande clé de partition](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un conteneur avec une grande clé de partition. |
|[Lister ou obtenir des bases de données et des conteneurs](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Listez ou obtenez des bases de données et des conteneurs. |
|[Obtenir les RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenez les RU/s d’une base de données ou d’un conteneur. |
|[Mettre à jour les RU/s](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mettez à jour les RU/s d’une base de données ou d’un conteneur. |
|[Créer un conteneur sans stratégie d’index](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Créer un conteneur Azure Cosmos dont la stratégie d’index est désactivée.|
|[Mettre à jour un compte](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mettez à jour le niveau de cohérence par défaut d’un compte Cosmos DB. |
|[Mettre à jour les régions d’un compte](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mettez à jour les régions d’un compte Cosmos DB. |
|[Modifier la priorité de basculement ou déclencher un basculement](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Modifiez la priorité de basculement régional d’un compte Azure Cosmos ou déclenchez un basculement manuel. |
|[Clés de compte ou chaînes de connexion](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenez des clés primaires et secondaires, des chaînes de connexion, ou regénérez une clé de compte d’un compte Azure Cosmos DB. |
|[Créer un compte Cosmos avec un pare-feu IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte Azure Cosmos DB avec le pare-feu IP activé. |
|||
