---
title: Optimiser une collection de statistiques de requête sur un serveur Azure Database pour PostgreSQL
description: Cet article explique comment vous pouvez optimiser une collection de statistiques de requête sur un serveur Azure Database pour PostgreSQL.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 10/25/2018
ms.date: 02/18/2019
ms.openlocfilehash: 428a22f79008130448bef65f14322d6880b1b367
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422699"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql-server"></a>Optimiser une collection de statistiques de requête sur un serveur Azure Database pour PostgreSQL 
Cet article décrit la procédure d’optimisation d’une collection de statistiques de requête sur un serveur Azure Database pour PostgreSQL.

## <a name="use-pgstatsstatements"></a>Utiliser pg_stats_statements
**Pg_stat_stat_stat_statements** est une extension PostgreSQL qui est activée par défaut dans Azure Database pour PostgreSQL. L’extension fournit un moyen de suivre les statistiques d’exécution pour toutes les instructions SQL exécutées par un serveur. Ce module se connecte à chaque exécution de requête et s’accompagne d’un coût de performance non négligeable. L’activation de **pg_stat_stat_stat_statements** force l’écriture du texte de la requête dans les fichiers sur disque.

Si vous disposez de requêtes uniques avec un texte long ou si vous ne surveillez pas activement **pg_stat_statements**, désactivez **pg_stat_statements** pour de meilleures performances. Pour ce faire, définissez le paramètre sur `pg_stat_statements.track = NONE`.

Pour certaines charges de travail de nos clients, nous avons constaté une amélioration des performances d’environ 50 % lorsque **pg_stat_statements** est désactivé. Le compromis que vous faites lorsque vous désactivez pg_stat_stat_statements est dû à l’incapacité à résoudre les problèmes de performances.

Pour définir `pg_stat_statements.track = NONE` :

- Dans le portail Azure, accédez à la [page de gestion des ressources PostgreSQL et sélectionnez le panneau Paramètres du serveur](howto-configure-server-parameters-using-portal.md).

  ![Panneau des paramètres du serveur PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Utilisez l’[interface de ligne de commande Azure](howto-configure-server-parameters-using-cli.md), az postgres server configuration set sur `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Utiliser le magasin de données des requêtes 
La fonctionnalité [Magasin des requêtes](concepts-query-store.md) dans Azure Database pour PostgreSQL fournit un moyen plus efficace de suivre les performances des requêtes. Nous recommandons cette fonctionnalité comme alternative à l’utilisation de *pg_stats_statements*. 

## <a name="next-steps"></a>Étapes suivantes
Pensez à régler `pg_stat_statements.track = NONE` dans le [portail Azure](howto-configure-server-parameters-using-portal.md) ou à utiliser l’[interface de ligne de commande Azure](howto-configure-server-parameters-using-cli.md).

Pour plus d'informations, consultez les pages suivantes : 
- [Scénarios d’utilisation du magasin de requêtes](concepts-query-store-scenarios.md) 
- [Meilleures pratiques relatives au magasin de données des requêtes](concepts-query-store-best-practices.md) 
