---
title: Arrêt/Démarrage - Portail Azure - Serveur Azure Database pour MySQL
description: Cet article explique comment arrêter/démarrer des opérations dans Azure Database pour MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 95be6aa576d9d059ce419443f8c7e32af5ff397a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826206"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Arrêt/Démarrage d’Azure Database pour MySQL

> [!IMPORTANT]
> La fonctionnalité Arrêt/Démarrage d’Azure Database pour MySQL est actuellement en préversion publique.

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

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Arrêter le serveur Azure Database pour MySQL":::

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
