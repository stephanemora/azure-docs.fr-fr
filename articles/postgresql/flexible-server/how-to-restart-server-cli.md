---
title: Redémarrer - Portail Azure - Azure Database pour PostgreSQL - Serveur flexible
description: Cet article explique comment redémarrer des opérations dans Azure Database pour PostgreSQL à l’aide d’Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 05/29/2021
ms.openlocfilehash: 6687603572d752a1f433213dc8a4068275298cae
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025792"
---
# <a name="restart-an-azure-database-for-postgresql---flexible-server-preview"></a>Redémarrer un serveur flexible Azure Database pour PostgreSQL (préversion)

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est en actuellement en préversion publique.

Cet article explique comment redémarrer, démarrer et arrêter un serveur flexible à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Installez ou mettez à niveau Azure CLI vers la dernière version. Consultez [Installer Azure CLI](/cli/azure/install-azure-cli).
-  Connectez-vous au compte Azure à l’aide de la commande [az login](/cli/azure/reference-index#az_login). Notez la propriété **id**, qui fait référence à l’**ID d’abonnement** pour votre compte Azure.

    ```azurecli-interactive
    az login
    ````

- Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel vous souhaitez créer le serveur à l’aide de la commande ```az account set```.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Créez un serveur flexible PostgreSQL si vous n’en avez pas encore créé à l’aide de la commande ```az postgres flexible-server create```.

    ```azurecli
    az postgres flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="restart-a-server"></a>Redémarrer un serveur
Pour redémarrer un serveur, exécutez la commande ```az postgres flexible-server restart```. Si vous utilisez un [contexte local](/cli/azure/config/param-persist), vous n’avez pas besoin de fournir d’arguments.

**Utilisation :**
```azurecli
az postgres flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**Exemple sans contexte local :**
```azurecli
az postgres flexible-server restart  --resource-group --name myservername
```

**Exemple avec contexte local :**
```azurecli
az postgres flexible-server restart
```

> [!IMPORTANT]
> Une fois que le serveur a correctement redémarré, toutes les opérations de gestion sont disponibles pour le serveur flexible.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [l’arrêt et le démarrage d’Azure Database pour PostgreSQL - Serveur flexible](./how-to-stop-start-server-cli.md)


