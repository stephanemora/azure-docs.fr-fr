---
title: Redémarrer un serveur - Azure CLI - Azure Database pour MySQL
description: Cet article explique comment redémarrer un serveur Azure Database pour MySQL à l’aide d’Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 76ee5e413d325672ac1e87f62bbfa2996ab44831
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641151"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Redémarrer un serveur Azure Database pour MySQL à l’aide d’Azure CLI

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
Cette rubrique explique comment redémarrer un serveur Azure Database pour MySQL. Vous pouvez avoir besoin de redémarrer votre serveur pour des raisons de maintenance, ce qui entraîne une brève interruption de service pendant que le serveur effectue l’opération.

Le redémarrage du serveur est bloqué si le service est occupé. Par exemple, le service peut traiter une opération précédemment demandée, telle que la mise à l’échelle de vCores.

Le temps nécessaire à un redémarrage varie selon le processus de récupération de MySQL. Pour réduire le délai de redémarrage, nous vous recommandons de diminuer la quantité d’activités se produisant sur le serveur avant le redémarrage.

## <a name="prerequisites"></a>Prérequis

Pour suivre cette procédure :

- Vous avez besoin d’un [serveur Azure Database pour MySQL](quickstart-create-server-up-azure-cli.md).
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Cet article demande la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="restart-the-server"></a>Redémarrez le serveur

Redémarrez le serveur en utilisant la commande suivante :

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment configurer les paramètres dans Azure Database pour MySQL](howto-configure-server-parameters-using-cli.md)
