---
title: Accéder aux journaux d’audit - Portail Azure - Azure Database pour MySQL
description: Cet article décrit comment configurer et consulter les journaux d’audit dans Azure Database pour MySQL à partir du portail Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: fcd436f87b24d8cdc246d2f1d33a49311e72d46b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380301"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Configurer et consulter les journaux d’audit Azure Database pour MySQL dans le portail Azure

Vous pouvez configurer les [journaux d’audit Azure Database pour MySQL](concepts-audit-logs.md) et les paramètres de diagnostic à partir du portail Azure.

> [!IMPORTANT]
> Pour l’instant, la fonctionnalité Journal d’audit n’existe qu’en préversion.

## <a name="prerequisites"></a>Prérequis

Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :

- [Serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurer l’enregistrement d’audit

Activez et configurez l’enregistrement d’audit.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez votre serveur Azure Database pour MySQL.

1. Dans la section **Paramètres** dans l’encadré, sélectionnez **Paramètres du serveur**.
    ![Paramètres du serveur](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Mettez à jour le paramètre **audit_log_enabled** sur ON.
    ![Activer les journaux d’audit](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Sélectionnez les [types d’événements](concepts-audit-logs.md#configure-audit-logging) à enregistrer en mettant à jour le paramètre **audit_log_events**.
    ![Événements du journal d’audit](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Ajoutez des utilisateurs MySQL à exclure de l’enregistrement en mettant à jour le paramètre **audit_log_exclude_users**. Spécifiez les utilisateurs en fournissant leur nom d’utilisateur MySQL.
    ![Utilisateurs à exclure du journal d’audit](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Une fois que vous avez modifié les paramètres, vous pouvez cliquer sur **Enregistrer**. Vous pouvez également **Abandonner** vos modifications.
    ![Save](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configurer les journaux de diagnostic

1. Dans la section **Supervision** dans la barre latérale, sélectionnez **Paramètres de diagnostic**.

1. Cliquez sur « + Ajouter un paramètre de diagnostic » ![Ajouter un paramètre de diagnostic](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Entrez un nom de paramètre de diagnostic.

1. Spécifiez les récepteurs de données auxquels envoyer les journaux d’audit (compte de stockage, hub d’événements et/ou espace de travail Log Analytics).

1. Sélectionnez « MySqlAuditLogs » comme type de journal.
![Configurer un paramètre de diagnostic](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Une fois que vous avez configuré les récepteurs de données auxquels envoyer les journaux d’audit, vous pouvez cliquer sur **Enregistrer**.
![Enregistrer un paramètre de diagnostic](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Accédez aux journaux d’audit en les explorant dans les récepteurs de données que vous avez configurés. L’affichage des journaux peut prendre jusqu’à 10 minutes.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [journaux d’audit](concepts-audit-logs.md) dans Azure Database pour MySQL
- Découvrir comment configurer les journaux d’audit dans [Azure CLI](howto-configure-audit-logs-cli.md)