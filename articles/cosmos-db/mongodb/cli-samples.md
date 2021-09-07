---
title: Exemples Azure CLI pour l’API Azure Cosmos DB pour MongoDB
description: Exemples Azure CLI pour l’API Azure Cosmos DB pour MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 08/26/2021
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 04ac3178b439e755bbcbd33855ff0bab9a566784
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029197"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-api-for-mongodb"></a>Exemples Azure CLI pour l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Le tableau suivant comprend des liens vers des exemples de scripts Azure CLI pour Azure Cosmos DB. Utilisez les liens sur la droite pour accéder aux exemples spécifiques aux API. Les exemples communs sont les mêmes pour toutes les API. Des pages d’informations de référence pour toutes les commandes Azure Cosmos DB CLI sont disponibles dans les [Informations de référence sur Azure CLI](/cli/azure/cosmosdb). Vous trouverez également les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Ces exemples nécessitent Azure CLI version 2.12.1 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="common-samples"></a>Exemples communs

Ces exemples s’appliquent à toutes les API Azure Cosmos DB.

|Tâche | Description |
|---|---|
| [Ajouter des régions de basculement](../scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Ajoutez une région, changez la priorité de basculement, déclenchez un basculement manuel.|
| [Clés de compte et chaînes de connexion](../scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Listez les clés de compte, les clés en lecture seule, regénérez les clés et listez les chaînes de connexion.|
| [Sécuriser avec le pare-feu IP](../scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte Cosmos avec le pare-feu IP configuré.|
| [Sécuriser un nouveau compte avec des points de terminaison de service](../scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte Cosmos et sécurisez-le avec des points de terminaison de service.|
| [Sécuriser un compte existant avec des points de terminaison de service](../scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Mettez à jour un compte Cosmos pour le sécuriser avec des points de terminaison de service quand le sous-réseau est finalement configuré.|
|||

## <a name="mongodb-api-samples"></a>Exemples d’API MongoDB

|Tâche | Description |
|---|---|
| [Créer un compte, une base de données et une collection Azure Cosmos](../scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Créez un compte, une base de données et une collection Azure Cosmos DB pour l’API MongoDB. |
| [Créer un compte et une base de données avec mise à l’échelle automatique et deux collections Azure Cosmos avec débit partagé](../scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Crée un compte et une base de données avec mise à l’échelle automatique et deux collections Azure Cosmos DB avec débit partagé pour l’API MongoDB. |
| [Opérations de débit](../scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Opérations sur une base de données et une collection, comme la lecture du débit, la mise à jour du débit et la migration entre le débit standard et le débit avec mise à l’échelle automatique.|
| [Verrouiller des ressources contre la suppression](../scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources.|
|||

## <a name="next-steps"></a>Étapes suivantes

Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
* Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existantes, lisez l’article sur l’[estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
* Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-capacity-planner.md)
