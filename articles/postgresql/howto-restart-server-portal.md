---
title: Redémarrer un serveur Azure Database pour PostgreSQL avec le portail Azure
description: Cet article explique comment redémarrer un serveur Azure Database pour PostgreSQL à l’aide du portail Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 28e99f64fdee414549c55f9666bfd53f07fb3efb
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892692"
---
# <a name="restart-azure-database-for-postgresql-server-using-azure-portal"></a>Redémarrer un serveur Azure Database pour PostgreSQL avec le portail Azure
Cette rubrique explique comment redémarrer un serveur Azure Database pour PostgreSQL. Vous pouvez avoir besoin de redémarrer votre serveur pour des raisons de maintenance, ce qui entraîne une brève interruption de service pendant que le serveur effectue l’opération.

Le redémarrage du serveur est bloqué si le service est occupé. Par exemple, le service peut traiter une opération précédemment demandée, telle que la mise à l’échelle de vCores.
 
Le temps nécessaire à un redémarrage varie selon le processus de récupération de PostgreSQL. Pour réduire le délai de redémarrage, nous vous recommandons de diminuer la quantité d’activités se produisant sur le serveur avant le redémarrage.

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database pour PostgreSQL et une base de données](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Effectuer le redémarrage du serveur

Les étapes suivantes redémarrent le serveur PostgreSQL :

1. Dans le portail Azure, sélectionnez votre serveur Azure Database pour PostgreSQL.

2. Dans la barre d’outils de la page **Vue d’ensemble** du serveur, cliquez sur **Redémarrer**.

   ![Azure Database pour PostgreSQL - Vue d’ensemble - Bouton Redémarrer](./media/howto-restart-server-portal/2-server.png)

3. Cliquez sur **Oui** pour confirmer le redémarrage du serveur.

   ![Azure Database pour PostgreSQL - Confirmation du redémarrage ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Remarquez que l’état du serveur passe à « Redémarrage en cours ».

   ![Azure Database pour PostgreSQL - État du redémarrage ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Vérifiez que le redémarrage du serveur a réussi.

   ![Azure Database pour PostgreSQL - Réussite du redémarrage ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Créer un serveur Azure Database pour PostgreSQL avec le portail Azure](./quickstart-create-server-database-portal.md)