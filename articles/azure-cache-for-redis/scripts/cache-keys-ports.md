---
title: Exemple de script Azure CLI - Obtenir le nom d’hôte, les ports et les clés pour un cache Azure pour Redis | Microsoft Docs
description: Exemple de script Azure CLI - Obtenir le nom d’hôte, les ports et les clés pour une instance du cache Azure pour Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: ff410db561879089c4c1f20acb7cb349f0484fda
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234301"
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
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Récupérez les détails d’une instance du cache Azure pour Redis. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Récupérez les clés d’accès pour une instance du cache Azure pour Redis. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Des exemples supplémentaires de scripts CLI de Cache Azure pour Redis sont disponibles dans la [documentation du Cache Azure pour Redis](../cli-samples.md).
