---
title: Redémarrer/Arrêter/Démarrer - Portail Azure - Serveur flexible Azure Database pour MySQL
description: Cet article explique comment redémarrer/arrêter/démarrer des opérations dans Azure Database pour MySQL à l’aide de l’interface Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: c9e646ad40c669e51f052ac9888cdd7c6883a848
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107508941"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Redémarrer/Arrêter/Démarrer un serveur flexible Azure Database pour MySQL (préversion)

> [!IMPORTANT]
> Azure Database pour MySQL - Serveur flexible est actuellement en préversion publique.

Cet article explique comment redémarrer, démarrer et arrêter un serveur flexible à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Installez ou mettez à niveau Azure CLI vers la dernière version. Consultez [Installer Azure CLI](/cli/azure/install-azure-cli).
-  Connectez-vous au compte Azure à l’aide de la commande [az login](/cli/azure/reference-index#az-login). Notez la propriété **id**, qui fait référence à l’**ID d’abonnement** pour votre compte Azure.

    ```azurecli-interactive
    az login
    ````

- Si vous avez plusieurs abonnements, choisissez l’abonnement approprié dans lequel vous voulez créer le serveur à l’aide de la commande ```az account set```.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Créez un serveur flexible MySQL si vous n’en avez pas encore créé à l’aide de la commande ```az mysql flexible-server create```.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>Arrêter un serveur en cours d’exécution
Pour arrêter un serveur, exécutez la commande ```az mysql flexible-server stop```. Si vous utilisez un [contexte local](/cli/azure/config/param-persist), vous n’avez pas besoin de fournir d’arguments.

**Utilisation :**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**Exemple sans contexte local :**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**Exemple avec contexte local :**
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>Démarrer un serveur arrêté
Pour démarrer un serveur, exécutez la commande ```az mysql flexible-server start```. Si vous utilisez un [contexte local](/cli/azure/config/param-persist), vous n’avez pas besoin de fournir d’arguments.

**Utilisation :**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**Exemple sans contexte local :**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**Exemple avec contexte local :**
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
> Une fois que le serveur a correctement redémarré, toutes les opérations de gestion sont disponibles pour le serveur flexible.

## <a name="restart-a-server"></a>Redémarrer un serveur
Pour redémarrer un serveur, exécutez la commande ```az mysql flexible-server restart```. Si vous utilisez un [contexte local](/cli/azure/config/param-persist), vous n’avez pas besoin de fournir d’arguments.

**Utilisation :**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**Exemple sans contexte local :**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**Exemple avec contexte local :**
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
> Une fois que le serveur a correctement redémarré, toutes les opérations de gestion sont disponibles pour le serveur flexible.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [réseaux dans un serveur flexible Azure Database pour MySQL](./concepts-networking.md)
- [Créer et gérer un réseau virtuel de serveur flexible Azure Database pour MySQL avec le portail Azure](./how-to-manage-virtual-network-portal.md).

