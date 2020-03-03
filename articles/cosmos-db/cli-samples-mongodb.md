---
title: Exemples Azure CLI pour l’API MongoDB Azure Cosmos DB
description: Exemples Azure CLI pour l’API MongoDB Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.author: mjbrown
ms.openlocfilehash: f7807a4c2024e16f563adbcb46a5c60e5c542dda
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524561"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Exemples Azure CLI pour l’API MongoDB Azure Cosmos DB

Le tableau suivant comprend des liens vers des exemples de scripts Azure CLI pour l’API MongoDB Azure Cosmos DB. Des pages d’informations de référence pour toutes les commandes Azure Cosmos DB CLI sont disponibles dans les [Informations de référence sur Azure CLI](/cli/azure/cosmosdb). Vous trouverez tous les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> Actuellement, vous pouvez uniquement créer la version 3.2 (c’est-à-dire, les comptes utilisant le point de terminaison au format `*.documents.azure.com`) de l’API d’Azure Cosmos DB pour les comptes MongoDB à l’aide de PowerShell, de l’interface CLI et des modèles Resource Manager. Pour créer la version 3.6 des comptes, utilisez le portail Azure à la place.

| |  |
|---|---|
| [Créer un compte, une base de données et une collection Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte, une base de données et une collection Azure Cosmos DB pour l’API MongoDB. |
| [Changer le débit](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Mettez à jour les unités de requête/s pour une base de données et une collection.|
| [Ajouter des régions de basculement](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Ajoutez une région, changez la priorité de basculement, déclenchez un basculement manuel.|
| [Clés de compte et chaînes de connexion](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Listez les clés de compte, les clés en lecture seule, regénérez les clés et listez les chaînes de connexion.|
| [Sécuriser avec le pare-feu IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte Cosmos avec le pare-feu IP configuré.|
| [Sécuriser un nouveau compte avec des points de terminaison de service](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte Cosmos et sécurisez-le avec des points de terminaison de service.|
| [Sécuriser un compte existant avec des points de terminaison de service](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Mettez à jour un compte Cosmos pour le sécuriser avec des points de terminaison de service quand le sous-réseau est finalement configuré.|
|||
