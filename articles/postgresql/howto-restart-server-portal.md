---
title: Redémarrer un serveur - Portail Azure - Azure Database pour PostgreSQL - Serveur unique
description: Cet article explique comment redémarrer une instance d'Azure Database pour PostgreSQL - Single Server à l'aide du portail Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 12/20/2020
ms.openlocfilehash: d3dc01ee163b941f450eeac98adfdf1ea0e91190
ms.sourcegitcommit: 0830e02635d2f240aae2667b947487db01f5fdef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2020
ms.locfileid: "97706896"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Redémarrer une instance d'Azure Database pour PostgreSQL - Single Server à l'aide du portail Azure
Cette rubrique explique comment redémarrer un serveur Azure Database pour PostgreSQL. Vous pouvez avoir besoin de redémarrer votre serveur pour des raisons de maintenance, ce qui entraîne une brève interruption de service pendant que le serveur effectue l’opération.

Le redémarrage du serveur est bloqué si le service est occupé. Par exemple, le service peut traiter une opération précédemment demandée, telle que la mise à l’échelle de vCores.
 
> [!NOTE] 
> Le temps nécessaire à un redémarrage varie selon le processus de récupération de PostgreSQL. Pour réduire le délai de redémarrage, nous vous recommandons de diminuer la quantité d’activités se produisant sur le serveur avant le redémarrage. Vous pouvez également augmenter la fréquence des points de contrôle. Vous pouvez également régler les valeurs des paramètres liés aux points de contrôle, y compris `max_wal_size`. Il est également recommandé d’exécuter la commande `CHECKPOINT` avant de redémarrer le serveur.

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Effectuer le redémarrage du serveur

Les étapes suivantes redémarrent le serveur PostgreSQL :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur Azure Database pour PostgreSQL.

2. Dans la barre d’outils de la page **Vue d’ensemble** du serveur, cliquez sur **Redémarrer**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database pour PostgreSQL - Vue d’ensemble - Bouton Redémarrer":::

3. Cliquez sur **Oui** pour confirmer le redémarrage du serveur.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database pour PostgreSQL - Confirmation du redémarrage":::

4. Remarquez que l’état du serveur passe à « Redémarrage en cours ».

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database pour PostgreSQL - État du redémarrage":::

5. Vérifiez que le redémarrage du serveur a réussi.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database pour PostgreSQL - Réussite du redémarrage":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment configurer les paramètres dans Azure Database pour PostgreSQL](howto-configure-server-parameters-using-portal.md)