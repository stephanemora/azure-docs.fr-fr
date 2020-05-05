---
title: Augmenter automatiquement le stockage - Azure CLI - Azure Database pour MySQL
description: Cet article explique comment vous pouvez activer la croissance automatique du stockage avec Azure CLI dans Azure Database pour MySQL.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 44ce852aaf2ed5839650132c6eae95728c27dc5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062632"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Activer la croissance automatique pour un serveur Azure Database pour MySQL avec Azure CLI
Cet article explique comment vous pouvez configurer l’augmentation d’un stockage de serveur Azure Database pour MySQL sans affecter la charge de travail.

Le serveur [qui atteint la limite de stockage](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit) est placé en lecture seule. Si la croissance automatique du stockage est activée pour les serveurs avec moins de 100 Go de stockage provisionnés, la taille du stockage provisionné augmente de 5 Go dès que l’espace de stockage disponible est inférieur à 1 Go ou à 10 % du stockage provisionné (selon la valeur la plus élevée). Pour les serveurs avec plus de 100 Go de stockage approvisionnés, la taille de stockage approvisionné augmente de 5 % lorsque l’espace de stockage libre est inférieur à 5 % de la taille de stockage approvisionné. Les limites de stockage maximales spécifiées [ici](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) s’appliquent.

## <a name="prerequisites"></a>Conditions préalables requises
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ce guide de procédures requiert l’utilisation de la version 2.0 Azure CLI ou version ultérieure. Pour vérifier la version, à l’invite de commande de l’interface Azure CLI, entrez `az --version`. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>Activer la croissance automatique du stockage du serveur MySQL

Activez le stockage à croissance automatique du serveur sur un serveur existant avec la commande suivante :

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Activez le stockage à croissance automatique du serveur sur un nouveau serveur avec la commande suivante :

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment créer des alertes sur des métriques](howto-alert-on-metric.md).