---
title: Augmenter automatiquement le stockage - Azure CLI - Azure Database for MariaDB
description: Cet article explique comment vous pouvez activer la croissance automatique du stockage à l’aide de l’interface Azure CLI dans Azure Database for MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e46539e76eec73554f6e57ea09a373b79520def9
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426083"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Activer la croissance automatique du stockage Azure Database for MariaDB à l’aide de l’interface Azure CLI
Cet article explique comment vous pouvez configurer l’augmentation d’un stockage de serveur Azure Database for MariaDB sans affecter la charge de travail.

Le serveur [qui atteint la limite de stockage](concepts-pricing-tiers.md#reaching-the-storage-limit) est placé en lecture seule. Si la croissance automatique du stockage est activée pour les serveurs avec moins de 100 Go de stockage provisionnés, la taille du stockage provisionné augmente de 5 Go dès que l’espace de stockage disponible est inférieur à 1 Go ou à 10 % du stockage provisionné (selon la valeur la plus élevée). Pour les serveurs avec plus de 100 Go de stockage approvisionnés, la taille de stockage approvisionné augmente de 5 % lorsque l’espace de stockage libre est inférieur à 5 % de la taille de stockage approvisionné. Les limites de stockage maximales spécifiées [ici](concepts-pricing-tiers.md#storage) s’appliquent.

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ce guide de procédures requiert l’utilisation de la version 2.0 Azure CLI ou version ultérieure. Pour vérifier la version, à l’invite de commande de l’interface Azure CLI, entrez `az --version`. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-mariadb-server-storage-auto-grow"></a>Activer la croissance automatique du stockage du serveur MariaDB

Activez le stockage à croissance automatique du serveur sur un serveur existant avec la commande suivante :

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Activez le stockage à croissance automatique du serveur sur un nouveau serveur avec la commande suivante :

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment créer des alertes sur des métriques](howto-alert-metric.md).
