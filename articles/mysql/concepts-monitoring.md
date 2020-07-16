---
title: Supervision - Azure Database pour MySQL
description: Cet article décrit les métriques de surveillance et d’alerte pour Azure Database pour MySQL, notamment les statistiques sur l’UC, le stockage et la connexion.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 803437cc45fce9ab850682cf4725c0bf0d21bed6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85414111"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitoring dans Azure Database pour MySQL
La surveillance des données relatives à vos serveurs vous aide à résoudre les problèmes et à optimiser votre charge de travail. Azure Database pour MySQL propose diverses métriques qui fournissent des insights sur le comportement de votre serveur.

## <a name="metrics"></a>Mesures
Toutes les métriques Azure présentent une fréquence d’une minute et chaque métrique fournit 30 jours d’historique. Vous pouvez configurer des alertes basées sur les métriques. Pour des instructions étape par étape, consultez [Configurer des alertes](howto-alert-on-metric.md). Les autres tâches incluent la configuration d’actions automatisées, l’exécution d’analyses avancées et l’archivage de l’historique. Pour plus d’informations, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Liste des métriques
Ces métriques sont disponibles pour Azure Database pour MySQL :

|Métrique|Nom d’affichage de la métrique|Unité|Description|
|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Pourcentage d’UC en cours d’utilisation.|
|memory_percent|Pourcentage de mémoire|Pourcentage|Pourcentage de mémoire en cours d’utilisation.|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Pourcentage d’E/S en cours d’utilisation. (Non applicable pour les serveurs de niveau De base.)|
|storage_percent|Pourcentage de stockage|Pourcentage|Pourcentage de stockage utilisé par rapport à la limite maximale du serveur.|
|storage_used|Stockage utilisé|Octets|Quantité de stockage en cours d’utilisation. Le stockage utilisé par le service peut inclure les fichiers de base de données, les journaux d’activité des transactions et les journaux d’activité du serveur.|
|serverlog_storage_percent|Pourcentage de stockage du journal du serveur|Pourcentage|Pourcentage de stockage du journal du serveur utilisé par rapport au stockage du journal du serveur maximal défini pour le serveur.|
|serverlog_storage_usage|Stockage du journal du serveur utilisé|Octets|Quantité de stockage du journal du serveur en cours d’utilisation.|
|serverlog_storage_limit|Limite de stockage du journal du serveur|Octets|Stockage du journal du serveur maximal défini pour ce serveur.|
|storage_limit|Limite de stockage|Octets|Stockage maximal pour ce serveur.|
|active_connections|Connexions actives|Count|Nombre de connexions actives sur le serveur.|
|connections_failed|Connexions ayant échoué|Count|Nombre de connexions au serveur ayant échoué.|
|seconds_behind_master|Décalage de la réplication en secondes|Count|Nombre de secondes de latence du serveur réplica par rapport au serveur maître.|
|network_bytes_egress|Network Out|Octets|Sortie réseau entre connexions actives.|
|network_bytes_ingress|Network In|Octets|Entrée réseau entre connexions actives.|
|backup_storage_used|Stockage de sauvegarde utilisé|Octets|Quantité de stockage de sauvegarde utilisée.|

## <a name="server-logs"></a>Journaux d’activité du serveur
Vous pouvez activer la journalisation des requêtes lentes et d’audit sur votre serveur. Ces journaux sont également disponibles par le biais des journaux de diagnostic Azure dans les journaux Azure Monitor, les hubs d’événements et le compte de stockage. Pour en savoir plus sur la journalisation, visitez les articles sur les  [journaux d’audit](concepts-audit-logs.md) et les [journaux de requêtes lentes](concepts-server-logs.md).

## <a name="query-store"></a>Magasin des requêtes
Le [Magasin des requêtes](concepts-query-store.md) est une fonctionnalité qui effectue le suivi des performances des requêtes au fil du temps, y compris les statistiques d’exécution des requêtes et les événements d’attente. La fonctionnalité conserve les informations de performances du runtime de requête dans le schéma **mysql**. Vous pouvez contrôler la collecte et le stockage des données au moyen de différents boutons de configuration.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) fonctionne conjointement avec le Magasin des requêtes pour fournir des visualisations accessibles à partir du portail Azure. Ces graphiques vous permettent d’identifier les principales requêtes qui ont un impact sur les performances. Query Performance Insight est accessible dans la section **Performances intelligentes** de la page du portail de votre serveur Azure Database pour MySQL.

## <a name="performance-recommendations"></a>Recommandations sur les performances
La fonctionnalité [Recommandations sur les performances](concepts-performance-recommendations.md) identifie les opportunités d’amélioration des performances des charges de travail. La fonctionnalité Recommandations sur les performances fournit des recommandations pour créer des index susceptibles d’améliorer les performances de vos charges de travail. Pour générer des recommandations d’index, la fonctionnalité prend en compte différentes caractéristiques de la base de données, notamment son schéma et la charge de travail comme indiqué par le Magasin des requêtes. Après avoir implémenté une recommandation sur les performances, les clients doivent tester les performances pour évaluer l’impact des changements.

## <a name="planned-maintenance-notification"></a>Notification de maintenance planifiée

Les **notifications de maintenance planifiée** vous permettent de recevoir des alertes concernant la maintenance planifiée à venir sur Azure Database pour MySQL. Ces notifications sont intégrées à la maintenance planifiée de [Service Health](../service-health/overview.md) et vous permettent d’afficher toutes les tâches de maintenance planifiée de vos abonnements dans un même emplacement. Cela permet également d’adapter la notification au public approprié pour divers groupes de ressources, car des contacts différents peuvent être responsables de différentes ressources. Vous recevez la notification concernant la maintenance à venir 72 heures avant l’événement.

> [!Note]
> Nous mettons tout en œuvre pour fournir une **notification de maintenance planifiée** avec un préavis de 72 heures pour tous les événements. Toutefois, en cas de correctifs critiques ou de sécurité, des notifications peuvent être envoyées dans un délai plus proche de l’événement ou être omises.

### <a name="to-receive-planned-maintenance-notification"></a>Pour recevoir une notification de maintenance planifiée

1. Dans le [portail](https://portal.azure.com), sélectionnez **Intégrité du service**.
2. Dans la section **Alertes**, sélectionnez **Alertes d’intégrité**.
3. Sélectionnez **+ Ajouter une alerte d’intégrité de service** et renseignez les champs.
4. Renseignez les champs obligatoires. 
5. Choisissez le **type d’événement**, puis sélectionnez **Maintenance planifiée** ou **Tout sélectionner**.
6. Dans **Groupes d’actions**, définissez la manière dont vous souhaitez recevoir l’alerte (obtenir un e-mail, déclencher une application logique, etc.).  
7. Vérifiez que l’activation de la règle lors de la création est définie sur Oui.
8. Sélectionnez **Créer une règle d’alerte** pour terminer votre alerte.

Pour obtenir des instructions détaillées sur la création d’**alertes d’intégrité de service**, consultez [Créer des alertes de journal d’activité sur les notifications de service](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> Les notifications de maintenance planifiée sont actuellement disponibles en préversion dans toutes les régions, **sauf** USA Centre-Ouest.

## <a name="next-steps"></a>Étapes suivantes
- Consultez le [guide pratique pour configurer des alertes](howto-alert-on-metric.md) pour savoir comment créer une alerte sur une métrique.
- Pour plus d’informations sur la façon d’accéder aux métriques et de les exporter à l’aide du portail Azure, de l’API REST ou de CLI, consultez [Vue d’ensemble des métriques Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Lisez notre blog sur les [meilleures pratiques de surveillance de votre serveur](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
