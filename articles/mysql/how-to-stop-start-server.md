---
title: Arrêt/Démarrage - Portail Azure - Serveur Azure Database pour MySQL
description: Cet article explique comment arrêter/démarrer des opérations dans Azure Database pour MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: ed784f38c5c297e06c62c2bb7f801a4f0aac0799
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641137"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Arrêt/Démarrage d’Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

> [!IMPORTANT]
> Lorsque vous **arrêtez** le serveur, il reste dans cet état durant les 7 jours suivants. Si vous ne le **démarrez** pas manuellement pendant cette période, le serveur sera automatiquement démarré à la fin des 7 jours. Vous pouvez choisir de l’**arrêter** de nouveau si vous n’utilisez pas le serveur.

Cet article indique la procédure pas-à-pas à suivre pour arrêter et démarrer le serveur unique.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

-   Vous devez disposer d’un serveur unique Azure Database pour MySQL.

> [!NOTE]
> Reportez-vous à la limitation de l’utilisation de la fonctionnalité [Arrêt/Démarrage](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Arrêt/Démarrage d’Azure Database pour MySQL à l’aide du portail Azure

### <a name="stop-a-running-server"></a>Arrêter un serveur en cours d’exécution

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez votre serveur MySQL à arrêter.

2.  Dans la page **Vue d’ensemble**, cliquez sur le bouton **Arrêter** dans la barre d’outils.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Arrêter le serveur Azure Database pour MySQL":::

    > [!NOTE]
    > Une fois que le serveur est arrêté, les autres opérations de gestion ne sont pas disponibles pour le serveur unique.

### <a name="start-a-stopped-server"></a>Démarrer un serveur arrêté

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur unique à démarrer.

2.  Dans la page **Vue d’ensemble**, cliquez sur le bouton **Démarrer** dans la barre d’outils.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Démarrer le serveur Azure Database pour MySQL":::

    > [!NOTE]
    > Une fois que le serveur a démarré, toutes les opérations de gestion sont disponibles pour le serveur unique.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Arrêt/Démarrage d’Azure Database pour MySQL à l’aide de l’interface CLI

### <a name="stop-a-running-server"></a>Arrêter un serveur en cours d’exécution

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez votre serveur MySQL à arrêter.

2.  Dans la page **Vue d’ensemble**, cliquez sur le bouton **Arrêter** dans la barre d’outils.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Une fois que le serveur est arrêté, les autres opérations de gestion ne sont pas disponibles pour le serveur unique.

### <a name="start-a-stopped-server"></a>Démarrer un serveur arrêté

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur unique à démarrer.

2.  Dans la page **Vue d’ensemble**, cliquez sur le bouton **Démarrer** dans la barre d’outils.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Une fois que le serveur a démarré, toutes les opérations de gestion sont disponibles pour le serveur unique.

## <a name="next-steps"></a>Étapes suivantes
Découvrez [comment créer des alertes sur des métriques](howto-alert-on-metric.md).
