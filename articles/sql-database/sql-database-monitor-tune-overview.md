---
title: Surveillance et optimisation des performances
description: Vue d’ensemble des fonctionnalités de surveillance et d’optimisation des performances et de la méthodologie dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79232469"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Surveillance et optimisation des performances dans Azure SQL Database

Pour surveiller les performances d’une base de données dans Azure SQL Database, commencez par surveiller les ressources processeur et d’E/S utilisées par votre charge de travail par rapport au niveau de performances de base de données choisi lors de la sélection d’un niveau de service et d’un niveau de performances spécifiques. Pour cela, Azure SQL Database émet des métriques de ressources qui peuvent être affichées dans le portail Azure ou à l’aide d’un des outils de gestion SQL suivants : [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Pour les bases de données uniques et mises en pool, Azure SQL Database fournit un certain nombre de conseillers en base de données pour fournir des recommandations intelligentes sur l’optimisation des performances et des options d’optimisation automatique afin d’améliorer les performances. En outre, Query Performance Insight affiche des détails sur les requêtes responsables de l’utilisation la plus intensive du processeur et des E/S pour les bases de données uniques et mises en pools.

Azure SQL Database fournit des fonctionnalités de surveillance et d’optimisation supplémentaires grâce à l’intelligence artificielle, pour vous aider à résoudre les problèmes et à optimiser les performances de vos bases de données et solutions. Vous pouvez choisir de configurer l’[exportation en streaming](sql-database-metrics-diag-logging.md) de ces journaux [Intelligent Insights](sql-database-intelligent-insights.md) et autres journaux de ressources et métriques SQL Database vers l’une des différentes destinations à des fins de consommation et d’analyse, en particulier à l’aide de [SQL Analytics](../azure-monitor/insights/azure-sql.md). Azure SQL Analytics est une solution cloud avancée permettant de superviser dans un seul affichage les performances de toutes vos bases de données Azure SQL à grande échelle et sur plusieurs abonnements. Pour obtenir la liste des journaux et des métriques que vous pouvez exporter, consultez [Télémétrie de diagnostic pour l’exportation](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Enfin, SQL possède ses propres capacités de surveillance et de diagnostic avec [SQL Server Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) et les [vues de gestion dynamique (DMV)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Consultez [Surveillance avec des DMV](sql-database-monitoring-with-dmvs.md) pour des scripts afin de surveiller un large éventail de problèmes de performances.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Capacités de surveillance et d’optimisation dans le portail Azure

Dans le portail Azure, Azure SQL Database permet une surveillance des métriques de ressources pour tous les types de déploiement. Pour les bases de données uniques et mises en pool, les conseillers en base de données et les Query Performance Insight fournissent des recommandations d’optimisation et une analyse des performances des requêtes. Enfin, dans le portail Azure, vous pouvez automatiser ces options pour les serveurs logiques et leurs bases de données uniques et mises en pool.

### <a name="sql-database-resource-monitoring"></a>Surveillance des ressources SQL Database

Vous pouvez surveiller rapidement les métriques de ressources suivantes dans la vue **Métriques** du portail Azure :

- **Utilisation du DTU**

  Vérifiez si une base de données ou un pool élastique utilise 100 % du DTU sur une longue période. Une utilisation élevée du DTU indique que votre charge de travail peut nécessiter plus de ressources de processeur ou d’E/S. Cela peut également indiquer que certaines requêtes doivent être optimisées.
- **Utilisation du processeur**

  Vérifiez si une base de données, un pool élastique ou instance gérée utilise 100 % du processeur sur une longue période. Une utilisation élevée du processeur indique que votre charge de travail peut nécessiter plus de ressources de processeur ou d’E/S. Cela peut également indiquer que certaines requêtes doivent être optimisées.
- **Utilisation d’E/S**

  Vérifiez si une base de données, un pool élastique ou une instance gérée atteint les limites d’E/S du stockage sous-jacent. Une utilisation élevée d’E/S indique que votre charge de travail peut nécessiter plus de ressources de processeur ou d’E/S. Cela peut également indiquer que certaines requêtes doivent être optimisées.

  ![Mesures de ressources](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Conseillers en base de données
Azure SQL Database inclut des [conseillers en base de données](sql-database-advisor.md) qui fournissent des recommandations d’optimisation des performances pour les bases de données uniques et mises en pool. Ces recommandations sont disponibles dans le portail Azure et dans [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Vous pouvez également activer l’[optimisation automatique](sql-database-automatic-tuning.md) afin que SQL Database implémente automatiquement ces recommandations d’optimisation.

### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) affiche dans le portail Azure les performances des requêtes qui consomment le plus de ressources et dont l’exécution et la plus longue pour les bases de données uniques et mises en pool.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Générer des évaluations intelligentes des problèmes de performances

Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) utilise l’intelligence intégrée pour surveiller en permanence l’utilisation de la base de données et détecter les événements perturbateurs entraînant des performances médiocres. Intelligent Insights détecte automatiquement les problèmes de performances liés aux bases de données dans Azure SQL Database en fonction des temps d’attente d’exécution des requêtes, des erreurs ou des expirations de délais d’attente. Une fois un tel événement détecté, une analyse détaillée est effectuée, qui génère un journal de ressources (appelé SQLInsights) avec une [évaluation intelligente des problèmes](sql-database-intelligent-insights-troubleshoot-performance.md). Cette évaluation se compose d’une analyse de cause racine du problème de performances de la base de données et, dans la mesure du possible, de recommandations pour une amélioration des performances.

Intelligent Insights est une fonctionnalité unique de l’intelligence intégrée Azure, qui apporte les avantages suivants :

- Surveillance proactive
- Analyse des performances personnalisée
- Détection anticipée de la dégradation des performances de la base de données
- Analyse de la cause racine des problèmes détectés
- Recommandations pour l’amélioration des performances
- Capacité de scale-out sur des centaines de milliers de bases de données
- Impact positif sur les ressources de DevOps et le coût total de possession

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Activer l’exportation en streaming des journaux de métriques et de ressources

Vous pouvez activer et configurer l’[exportation en streaming](sql-database-metrics-diag-logging.md) de la télémétrie de diagnostic vers l’une des différentes destinations, notamment le journal de ressources Intelligent Insights. Utilisez [SQL Analytics](../azure-monitor/insights/azure-sql.md) et d’autres capacités pour utiliser cette télémétrie de diagnostic supplémentaire afin d’identifier et de résoudre les problèmes de performances.

Vous configurez les paramètres de diagnostic pour diffuser en continu des catégories de métriques et des journaux de ressources pour des bases de données individuelles, des bases de données mises en pool, des pools élastiques, des instances gérées et des bases de données d'instance vers l'une des ressources Azure suivantes.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Espace de travail Log Analytics dans Azure Monitor

Vous pouvez diffuser en continu des journaux de métriques et de ressources vers un [espace de travail Log Analytics dans Azure Monitor](../azure-monitor/platform/resource-logs-collect-workspace.md). Les données diffusées en continu ici peuvent être utilisées par [SQL Analytics](../azure-monitor/insights/azure-sql.md), une solution de surveillance cloud uniquement permettant une surveillance intelligente de vos bases de données, avec des rapports de performances, des alertes et des recommandations d’atténuation. Les données diffusées en continu vers un espace de travail Log Analytics peuvent être analysées avec d’autres données de surveillance collectées, ce qui vous permet également d’exploiter d’autres fonctionnalités Azure Monitor, notamment les alertes et les visualisations.

### <a name="azure-event-hubs"></a>Hubs d'événements Azure

Vous pouvez diffuser en continu des journaux de métriques et de ressources vers [Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md). Diffusez en continu des données télémétriques vers des hubs d’événements pour fournir les fonctionnalités suivantes :

- **Streaming des journaux vers des systèmes de journalisation et de données télémétriques tiers**

  Streaming de tous vos journaux de métriques et de ressources vers un hub d’événements unique pour envoyer les données de journal d’activité vers un outil tiers SIEM ou Log Analytics.
- **Créer une plateforme de journalisation et de télémétrie personnalisée**

  La nature hautement évolutive de l’abonnement aux hubs d’événements vous permet d’ingérer de manière flexible les journaux de métriques et de ressources dans une plateforme de télémétrie personnalisée. Pour plus d’informations, consultez [Conception et dimensionnement d’une plateforme de télémétrie à échelle mondiale sur Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).
- **Afficher l’intégrité du service en procédant au streaming des données de vers Power BI**

  Utilisez Event Hubs, Stream Analytics et Power BI pour transformer vos données de diagnostic en informations en temps réel sur vos services Azure. Consultez [Stream Analytics et Power BI : tableau de bord d’analyse en temps réel pour les données de streaming](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) pour plus de détails sur cette solution.

### <a name="azure-storage"></a>Stockage Azure

Diffusez en continu des journaux de métriques et de ressources vers le [Stockage Azure](../azure-monitor/platform/resource-logs-collect-storage.md). Utilisez le Stockage Azure pour archiver de vastes quantités de données télémétriques de diagnostics pour une fraction du coût des deux options de streaming précédentes.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Utiliser les événements étendus dans le moteur de base de données SQL

En outre, vous pouvez utiliser des [événements étendus](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) dans SQL pour une surveillance et un dépannage avancés supplémentaires. L’architecture des événements étendus permet aux utilisateurs de collecter autant de données que nécessaire pour dépanner ou identifier un problème de performances. Pour plus d’informations sur l’utilisation d’événements étendus dans SQL Database, consultez [Événements étendus dans SQL Database](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les recommandations relatives aux performances intelligentes pour les bases de données uniques et mises en pool, consultez [Recommandations relatives aux performances de Database Advisor](sql-database-advisor.md).
- Pour plus d’informations sur l’analyse automatique des performances de base de données à l’aide des diagnostics automatisés et de l’analyse de la cause racine des problèmes de performances, consultez l’article [Intelligent Insights](sql-database-intelligent-insights.md).
'''''''''