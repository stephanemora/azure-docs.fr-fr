---
title: Accéder aux journaux d’audit - Azure CLI - Azure Database pour MySQL
description: Cet article décrit comment configurer et consulter les journaux d’audit dans Azure Database pour MySQL à partir d’Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384118"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurer et consulter les journaux d’audit dans Azure CLI

Vous pouvez configurer les [journaux d’audit Azure Database pour MySQL](concepts-audit-logs.md) à partir d’Azure CLI.

> [!IMPORTANT]
> Pour l’instant, la fonctionnalité Journal d’audit n’existe qu’en préversion.

## <a name="prerequisites"></a>Prérequis

Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :

- [Serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ce guide de procédures requiert l’utilisation de la version 2.0 Azure CLI ou version ultérieure. Pour vérifier la version, à l’invite de commande de l’interface Azure CLI, entrez `az --version`. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configurer l’enregistrement d’audit

Activez et configurez l’enregistrement d’audit en procédant comme suit :

1. Activez les journaux d’audit en définissant le paramètre **audit_logs_enabled** sur « ON ». 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Sélectionnez les [types d’événements](concepts-audit-logs.md#configure-audit-logging) à enregistrer en mettant à jour le paramètre **audit_log_events**.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Ajoutez des utilisateurs MySQL à exclure de l’enregistrement en mettant à jour le paramètre **audit_log_exclude_users**. Spécifiez les utilisateurs en fournissant leur nom d’utilisateur MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Ajoutez des utilisateurs MySQL spécifiques à exclure de l’enregistrement en mettant à jour le paramètre **audit_log_include_users**. Spécifiez les utilisateurs en fournissant leur nom d’utilisateur MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Étapes suivantes
- Apprenez-en davantage sur les [journaux d’audit](concepts-audit-logs.md) dans Azure Database pour MySQL
- Guide pratique pour configurer les journaux d’audit dans le [portail Azure](howto-configure-audit-logs-portal.md)