---
title: Exemples Azure PowerShell pour Azure Cosmos DB - API MongoDB
description: Procurez-vous les exemples Azure PowerShell pour effectuer diverses tâches courantes dans l’API d’Azure Cosmos DB pour MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 697dc68018ced08e22efdc179f84a2c968f953a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563863"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Exemples Azure PowerShell pour Azure Cosmos DB - API MongoDB

Le tableau suivant comprend des liens vers des exemples de scripts Azure PowerShell pour l’API MongoDB Azure Cosmos DB.

> [!NOTE]
> Actuellement, vous pouvez uniquement créer la version 3.2 (c’est-à-dire, les comptes utilisant le point de terminaison au format `*.documents.azure.com`) de l’API d’Azure Cosmos DB pour les comptes MongoDB à l’aide de PowerShell, de l’interface CLI et des modèles Resource Manager. Pour créer la version 3.6 des comptes, utilisez le portail Azure à la place.

> [!NOTE]
> Les exemples utilisent les applets de commande de gestion [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Vérifiez régulièrement les mises à jour de `Az.CosmosDB`.

|Tâche | Description |
|---|---|
|[Créer un compte, une base de données et une collection](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte, une base de données et une collection Azure Cosmos. |
|[Lister ou obtenir des bases de données et des collections](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Listez ou obtenez des bases de données et des collections. |
|[Obtenir les RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenez les RU/s d’une base de données ou d’une collection. |
|[Mettre à jour les RU/s](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Mettez à jour les RU/s d’une base de données ou d’une collection. |
|[Mettre à jour un compte ou ajouter une région](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ajoutez une région à un compte Cosmos. Peut également être utilisé pour modifier d’autres propriétés de compte, mais celles-ci doivent être séparées des modifications apportées aux régions. |
|[Modifier la priorité de basculement ou déclencher un basculement](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Modifiez la priorité de basculement régional d’un compte Azure Cosmos ou déclenchez un basculement manuel. |
|[Clés de compte ou chaînes de connexion](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenez des clés primaires et secondaires, des chaînes de connexion, ou regénérez une clé de compte d’un compte Azure Cosmos. |
|[Créer un compte Cosmos avec un pare-feu IP](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Créez un compte Azure Cosmos avec pare-feu IP activé. |
|[Verrouiller des ressources contre la suppression](scripts/powershell/mongodb/powershell-mongodb-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Empêchez la suppression de ressources à l’aide de verrous de ressources. |
|||
