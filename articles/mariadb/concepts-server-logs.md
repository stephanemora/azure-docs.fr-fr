---
title: Journaux des requêtes lentes - Azure Database for MariaDB
description: Décrit les journaux d’activité disponibles dans Azure Database for MariaDB et les paramètres disponibles pour l’activation de différents niveaux de journalisation.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 8a451b06c8166b48fd892050e53204e2b65856c3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772102"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Journaux des requêtes lentes dans Azure Database for MariaDB
Dans Azure Database for MariaDB, le journal des requêtes lentes est accessible aux utilisateurs. L’accès aux journaux des transactions n’est pas pris en charge. Le journal des requêtes lentes peut être utilisé pour identifier les goulots d’étranglement en matière de performances, afin de les faire disparaître.

Pour plus d’informations sur le journal des requêtes lentes, consultez la documentation MariaDB relative aux [journal des requêtes lentes](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-slow-query-logs"></a>Accéder aux journaux des requêtes lentes
Vous pouvez lister et télécharger les journaux des requêtes lentes Azure Database for MariaDB à l’aide du portail Azure et de l’interface Azure CLI.

Dans le portail Azure, sélectionnez votre serveur Azure Database for MariaDB. Sous l’en-tête **Supervision**, sélectionnez la page **Journaux d’activité des serveurs**.

Pour plus d’informations sur l’interface de ligne de commande Azure, consultez [Configuration et accès aux journaux d’activité du serveur à l’aide de la ligne de commande Azure](howto-configure-server-logs-cli.md).

## <a name="log-retention"></a>Rétention des journaux
Les journaux d’activité sont disponibles pendant sept jours à compter de leur création. Si la taille totale des journaux d’activité disponibles dépasse 7 Go, les fichiers les plus anciens sont supprimés jusqu’à ce que de l’espace soit disponible.

Une rotation des journaux d’activité s’effectue toutes les 24 heures ou une fois les 7 Go atteints, selon ce qui se produit en premier.

## <a name="configure-slow-query-logging"></a>Configurer la journalisation des requêtes lentes
Par défaut, le journal des requêtes lentes est désactivé. Pour l’activer, définissez slow_query_log sur ON.

Les autres paramètres que vous pouvez ajuster incluent :

- **long_query_time** : si une requête dure plus longtemps que long_query_time (en secondes), cette requête est enregistrée. La valeur par défaut est 10 secondes.
- **log_slow_admin_statements** : si ce paramètre est activé, inclut des instructions d’administration telles que ALTER_TABLE et ANALYZE_TABLE dans les instructions écrites dans le journal des requêtes lentes.
- **log_queries_not_using_indexes** : détermine si les requêtes qui n’utilisent pas les index sont enregistrées dans le journal des requêtes lentes.
- **log_throttle_queries_not_using_indexes** : Ce paramètre limite le nombre de requêtes hors index qui peuvent être écrites dans le journal des requêtes lentes. Ce paramètre prend effet lorsque log_queries_not_using_indexes est défini sur ON.

Consultez la [documentation MariaDB consacrée au journal des requêtes lentes](https://mariadb.com/kb/en/library/slow-query-log-overview/) pour obtenir une description complète des paramètres du journal des requêtes lentes.

## <a name="diagnostic-logs"></a>Journaux de diagnostic
Azure Database for MariaDB est intégré aux journaux de diagnostic Azure Monitor. Une fois que vous avez activé les journaux des requêtes lentes sur votre serveur MariaDB, vous pouvez choisir qu’ils soient transmis vers des journaux Azure Monitor, des hubs d’événements et le Stockage Azure. Pour en savoir plus sur l’activation des journaux de diagnostic, consultez la section des procédures de la [documentation des journaux de diagnostic](../azure-monitor/platform/resource-logs-overview.md).

> [!IMPORTANT]
> Cette fonctionnalité de diagnostic des journaux de serveur n’est disponible que dans les [niveaux tarifaires](concepts-pricing-tiers.md) Usage général et Mémoire optimisée.

Le tableau suivant décrit ce que contient chaque journal. En fonction de la méthode de sortie, les champs inclus et l’ordre dans lequel ils apparaissent peuvent varier.

| **Propriété** | **Description** |
|---|---|
| `TenantId` | Votre ID d’abonné |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Horodatage du moment où le journal a été enregistré en UTC |
| `Type` | Type de journal. Toujours `AzureDiagnostics` |
| `SubscriptionId` | GUID de l’abonnement auquel appartient le serveur |
| `ResourceGroup` | Nom du groupe de ressources auquel le serveur appartient |
| `ResourceProvider` | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de ressource |
| `Resource` | Nom du serveur |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nom du serveur |
| `start_time_t` [UTC] | Heure de début de la requête |
| `query_time_s` | Durée totale d’exécution de la requête |
| `lock_time_s` | Durée totale pendant laquelle la requête a été verrouillée |
| `user_host_s` | Nom d’utilisateur |
| `rows_sent_s` | Nombre de lignes envoyées |
| `rows_examined_s` | Nombre de lignes examinées |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | ID de l’insertion |
| `sql_text_s` | Requête complète |
| `server_id_s` | ID du serveur |
| `thread_id_s` | ID du thread |
| `\_ResourceId` | URI de ressource |

## <a name="next-steps"></a>Étapes suivantes
- [Guide pratique pour configurer les journaux d’activité des serveurs et y accéder à partir du portail Azure](howto-configure-server-logs-portal.md).
