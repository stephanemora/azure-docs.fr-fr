---
title: Superviser et optimiser Azure Database pour PostgreSQL
description: Cet article décrit les fonctionnalités de supervision et d’optimisation disponibles dans Azure Database pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 0d5508fe6b07bd5664bd0708960559537d8f6be1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456221"
---
# <a name="monitor-and-tune"></a>Surveiller et régler
La surveillance des données relatives à vos serveurs vous aide à résoudre les problèmes et à optimiser votre charge de travail. Azure Database pour PostgreSQL propose différentes options de supervision pour fournir un insight sur le comportement de votre serveur.

## <a name="metrics"></a>Mesures
Azure Database pour PostgreSQL propose diverses métriques qui donnent des informations sur le comportement des ressources prenant en charge le serveur MySQL. Chaque métrique est émise selon une fréquence d’une minute et est conservée jusqu’à 30 jours dans l’historique. Vous pouvez configurer des alertes basées sur les métriques. Pour obtenir des instructions détaillées, consultez [Configurer des alertes sur les métriques](howto-alert-on-metric.md). Les autres tâches incluent la configuration d’actions automatisées, l’exécution d’analyses avancées et l’archivage de l’historique. Pour plus d’informations, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Liste des métriques
Les métriques suivantes sont disponibles pour Azure Database pour PostgreSQL :

|Métrique|Nom d’affichage de la métrique|Unité|Description|
|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Pourcentage d’UC en cours d’utilisation.|
|memory_percent|Pourcentage de mémoire|Pourcentage|Pourcentage de mémoire en cours d’utilisation.|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Pourcentage d’E/S en cours d’utilisation.|
|storage_percent|Pourcentage de stockage|Pourcentage|Pourcentage de stockage utilisé par rapport à la limite maximale du serveur.|
|storage_used|Stockage utilisé|Octets|Quantité de stockage en cours d’utilisation. Le stockage utilisé par le service peut inclure les fichiers de base de données, les journaux d’activité des transactions et les journaux d’activité du serveur.|
|storage_limit|Limite de stockage|Octets|Stockage maximal pour ce serveur.|
|serverlog_storage_percent|Pourcentage de stockage du journal du serveur|Pourcentage|Pourcentage de stockage du journal du serveur utilisé par rapport au stockage du journal du serveur maximal défini pour le serveur.|
|serverlog_storage_usage|Stockage du journal du serveur utilisé|Octets|Quantité de stockage du journal du serveur en cours d’utilisation.|
|serverlog_storage_limit|Limite de stockage du journal du serveur|Octets|Stockage du journal du serveur maximal défini pour ce serveur.|
|active_connections|Connexions actives|Nombre|Nombre de connexions actives sur le serveur.|
|connections_failed|Connexions ayant échoué|Nombre|Nombre de connexions au serveur ayant échoué.|
|network_bytes_egress|Network Out|Octets|Sortie réseau entre connexions actives.|
|network_bytes_ingress|Network In|Octets|Entrée réseau entre connexions actives.|
|backup_storage_used|Stockage de sauvegarde utilisé|Octets|Quantité de stockage de sauvegarde utilisée.|

## <a name="server-logs"></a>Journaux d’activité du serveur
Vous pouvez activer la journalisation sur votre serveur. Ces journaux sont également disponibles via les journaux de Diagnostic Azure dans [Azure Monitor enregistre](../azure-monitor/log-query/log-query-overview.md), Event Hubs et compte de stockage. Pour en savoir plus sur la journalisation, consultez la page [journaux d’activité serveur](concepts-server-logs.md).

## <a name="query-store"></a>Magasin de requêtes
[Magasin des requêtes](concepts-query-store.md) est une fonctionnalité en préversion publique qui effectue le suivi des performances des requêtes au fil du temps, y compris les statistiques d’exécution des requêtes et les événements d’attente. Cette fonctionnalité conserve les informations sur les performances d’exécution des requêtes dans une base de données système nommée **azure_sys** sous le schéma query_store. Vous pouvez contrôler la collecte et le stockage des données au moyen de différents boutons de configuration.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) fonctionne conjointement avec le Magasin des requêtes pour fournir des visualisations accessibles à partir du portail Azure. Ces graphiques vous permettent d’identifier les principales requêtes qui ont un impact sur les performances. Query Performance Insight est disponible en préversion publique et est accessible dans la section **Support + dépannage** dans la page du portail de votre serveur Azure Database pour PostgreSQL.

## <a name="performance-recommendations"></a>Recommandations sur les performances
La fonctionnalité [Recommandations sur les performances](concepts-performance-recommendations.md) identifie les opportunités d’amélioration des performances des charges de travail. La préversion publique de la fonctionnalité Recommandations sur les performances fournit des recommandations pour créer des index susceptibles d’améliorer les performances de vos charges de travail. Pour générer des recommandations d’index, la fonctionnalité prend en compte différentes caractéristiques de la base de données, notamment son schéma et la charge de travail comme indiqué par le Magasin des requêtes. Après avoir implémenté une recommandation sur les performances, les clients doivent tester les performances pour évaluer l’impact des changements. 

## <a name="next-steps"></a>Étapes suivantes
- Consultez le [guide pratique pour configurer des alertes](howto-alert-on-metric.md) pour savoir comment créer une alerte sur une métrique.
- Pour plus d’informations sur la façon d’accéder aux métriques et de les exporter à l’aide du portail Azure, de l’API REST ou de CLI, consultez [Vue d’ensemble des métriques Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Lisez notre blog sur les [meilleures pratiques de surveillance de votre serveur](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
