---
title: Journaux d’activité de serveur pour Azure Database pour MySQL
description: Décrit les journaux d’activité disponibles dans Azure Database pour MySQL et les paramètres disponibles pour l’activation de différents niveaux de journalisation.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: c5087a038e31c4819ef1ef173bb32faa41e04c97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525841"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Journaux d’activité de serveur dans Azure Database pour MySQL
Dans Azure Database pour MySQL, le journal des requêtes lentes est disponible pour les utilisateurs. L’accès aux journaux des transactions n’est pas pris en charge. Le journal des requêtes lentes peut être utilisé pour identifier les goulots d’étranglement en matière de performances, afin de les faire disparaître. 

Pour plus d’informations sur le journal des requêtes lentes MySQL, consultez la [section sur le journal des requêtes lentes](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) du manuel de référence MySQL.

## <a name="access-server-logs"></a>Accéder aux journaux d’activité du serveur
Vous pouvez répertorier et télécharger les journaux d’activité des serveurs Azure Database pour MySQL à l’aide du portail Azure et de l’interface de ligne de commande Azure.

Dans le portail Azure, sélectionnez votre serveur Azure Database pour MySQL. Sous l’en-tête **Supervision**, sélectionnez la page **Journaux d’activité des serveurs**.

Pour plus d’informations sur l’interface de ligne de commande Azure, consultez [Configuration et accès aux journaux d’activité du serveur à l’aide de la ligne de commande Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Rétention des journaux
Les journaux d’activité sont disponibles pendant sept jours à compter de leur création. Si la taille totale des journaux d’activité disponibles dépasse 7 Go, les fichiers les plus anciens sont supprimés jusqu’à ce que de l’espace soit disponible. 

Une rotation des journaux d’activité s’effectue toutes les 24 heures ou une fois les 7 Go atteints, selon ce qui se produit en premier.


## <a name="configure-logging"></a>Configuration de la journalisation 
Par défaut, le journal des requêtes lentes est désactivé. Pour l’activer, définissez slow_query_log sur ON.

Les autres paramètres que vous pouvez ajuster incluent :

- **long_query_time** : si une requête dure plus longtemps que long_query_time (en secondes), cette requête est enregistrée. La valeur par défaut est 10 secondes.
- **log_slow_admin_statements** : si ce paramètre est activé, inclut des instructions d’administration telles que ALTER_TABLE et ANALYZE_TABLE dans les instructions écrites dans le journal des requêtes lentes.
- **log_queries_not_using_indexes** : détermine si les requêtes qui n’utilisent pas les index sont enregistrées dans le journal des requêtes lentes.
- **log_throttle_queries_not_using_indexes** : Ce paramètre limite le nombre de requêtes hors index qui peuvent être écrites dans le journal des requêtes lentes. Ce paramètre prend effet lorsque log_queries_not_using_indexes est défini sur ON.

Consultez [la documentation MySQL consacrée au journal des requêtes lentes](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) pour obtenir une description complète des paramètres du journal des requêtes lentes.

## <a name="diagnostic-logs"></a>Journaux de diagnostic
Azure Database pour MySQL est intégré aux journaux de diagnostic Azure Monitor. Une fois que vous avez activé les journaux de requêtes lentes sur votre serveur MySQL, vous pouvez choisir pour qu’elles soient émis vers les journaux Azure Monitor, Event Hubs ou stockage Azure. Pour en savoir plus sur l’activation des journaux de diagnostic, consultez la section des procédures de la [documentation des journaux de diagnostic](../azure-monitor/platform/diagnostic-logs-overview.md).

> [!IMPORTANT]
> Cette fonctionnalité de diagnostique pour les journaux de serveur est uniquement disponible dans l’usage général et à mémoire optimisée [niveaux tarifaires](concepts-pricing-tiers.md).

Le tableau suivant décrit ce que contient chaque journal. En fonction de la méthode de sortie, les champs inclus et l’ordre dans lequel ils apparaissent peuvent varier.

| **Propriété** | **Description** |
|---|---|
| `TenantId` | Votre ID d’abonné |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Horodatage du moment où le journal a été enregistré en UTC |
| `Type` | Type de journal. Toujours `AzureDiagnostics` |
| `SubscriptionId` | GUID de l’abonnement auquel appartient le serveur |
| `ResourceGroup` | Nom du groupe de ressources auquel le serveur appartient |
| `ResourceProvider` | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORMYSQL` |
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
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | ID de l’insertion |
| `sql_text_s` | Requête complète |
| `server_id_s` | ID du serveur |
| `thread_id_s` | ID du thread |
| `\_ResourceId` | URI de ressource |

## <a name="next-steps"></a>Étapes suivantes
- [Comment configurer et accéder aux journaux des serveurs à l’aide de l’interface de ligne de commande Azure](howto-configure-server-logs-in-cli.md).
