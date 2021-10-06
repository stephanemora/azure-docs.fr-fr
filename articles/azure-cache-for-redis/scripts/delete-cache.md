---
title: Supprimer une instance Azure Cache for Redis - Azure CLI
description: Cet exemple de code Azure CLI montre comment supprimer une instance Azure Cache for Redis à l’aide de la commande az redis delete.
author: curib
ms.author: cauribeg
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93c929d03ef94561ce00479bddbdd2d71fc062ff
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536335"
---
# <a name="delete-an-azure-cache-for-redis"></a>Supprimer un Cache Azure pour Redis

Dans ce scénario, vous apprenez comment supprimer un Cache Azure pour Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour supprimer une instance du Cache Azure pour Redis. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az redis delete](/cli/azure/redis) | Supprimez une instance du Cache Azure pour Redis. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Des exemples supplémentaires de scripts CLI de Cache Azure pour Redis sont disponibles dans la [documentation du Cache Azure pour Redis](../cli-samples.md).