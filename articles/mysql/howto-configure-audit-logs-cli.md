---
title: Accéder aux journaux d’audit - Azure CLI - Azure Database pour MySQL
description: Cet article décrit comment configurer et consulter les journaux d’audit dans Azure Database pour MySQL à partir d’Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 6/24/2020
ms.openlocfilehash: 249fdc83e42384e4d989293078e842a8bb2a8583
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641132"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurer et consulter les journaux d’audit dans Azure CLI

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Vous pouvez configurer les [journaux d’audit Azure Database pour MySQL](concepts-audit-logs.md) à partir d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

Pour parcourir ce guide pratique :

- Vous avez besoin d’un [serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

[!INCLUDE[azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Cet article demande la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="configure-audit-logging"></a>Configurer l’enregistrement d’audit

> [!IMPORTANT]
> Il est recommandé de ne consigner que les types d’événements et les utilisateurs requis à des fins d’audit pour garantir que les performances de votre serveur ne sont pas fortement affectées.

Activez et configurez l'enregistrement d'audit en procédant comme suit :

1. Activez les journaux d’audit en définissant le paramètre **audit_logs_enabled** sur « ON ». 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

2. Sélectionnez les [types d’événements](concepts-audit-logs.md#configure-audit-logging) à enregistrer en mettant à jour le paramètre **audit_log_events**.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

3. Ajoutez des utilisateurs MySQL à exclure de l’enregistrement en mettant à jour le paramètre **audit_log_exclude_users**. Spécifiez les utilisateurs en fournissant leur nom d’utilisateur MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

4. Ajoutez des utilisateurs MySQL spécifiques à exclure de l’enregistrement en mettant à jour le paramètre **audit_log_include_users**. Spécifiez les utilisateurs en fournissant leur nom d’utilisateur MySQL.

    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Étapes suivantes
- Apprenez-en davantage sur les [journaux d’audit](concepts-audit-logs.md) dans Azure Database pour MySQL
- Guide pratique pour configurer les journaux d’audit dans le [portail Azure](howto-configure-audit-logs-portal.md)
