---
title: Exemples Azure PowerShell pour Azure Cosmos DB
description: Exemples Azure PowerShell - Des scripts pour vous aider à créer et gérer des comptes Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 3498ac6a2a4aaa1682d7b5bc5aae5383866d5bcd
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873795"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemples Azure PowerShell pour Azure Cosmos DB

Le tableau suivant comprend des liens vers des exemples de scripts Azure PowerShell pour Azure Cosmos DB. À ce stade, vous pouvez uniquement gérer le compte Azure Cosmos DB par le biais de PowerShell ; les autres ressources telles que les bases de données et les conteneurs ne peuvent pas être gérées par le biais de PowerShell.

| |  |
|---|---|
|**Création d’un compte Azure Cosmos DB**||
|[Création et configuration d’un compte Cosmos avec l’API SQL](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un seul compte Azure Cosmos DB à utiliser avec l’API SQL. |
|[Création et configuration d’un compte Cosmos avec l’API Azure Cosmos DB pour MongoDB](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un seul compte Cosmos avec l’API Azure Cosmos DB pour MongoDB. |
|[Créer et configurer un compte Cosmos avec l’API Gremlin](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un seul compte Azure Cosmos DB à utiliser avec l’API Gremlin. |
|[Création et configuration d’un compte Cosmos avec l’API Cassandra](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un seul compte Azure Cosmos DB à utiliser avec l’API Cassandra. |
|[Création et configuration d’un compte Cosmos avec l’API Table](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée un seul compte Azure Cosmos DB à utiliser avec l’API Table. |
|**Mettre à l’échelle Azure Cosmos DB**||
|[Répliquer un compte Azure Cosmos DB dans plusieurs régions et configurer les priorités de basculement](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Réplique les données de compte globalement dans plusieurs régions avec une priorité de basculement spécifié.|
|**Sécuriser Azure Cosmos DB**||
| [Obtenir les clés de compte](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtient les clés primaire et secondaire d’écriture maître et les clés primaire et secondaire de lecture seule pour le compte.|
| [Obtenir la chaîne de connexion MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtient la chaîne de connexion pour connecter votre application MongoDB à votre compte Azure Cosmos DB.|
|[Régénération des clés de compte](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Régénère la clé de maître ou en lecture seule pour le compte.|
|[Créer un pare-feu](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crée une stratégie de contrôle d’accès des IP entrantes pour limiter l’accès au compte à un ensemble de machines et/ou services cloud approuvés.|
|**Haute disponibilité et récupération d’urgence, sauvegarde et restauration**||
|[Configurer la stratégie de basculement](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Définit la priorité de basculement de chaque région dans laquelle le compte est répliqué.|
|||
