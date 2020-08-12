---
title: Gérer Azure Cache pour Redis avec Azure CLI
description: 'Exemples Azure CLI pour la gestion d’Azure Cache pour Redis : Créer un cache, supprimer un cache, obtenir les détails du cache, le nom d’hôte, les ports et les clés, connecter une application web.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9bfdd2d03b3ab6edd04a641787475930435a9ffc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499600"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Gérer Azure Cache pour Redis avec Azure CLI

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure.

| Create cache | Description |
| ------------ | ----------- |
| [Créer un cache](./scripts/create-cache.md) | Crée un groupe de ressources et un Cache Azure pour Redis de niveau de base. |
| [Créer un cache Premium avec clustering](./scripts/create-premium-cache-cluster.md) | Crée un groupe de ressources et un cache de niveau Premium avec activation du clustering.|
| [Obtenir les détails du cache](./scripts/show-cache.md) | Obtient les détails d’une instance du Cache Azure pour Redis, dont l’état d’approvisionnement. |
| [Obtenir le nom d’hôte, les ports et les clés](./scripts/cache-keys-ports.md) | Obtient le nom d’hôte, les ports et les clés d’une instance du Cache Azure pour Redis. |
|**Application web avec cache**| **Description**|
| [Connecter une application web à un Cache Azure pour Redis](./../app-service/scripts/cli-connect-to-redis.md) | Crée une application web Azure et un Cache Azure pour Redis, puis ajoute les détails de connexion redis aux paramètres d’application. |
|**Supprimer un cache**| **Description** |
| [Supprimer un cache](./scripts/delete-cache.md) | Supprime une instance du Cache Azure pour Redis  |

Pour plus d’informations sur l’interface de ligne de commande Azure, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) et [Bien démarrer avec Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
