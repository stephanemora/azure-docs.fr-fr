---
title: Redémarrez Azure Database pour MySQL server à l’aide d’Azure CLI
description: Cet article décrit comment vous pouvez redémarrer un serveur Azure Database pour MySQL à l’aide de l’interface CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/28/2019
ms.openlocfilehash: d00aa35437f93c010ce48f3036b4a684910702c0
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58623152"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Redémarrez Azure Database pour MySQL server à l’aide de l’interface CLI Azure
Cette rubrique explique comment redémarrer un serveur Azure Database pour MySQL. Vous pouvez avoir besoin de redémarrer votre serveur pour des raisons de maintenance, ce qui entraîne une brève interruption de service pendant que le serveur effectue l’opération.

Le redémarrage du serveur est bloqué si le service est occupé. Par exemple, le service peut traiter une opération précédemment demandée, telle que la mise à l’échelle de vCores.

Le temps nécessaire à un redémarrage varie selon le processus de récupération de MySQL. Pour réduire le délai de redémarrage, nous vous recommandons de diminuer la quantité d’activités se produisant sur le serveur avant le redémarrage.

## <a name="prerequisites"></a>Conditions préalables
Pour utiliser ce guide pratique, il vous faut :
- Un [Azure Database pour MySQL server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ce guide de procédures requiert l’utilisation de la version 2.0 Azure CLI ou version ultérieure. Pour vérifier la version, à l’invite de commande de l’interface Azure CLI, entrez `az --version`. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Redémarrez le serveur

Redémarrez le serveur avec la commande suivante :

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [comment définir des paramètres dans la base de données Azure pour MySQL](howto-configure-server-parameters-using-cli.md)