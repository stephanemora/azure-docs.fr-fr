---
title: Obtenir le nom d’hôte, les ports et les clés - Azure Cache for Redis - Azure CLI
description: Cet exemple de code Azure CLI montre comment obtenir le nom d’hôte, les ports et les clés pour une instance Azure Cache for Redis.
author: curib
ms.author: cauribeg
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 096443b1ec83c5aa7bec6446f3a67f6a750944b1
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534250"
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
| [az redis show](/cli/azure/redis) | Récupérez les détails d’une instance du Cache Azure pour Redis. |
| [az redis list-keys](/cli/azure/redis) | Récupérez les clés d’accès pour une instance du cache Azure pour Redis. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Des exemples supplémentaires de scripts CLI de Cache Azure pour Redis sont disponibles dans la [documentation du Cache Azure pour Redis](../cli-samples.md).