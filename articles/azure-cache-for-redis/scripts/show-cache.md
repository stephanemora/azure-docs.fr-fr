---
title: Obtenir les détails d’une instance Azure Cache for Redis - Azure CLI
description: Cet exemple de code Azure CLI montre comment récupérer les détails d’une instance Azure Cache for Redis, notamment son état de provisionnement.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 54920017f4a0598a74a54114e3d1e0a5392d7d2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411058"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Obtenir les détails d’un Cache Azure pour Redis

Dans ce scénario, vous allez apprendre à récupérer les détails d’une instance de Cache Azure pour Redis, y compris son état d’approvisionnement.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour récupérer les détails d’une instance du Cache Azure pour Redis. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Récupérez les détails d’une instance du Cache Azure pour Redis. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Des exemples supplémentaires de scripts CLI de Cache Azure pour Redis sont disponibles dans la [documentation du Cache Azure pour Redis](../cli-samples.md).
