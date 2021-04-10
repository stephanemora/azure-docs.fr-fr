---
title: Arrêt/Démarrage - Portail Azure - Serveur Azure Database pour MySQL
description: Cet article explique comment arrêter/démarrer des opérations dans Azure Database pour MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d297d215d4b0edfdd67b603ba4707bf02057ad78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100516870"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Arrêt/Démarrage d’Azure Database pour MySQL

> [!IMPORTANT]
>  Lorsque vous **arrêtez** le serveur, il reste dans cet état durant les 7 jours suivants. Si vous ne le **démarrez** pas manuellement pendant cette période, le serveur sera automatiquement démarré à la fin des 7 jours. Vous pouvez choisir de l’**arrêter** de nouveau si vous n’utilisez pas le serveur.

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
