---
title: Redémarrer un serveur Azure Database pour MySQL à l’aide du portail Azure
description: Cet article explique comment redémarrer un serveur Azure Database pour MySQL à l’aide du portail Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: cd9275e9e112d24d9dbb3d1d5a60c871d7d8ae5c
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890452"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Redémarrer un serveur Azure Database pour MySQL à l’aide du portail Azure
Cette rubrique explique comment redémarrer un serveur Azure Database pour MySQL. Vous pouvez avoir besoin de redémarrer votre serveur pour des raisons de maintenance, ce qui entraîne une brève interruption de service pendant que le serveur effectue l’opération.

Le redémarrage du serveur est bloqué si le service est occupé. Par exemple, le service peut traiter une opération précédemment demandée, telle que la mise à l’échelle de vCores.

Le temps nécessaire à un redémarrage varie selon le processus de récupération de MySQL. Pour réduire le délai de redémarrage, nous vous recommandons de diminuer la quantité d’activités se produisant sur le serveur avant le redémarrage.

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur et une base de données Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Effectuer le redémarrage du serveur

Les étapes suivantes redémarrent le serveur MySQL :

1. Dans le portail Azure, sélectionnez votre serveur Azure Database pour MySQL.

2. Dans la barre d’outils de la page **Vue d’ensemble** du serveur, cliquez sur **Redémarrer**.

   ![Azure Database pour MySQL - Vue d’ensemble - Bouton Redémarrer](./media/howto-restart-server-portal/2-server.png)

3. Cliquez sur **Oui** pour confirmer le redémarrage du serveur.

   ![Azure Database pour MySQL - Confirmation du redémarrage ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Remarquez que l’état du serveur passe à « Redémarrage en cours ».

   ![Azure Database pour MySQL - État du redémarrage ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Vérifiez que le redémarrage du serveur a réussi.

   ![Azure Database pour MySQL - Réussite du redémarrage ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Créer un serveur Azure Database pour MySQL à l'aide du portail Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)