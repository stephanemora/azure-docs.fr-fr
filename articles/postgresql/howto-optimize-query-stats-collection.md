---
title: Optimiser une collection de statistiques de requête parmi une collection de statistiques de requête sur un serveur Azure Database pour PostgreSQL
description: Cet article explique comment vous pouvez optimiser une collection de statistiques de requête sur un serveur Azure Database pour PostgreSQL.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 076442d85d7f628504cca95c36f3e99f4d0c5117
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966685"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>Optimiser une collection de statistiques de requête sur un serveur Azure Database pour PostgreSQL 
Cet article décrit l’optimisation d’une collection de statistiques de requête sur un serveur Azure Database pour PostgreSQL.

## <a name="using-pgstatsstatements"></a>Utilisation de pg_stats_statements
**Pg_stat_stat_stat_statements** est une extension PostgreSQL qui est activée par défaut dans Azure Database pour PostgreSQL. L’extension fournit un moyen de suivre les statistiques d’exécution de toutes les instructions SQL exécutées par un serveur. Cependant, ce module se connecte à chaque exécution de requête et s’accompagne d’un coût de performance non négligeable. L’activation de **pg_stat_stat_stat_statements** force l’écriture du texte de la requête dans les fichiers sur disque.

Pour les clients qui ont des requêtes uniques avec un texte long ou qui ne surveillent pas activement **pg_stat_stat_stat_statements**, nous recommandons de désactiver **pg_stat_stat_stat_stat_statements** pour de meilleures performances en définissant `pg_stat_statements.track = NONE`.

Sur certaines charges de travail de nos clients, nous avons constaté une amélioration des performances d’environ 50 % en désactivant **pg_stat_statements**. Cependant, le compromis que l’on fait en désactivant pg_stat_stat_statements est l’incapacité à résoudre les problèmes de performances.

Pour définir `pg_stat_statements.track = NONE` :

- Dans le portail Azure, accédez à la [page de gestion des ressources PostgreSQL et sélectionnez le panneau Paramètres du serveur](howto-configure-server-parameters-using-portal.md).

![Panneau des paramètres du serveur PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- À l’aide d’[Azure CLI](howto-configure-server-parameters-using-cli.md), az postgres server configuration set `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="using-query-store"></a>Utilisation du magasin des requêtes 
La fonctionnalité [Magasin des requêtes](concepts-query-store.md)dans Azure Database for PostgreSQL fournit une méthode plus performante pour le suivi des statistiques de requête et elle est recommandée comme alternative à l’utilisation de *pg_stats_statements*. 

## <a name="next-steps"></a>Étapes suivantes
Pensez à régler `pg_stat_statements.track = NONE` dans le [portail Azure](howto-configure-server-parameters-using-portal.md) ou à utiliser [Azure CLI](howto-configure-server-parameters-using-cli.md).

Pour plus d’informations, consultez [Scénarios d’utilisation du magasin de requêtes](concepts-query-store-scenarios.md) et [Meilleures pratiques relatives au magasin de requêtes](concepts-query-store-best-practices.md). 
