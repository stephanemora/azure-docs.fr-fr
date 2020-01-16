---
title: Journaux des requêtes lentes - Azure Database pour MySQL
description: Décrit les journaux des requêtes lentes disponibles dans Azure Database pour MySQL et les paramètres disponibles pour l’activation de différents niveaux de journalisation.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 9b661a7fa6a7b9f079a3b24d1b83f27118c4bd23
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745857"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Journaux des requêtes lentes dans Azure Database for MySQL
Dans Azure Database pour MySQL, le journal des requêtes lentes est disponible pour les utilisateurs. L’accès aux journaux des transactions n’est pas pris en charge. Le journal des requêtes lentes peut être utilisé pour identifier les goulots d’étranglement en matière de performances, afin de les faire disparaître.

Pour plus d’informations sur le journal des requêtes lentes MySQL, consultez la [section sur le journal des requêtes lentes](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) du manuel de référence MySQL.

## <a name="access-slow-query-logs"></a>Accéder aux journaux des requêtes lentes
Vous pouvez lister et télécharger les journaux des requêtes lentes Azure Database for MySQL à l’aide du portail Azure et de l’interface Azure CLI.

Dans le portail Azure, sélectionnez votre serveur Azure Database pour MySQL. Sous l’en-tête **Supervision**, sélectionnez la page **Journaux d’activité des serveurs**.

Pour plus d’informations sur Azure CLI, consultez [Configurer et accéder aux journaux des requêtes lentes à l’aide d’Azure CLI](howto-configure-server-logs-in-cli.md).

De même, vous pouvez diriger les journaux vers Azure Monitor à l’aide des journaux de diagnostic. Pour plus d’informations, voir [plus bas](concepts-server-logs.md#diagnostic-logs).

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
- **log_output** : si défini sur « File », permet au journal des requêtes lentes d’être écrit dans le stockage du serveur local et dans les journaux de diagnostic Azure Monitor. S’il est défini sur « None », le journal des requêtes lentes est uniquement écrit dans les journaux de diagnostics Azure Monitor. 

> [!Note]
> Pour `sql_text`, le journal est tronqué s’il dépasse 2 048 caractères.

Consultez [la documentation MySQL consacrée au journal des requêtes lentes](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) pour obtenir une description complète des paramètres du journal des requêtes lentes.

## <a name="diagnostic-logs"></a>Journaux de diagnostic
Azure Database pour MySQL est intégré aux journaux de diagnostic Azure Monitor. Une fois que vous avez activé les journaux des requêtes lentes sur votre serveur MySQL, vous pouvez choisir qu’ils soient transmis vers des journaux Azure Monitor, Event Hubs ou le Stockage Azure. Pour en savoir plus sur l’activation des journaux de diagnostic, consultez la section des procédures de la [documentation des journaux de diagnostic](../azure-monitor/platform/platform-logs-overview.md).

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
| `ResourceProvider` | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de ressource |
| `Resource` | Nom du serveur |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nom du serveur |
| `start_time_t` [UTC] | Heure de début de la requête |
| `query_time_s` | Durée totale d’exécution de la requête en secondes |
| `lock_time_s` | Durée totale en secondes pendant laquelle la requête a été verrouillée |
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
- [Comment configurer et accéder aux journaux d’activité des serveurs à l’aide de l’interface de ligne de commande Azure](howto-configure-server-logs-in-cli.md).
