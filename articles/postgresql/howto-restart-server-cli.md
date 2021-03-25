---
title: Redémarrer un serveur - Azure CLI - Azure Database pour PostgreSQL - Serveur unique
description: Cet article explique comment redémarrer un serveur Azure Database pour PostgreSQL - Single Server à l’aide d’Azure CLI.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e812b7872dd4d41d9a6cb87d75403524106c5981
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97706862"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Redémarrer une base de données Azure Database pour PostgreSQL - Single Server à l’aide d’Azure CLI
Cette rubrique explique comment redémarrer un serveur Azure Database pour PostgreSQL. Vous pouvez avoir besoin de redémarrer votre serveur pour des raisons de maintenance, ce qui entraîne une brève interruption de service pendant que le serveur effectue l’opération.

Le redémarrage du serveur est bloqué si le service est occupé. Par exemple, le service peut traiter une opération précédemment demandée, telle que la mise à l’échelle de vCores.
 
> [!NOTE] 
> Le temps nécessaire à un redémarrage varie selon le processus de récupération de PostgreSQL. Pour réduire le délai de redémarrage, nous vous recommandons de diminuer la quantité d’activités se produisant sur le serveur avant le redémarrage. Vous pouvez également augmenter la fréquence des points de contrôle. Vous pouvez également régler les valeurs des paramètres liés aux points de contrôle, y compris `max_wal_size`. Il est également recommandé d’exécuter la commande `CHECKPOINT` avant de redémarrer le serveur.

## <a name="prerequisites"></a>Prérequis
Pour suivre cette procédure :
- Créez un [serveur Azure Database pour PostgreSQL](quickstart-create-server-up-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Cet article demande la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="restart-the-server"></a>Redémarrez le serveur

Redémarrez le serveur en utilisant la commande suivante :

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment configurer les paramètres dans Azure Database pour PostgreSQL](howto-configure-server-parameters-using-cli.md)
