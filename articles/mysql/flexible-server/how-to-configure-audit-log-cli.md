---
title: Configurer des journaux d’audit avec Azure CLI - Azure Database pour MySQL - Serveur flexible
description: Cet article décrit les procédures de configuration et de consultation des journaux d’audit sur un serveur flexible Azure Database pour MySQL à partir d’Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: ac24c933cefda60f8ed0bb305c2d49800bd2d610
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641170"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Configurer et consulter des journaux d’audit sur un serveur flexible Azure Database pour MySQL à l’aide d’Azure CLI

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database pour MySQL - Serveur flexible est actuellement en préversion publique.

L’article vous montre la procédure de configuration de [journaux d’audit](concepts-audit-logs.md) pour votre serveur MySQL flexible à l’aide d’Azure CLI.

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

- Créez un serveur MySQL flexible si vous n’en avez pas encore créé à l’aide de la commande ```az mysql flexible-server create```.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-audit-logging"></a>Configurer l’enregistrement d’audit

>[!IMPORTANT]
> Il est recommandé de ne consigner que les types d’événements et les utilisateurs requis à des fins d’audit pour garantir que les performances de votre serveur ne sont pas fortement affectées.

Activez et configurez l’enregistrement d’audit.

```azurecli
# Enable audit logs

az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [journaux d'audit](concepts-audit-logs.md)
