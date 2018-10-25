---
title: Surveillance et optimisation des performances - Azure SQL Database | Microsoft Docs
description: Conseils pour le réglage des performances dans Azure SQL Database par le biais de l’évaluation et de l’amélioration.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/16/2018
ms.openlocfilehash: 5ef15b7a757b87c14bf0bd764bdd6ca6e6da64e0
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379034"
---
# <a name="monitoring-and-performance-tuning"></a>Surveillance et optimisation des performances

Azure SQL Database est un service de données flexible et géré automatiquement dans lequel vous pouvez facilement superviser l’utilisation, ajouter ou supprimer des ressources (processeur, mémoire, E/S), obtenir des recommandations susceptibles d’améliorer les performances de votre base de données, ou laisser la base de données s’adapter à votre charge de travail et optimiser automatiquement les performances.

## <a name="the-state-of-an-active-query"></a>État d’une requête active

Pour améliorer les performances d’Azure SQL Database, il faut comprendre que chaque demande de requête active effectuée depuis votre application est dans un état En cours d’exécution ou En attente. Lors du dépannage d’un problème de performances dans Azure SQL Database, gardez à l’esprit le graphique suivant :

![États de la charge de travail](./media/sql-database-monitor-tune-overview/workload-states.png)

Pour une charge de travail, les problèmes de performances peuvent être dus à une contention de l’UC (une condition **liée à l’exécution**) ou à des requêtes individuelles en attente de quelque chose (une condition **liée à l’attente**).

- **Utilisation excessive de l’UC dans votre base de données SQL Azure** :

  Vous pouvez constater une utilisation excessive de l’UC provoquant des problèmes de performances dans les conditions suivantes :

  - Trop de requêtes en cours d’exécution
  - Trop de requêtes en cours de compilation
  - Une ou plusieurs requêtes en cours d’exécution utilisent un plan de requête qui n’est pas le plan optimal

  Si c’est le cas pour votre charge de travail, votre objectif est d’identifier et d’optimiser les requêtes associées, ou bien de mettre à niveau la taille de la capacité de calcul ou du niveau de service de façon à augmenter la capacité de votre base de données SQL Azure pour absorber les besoins en ressources d’UC. Pour plus d’informations sur la mise à l’échelle des ressources pour les bases de données uniques, consultez [Mettre à l’échelle des ressources de base de données unique dans Azure SQL Database](sql-database-single-database-scale.md) et, pour la mise à l’échelle des ressources pour les pools élastiques, consultez [Mettre à l’échelle des ressources de pool élastique dans Azure SQL Database](sql-database-elastic-pool-scale.md).

- **Une requête individuelle est en attente de quelque chose**

  Une requête individuelle peut avoir des problèmes de performances quand elle est en attente de quelque chose. Dans ce scénario, votre objectif est de supprimer ou de réduire le temps d’attente.

### <a name="determine-if-you-have-a-running-related-performance-issue"></a>Déterminer si vous avez un problème de performances lié à l’exécution

Vous pouvez identifier les problèmes de performances liés à l’exécution selon plusieurs méthodes. Les méthodes les plus courantes sont :

- Utilisez le [portail Azure](#monitor-databases-using-the-azure-portal) pour surveiller le pourcentage d’utilisation de l’UC.
- Utilisez les [vues de gestion dynamique](sql-database-monitoring-with-dmvs.md) suivantes :

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retourne la consommation d’UC, d’E/S et de mémoire pour une base de données Azure SQL Database. Il existe une ligne pour toutes les 15 secondes, même s’il n’existe aucune activité dans la base de données. L’historique des données est conservé pendant une heure.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retourne les données d’utilisation de l’UC et les données sur le stockage pour une base de données Azure SQL Database. Les données sont collectées et agrégées par intervalles de cinq minutes.

> [!TIP]
> En règle générale, si votre utilisation de l’UC est constamment d’au moins 80 %, vous avez un problème de performances lié à l’exécution.

### <a name="determine-if-you-have-a-waiting-related-performance-issue"></a>Déterminer si vous avez un problème de performances lié à l’attente

Vérifiez d’abord qu’il ne s’agit pas d’un problème de performances lié à l’exécution, avec une utilisation élevée de l’UC. Si ce n’est pas le cas, l’étape suivante consiste à identifier les attentes les plus longues associées à la charge de travail de votre application.  Méthodes courantes pour visualiser les catégories des types d’attentes les plus longues :

- Le [magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) fournit des statistiques sur les attentes par requête au fil du temps. Dans le magasin des requêtes, les types d’attentes sont combinés en catégories d’attentes. Le mappage des catégories d’attentes au types d’attentes est disponible dans [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql?view=sql-server-2017#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) retourne des informations sur toutes les attentes rencontrées par les threads exécutés pendant l’opération. Vous pouvez utiliser cette vue agrégée pour diagnostiquer les problèmes de performances avec Azure SQL Database, ainsi qu’avec des requêtes et des lots spécifiques.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) retourne des informations sur la file d’attente des tâches qui sont en attente de certaines ressources.

Comme illustré dans le graphique précédent, les attentes les plus courantes sont :

- Verrous (blocage)
- E/S
- Contention liée à tempdb
- Attentes d’allocation de mémoire

Selon ce que vous voyez, chaque catégorie d’attentes a un chemin différent pour la résolution des problèmes.

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Présentation de la supervision des performances de base de données dans Azure SQL Database

L’analyse des performances d’une base de données SQL dans Azure démarre par l’analyse de l’utilisation des ressources par rapport au niveau de performances que vous avez choisi pour votre base de données. La supervision permet de déterminer si votre base de données a une capacité excédentaire ou rencontre des problèmes parce que les ressources ont atteint leur maximum, et ensuite de décider s’il est temps d’ajuster la taille de calcul et le niveau de service de votre base de données dans le [modèle d’achat DTU](sql-database-service-tiers-dtu.md) ou le [modèle d’achat vCore](sql-database-service-tiers-vcore.md). Vous pouvez superviser votre base de données dans le [portail Azure](https://portal.azure.com) à l’aide des outils graphiques ci-dessous ou des [vues de gestion dynamique](sql-database-monitoring-with-dmvs.md) SQL.

Azure SQL Database vous permet de déceler les opportunités d’amélioration et d’optimisation des performances des requêtes sans modifier les ressources, et ce, en passant en revue les [recommandations de paramétrage des performances](sql-database-advisor.md). Des index manquants et des requêtes optimisées de manière incorrecte sont souvent à l’origine de performances de base de données limitées. Vous pouvez appliquer ces recommandations de paramétrage pour améliorer les performances de votre charge de travail.
Vous pouvez également laisser Azure SQL Database [optimiser automatiquement les performances de vos requêtes](sql-database-automatic-tuning.md) en appliquant toutes les recommandations identifiées et en vérifiant qu’elles améliorent les performances de la base de données. Vous disposez des options suivantes pour la surveillance et la résolution des problèmes de performances de bases de données :

- Dans le [portail Azure](https://portal.azure.com), cliquez sur **Bases de données SQL**, sélectionnez la base de données, puis utilisez le graphique de surveillance pour rechercher des ressources proches de leur maximum. Consommation de DTU est affichée par défaut. Cliquez sur **Modifier** pour modifier l’intervalle de temps et les valeurs affichées.
- Utilisez [Query Performance Insight](sql-database-query-performance.md) pour identifier les requêtes utilisant le plus de ressources.
- Utilisez [SQL Database Advisor](sql-database-advisor-portal.md) pour obtenir des recommandations concernant la création et la suppression d’index, le paramétrage des requêtes et la résolution des problèmes de schéma.
- Utilisez [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pour le monitoring automatique des performances de votre base de données. Si un problème de performances est détecté, un journal de diagnostic est généré avec les détails correspondants et une analyse de la cause première du problème. Des recommandations sur l’amélioration des performances sont fournies quand cela est possible.
- [Activez le réglage automatique](sql-database-automatic-tuning-enable.md) et laissez Azure SQL Database corriger automatiquement les problèmes de performances identifiés.
- Vous pouvez également utiliser les [vues de gestion dynamique (DMV)](sql-database-monitoring-with-dmvs.md), les [événements étendus (`XEvents`)(sql-database/sql-database-xevent-db-diff-from-svr.md) et le [magasin de requêtes](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) pour obtenir les paramètres de performances en temps réel. Pour connaître les techniques que vous pouvez utiliser afin d’améliorer les performances d’Azure SQL Database en cas de problème avec ces rapports ou ces vues, consultez les [conseils sur les performances](sql-database-performance-guidance.md).

## <a name="monitor-databases-using-the-azure-portal"></a>Analyser des bases de données au moyen du portail Azure

Dans le [portail Azure](https://portal.azure.com/), vous pouvez superviser l’utilisation d’une base de données unique en sélectionnant votre base de données et en cliquant sur le graphique **Surveillance**. Une fenêtre **Métrique** apparaît. Vous pouvez la modifier en cliquant sur le bouton **Modifier le graphique**. Ajoutez les mesures suivantes :

- Pourcentage UC
- Pourcentage DTU
- Pourcentage E/S des données
- Pourcentage de la taille de la base de données

Une fois que vous avez ajouté ces métriques, vous pouvez continuer à les visualiser dans le graphique **Surveillance** avec plus d’informations sur la fenêtre **Métrique**. Les quatre mesures montrent le pourcentage d’utilisation moyen correspondant aux **DTU** de votre base de données. Consultez les articles [Modèle d’achat DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat vCore](sql-database-service-tiers-vcore.md) pour en savoir plus sur les niveaux de service.  

![Surveillance des niveaux de service des performances de la base de données.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Vous pouvez également configurer des alertes sur les mesures de performances. Cliquez sur le bouton **Ajouter une alerte** situé dans la fenêtre **Métrique**. Suivez l'assistant pour configurer votre alerte. Vous avez la possibilité de configurer une alerte si les mesures dépassent un certain seuil ou si la mesure tombe en dessous d’un certain seuil.

Par exemple, si vous pensez que la charge de travail dans votre base de données va augmenter, vous pouvez choisir de configurer une alerte par courrier électronique chaque fois que votre base de données atteint 80 % de n'importe quelle mesure de performances. Vous pouvez l’utiliser comme un avertissement anticipé pour déterminer le moment auquel vous devez passer à la taille de capacité de calcul supérieure.

Les métriques de performances peuvent également vous aider à déterminer si vous pouvez passer à une taille inférieure. Supposons que vous utilisez une base de données standard S2 et que toutes les mesures de performance indiquent que la base de données n'utilise pas plus de 10 % des performances en moyenne. Cette base de données fonctionnerait très bien en version S1 standard. Toutefois, prenez en considération les éventuels pics ou baisses de charges de travail avant de passer à une taille de calcul inférieure.

## <a name="improving-database-performance-with-more-resources"></a>Amélioration des performances de base de données avec davantage de ressources

Enfin, si aucun élément ne peut être modifié pour améliorer les performances de votre base de données, vous pouvez modifier la quantité de ressources disponibles dans Azure SQL Database. Vous pouvez affecter davantage de ressources en modifiant le [niveau de service DTU](sql-database-service-tiers-dtu.md) d’une base de données unique, ou augmenter les eDTU d’un pool élastique à tout moment. Ou, si vous utilisez le [modèle d’achat vCore](sql-database-service-tiers-vcore.md), vous pouvez changer le niveau de service ou augmenter les ressources allouées à votre base de données.

1. Pour les bases de données uniques, vous pouvez [modifier les niveaux de service](sql-database-service-tiers-dtu.md) et les [ressources de calcul](sql-database-service-tiers-vcore.md) à la demande afin d’améliorer les performances.
2. S’il existe plusieurs bases de données, envisagez d’utiliser des [pools élastiques](sql-database-elastic-pool-guidance.md) pour une mise à l’échelle automatique des ressources.

## <a name="tune-and-refactor-application-or-database-code"></a>Régler et refactoriser le code d’une application ou d’une base de données

Vous pouvez modifier le code d’une application pour optimiser l’utilisation de la base de données, modifier des index, forcer des plans ou utiliser des conseils afin d’adapter manuellement la base de données à votre charge de travail. Pour obtenir des directives et des conseils sur le réglage manuel et la réécriture du code, consultez la [rubrique de conseils de performances](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour activer le réglage automatique dans Azure SQL Database et permettre à cette fonction de gérer entièrement votre charge de travail, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).
- Pour utiliser le réglage manuel, vous pouvez consulter les [recommandations de réglage dans le portail Azure](sql-database-advisor-portal.md) et appliquer manuellement les recommandations qui amélioreront les performances de vos requêtes.
- Modifier les ressources disponibles dans votre base de données en modifiant les [niveaux de service Azure SQL Database](sql-database-performance-guidance.md)
