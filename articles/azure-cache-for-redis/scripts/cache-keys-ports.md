---
title: Obtenir le nom d’hôte, les ports et les clés - Azure Cache for Redis - Azure CLI
description: Cet exemple de code Azure CLI montre comment obtenir le nom d’hôte, les ports et les clés pour une instance Azure Cache for Redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9fef834e550f144075ec9cc72cac3b11cc1e99e4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504209"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Obtenir le nom d’hôte, les ports et les clés pour le cache Azure pour Redis

Dans ce scénario, vous apprenez comment récupérer le nom d’hôte, les ports et les clés utilisés pour se connecter à une instance du cache Azure pour Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour récupérer le nom d’hôte, les clés et les ports d’une instance du cache Azure pour Redis. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Récupérez les détails d’une instance du Cache Azure pour Redis. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Récupérez les clés d’accès pour une instance du cache Azure pour Redis. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Des exemples supplémentaires de scripts CLI de Cache Azure pour Redis sont disponibles dans la [documentation du Cache Azure pour Redis](../cli-samples.md).
