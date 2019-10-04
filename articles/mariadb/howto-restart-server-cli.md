---
title: Redémarrer un serveur Azure Database for MariaDB à l’aide d’Azure CLI
description: Cet article explique comment redémarrer un serveur Azure Database for MariaDB à l’aide d’Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/28/2019
ms.openlocfilehash: a6e0509d941d9bfdfe6db7a8b93ee49c5bece1a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171445"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Redémarrer un serveur Azure Database for MariaDB à l’aide d’Azure CLI
Cette rubrique explique comment redémarrer un serveur Azure Database for MariaDB. Vous pouvez avoir besoin de redémarrer votre serveur pour des raisons de maintenance, ce qui entraîne une brève interruption de service pendant que le serveur effectue l’opération.

Le redémarrage du serveur est bloqué si le service est occupé. Par exemple, le service peut traiter une opération précédemment demandée, telle que la mise à l’échelle de vCores.

Le temps nécessaire à un redémarrage varie selon le processus de récupération de MariaDB. Pour réduire le délai de redémarrage, nous vous recommandons de diminuer la quantité d’activités se produisant sur le serveur avant le redémarrage.

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ce guide de procédures requiert l’utilisation de la version 2.0 Azure CLI ou version ultérieure. Pour vérifier la version, à l’invite de commande de l’interface Azure CLI, entrez `az --version`. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Redémarrez le serveur

Redémarrez le serveur en utilisant la commande suivante :

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment configurer les paramètres dans Azure Database for MariaDB](howto-configure-server-parameters-cli.md)