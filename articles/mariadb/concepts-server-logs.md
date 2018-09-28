---
title: Journaux de serveur pour Azure Database for MariaDB
description: Décrit les journaux disponibles dans Azure Database for MariaDB et les paramètres disponibles pour l’activation de différents niveaux de journalisation.
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d982af64517ed452d907f62a39e975d472951392
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992981"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Journaux de serveur dans Azure Database for MariaDB
Dans Azure Database for MariaDB, le journal des requêtes lentes est accessible aux utilisateurs. L’accès aux journaux des transactions n’est pas pris en charge. Le journal des requêtes lentes peut être utilisé pour identifier les goulots d’étranglement en matière de performances, afin de les faire disparaître.

Pour plus d’informations sur le journal des requêtes lentes, consultez la documentation MariaDB relative aux [journal des requêtes lentes](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Accéder aux journaux du serveur
Vous pouvez répertorier et télécharger les journaux des serveurs Azure Database for MariaDB à l’aide du portail Azure et de l’interface Azure CLI.

Dans le portail Azure, sélectionnez votre serveur Azure Database for MariaDB. Sous l’en-tête **Surveillance**, sélectionnez la page **Journaux des serveurs**.

<!-- For more information on Azure CLI, see [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md).-->

## <a name="log-retention"></a>Rétention des journaux
Les journaux sont disponibles pendant sept jours à compter de leur création. Si la taille totale des journaux disponibles dépasse 7 Go, les fichiers les plus anciens sont supprimés jusqu’à ce que de l’espace soit disponible.

Une rotation des journaux s’effectue toutes les 24 heures ou une fois les 7 Go atteints, selon ce qui se produit en premier.

## <a name="configure-logging"></a>Configuration de la journalisation
Par défaut, le journal des requêtes lentes est désactivé. Pour l’activer, définissez slow_query_log sur ON.

Les autres paramètres que vous pouvez ajuster incluent :

- **long_query_time** : si une requête dure plus longtemps que long_query_time (en secondes), cette requête est enregistrée. La valeur par défaut est 10 secondes.
- **log_slow_admin_statements** : si ce paramètre est activé, inclut des instructions d’administration telles que ALTER_TABLE et ANALYZE_TABLE dans les instructions écrites dans le journal des requêtes lentes.
- **log_queries_not_using_indexes** : détermine si les requêtes qui n’utilisent pas les index sont enregistrées dans le journal des requêtes lentes.
- **log_throttle_queries_not_using_indexes** : ce paramètre limite le nombre de requêtes hors index qui peuvent être écrites dans le journal des requêtes lentes. Ce paramètre prend effet lorsque log_queries_not_using_indexes est défini sur ON.

Consultez la [documentation MariaDB consacrée au journal des requêtes lentes](https://mariadb.com/kb/en/library/slow-query-log-overview/) pour obtenir une description complète des paramètres du journal des requêtes lentes.

## <a name="next-steps"></a>Étapes suivantes
- [Guide pratique pour configurer les journaux des serveurs et y accéder à partir du portail Azure](howto-configure-server-logs-portal.md).
