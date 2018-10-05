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
ms.date: 07/16/2018
ms.openlocfilehash: c79285247950510791ede915fcf0e5373792044f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165039"
---
# <a name="monitoring-and-performance-tuning"></a>Surveillance et optimisation des performances

Azure SQL Database est un service de données flexible et géré automatiquement dans lequel vous pouvez aisément surveiller l’utilisation, ajouter ou supprimer des ressources (processeur, mémoire, E/S), obtenir des recommandations susceptibles d’améliorer les performances de votre base de données ou laisser la base de données s’adapter à votre charge de travail et optimiser automatiquement les performances.

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Présentation de la supervision des performances de base de données dans Azure SQL Database
La supervision des performances d’une base de données SQL dans Azure démarre par la supervision de l’utilisation des ressources par rapport au niveau de performances que vous avez choisi pour votre base de données. La supervision permet de déterminer si votre base de données a une capacité excédentaire ou rencontre des problèmes parce que les ressources ont atteint leur maximum, et ensuite de décider s’il est temps d’ajuster la taille de calcul et le niveau de service de votre base de données dans le [modèle d’achat DTU](sql-database-service-tiers-dtu.md) ou le [modèle d’achat vCore](sql-database-service-tiers-vcore.md). Vous pouvez superviser votre base de données dans le [portail Azure](https://portal.azure.com) à l’aide des outils graphiques ci-dessous ou des [vues de gestion dynamique](sql-database-monitoring-with-dmvs.md) SQL.

Azure SQL Database vous permet de déceler les opportunités d’amélioration et d’optimisation des performances des requêtes sans modifier les ressources, et ce, en passant en revue les [recommandations de paramétrage des performances](sql-database-advisor.md). Des index manquants et des requêtes optimisées de manière incorrecte sont souvent à l’origine de performances de base de données limitées. Vous pouvez appliquer ces recommandations de paramétrage pour améliorer les performances de votre charge de travail.
Vous pouvez également laisser Azure SQL Database [optimiser automatiquement les performances de vos requêtes](sql-database-automatic-tuning.md) en appliquant toutes les recommandations identifiées et en vérifiant qu’elles améliorent les performances de la base de données. Vous disposez des options suivantes pour la surveillance et la résolution des problèmes de performances de bases de données :

- Dans le [portail Azure](https://portal.azure.com), cliquez sur **Bases de données SQL**, sélectionnez la base de données, puis utilisez le graphique de surveillance pour rechercher des ressources proches de leur maximum. Consommation de DTU est affichée par défaut. Cliquez sur **Modifier** pour modifier l’intervalle de temps et les valeurs affichées.
- Utilisez [Query Performance Insight](sql-database-query-performance.md) pour identifier les requêtes utilisant le plus de ressources.
- Utilisez [SQL Database Advisor](sql-database-advisor-portal.md) pour obtenir des recommandations concernant la création et la suppression d’index, le paramétrage des requêtes et la résolution des problèmes de schéma.
- Utilisez [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pour le monitoring automatique des performances de votre base de données. Si un problème de performances est détecté, un journal de diagnostic est généré avec les détails correspondants et une analyse de la cause première du problème. Des recommandations sur l’amélioration des performances sont fournies quand cela est possible.
- [Activez le réglage automatique](sql-database-automatic-tuning-enable.md) et laissez Azure SQL Database corriger automatiquement les problèmes de performances identifiés.
- Vous pouvez également utiliser les [vues de gestion dynamique (DMV)](sql-database-monitoring-with-dmvs.md), les [événements étendus (`XEvents`)(sql-database/sql-database-xevent-db-diff-from-svr.md) et le [magasin de requêtes](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) pour obtenir les paramètres de performances en temps réel. Pour connaître les techniques que vous pouvez utiliser afin d’améliorer les performances d’Azure SQL Database en cas de problème avec ces rapports ou ces vues, consultez les [conseils sur les performances](sql-database-performance-guidance.md).

## <a name="monitor-databases-using-the-azure-portal"></a>Analyser des bases de données au moyen du portail Azure
Dans le [portail Azure](https://portal.azure.com/), vous pouvez surveiller l’utilisation d’une base de données unique en sélectionnant votre base de données et en cliquant sur le graphique **Surveillance**. Une fenêtre **Métrique** apparaît. Vous pouvez la modifier en cliquant sur le bouton **Modifier le graphique**. Ajoutez les mesures suivantes :

* Pourcentage UC
* Pourcentage DTU
* Pourcentage E/S des données
* Pourcentage de la taille de la base de données

Une fois que vous avez ajouté ces métriques, vous pouvez continuer à les afficher dans le graphique **Surveillance** avec plus d’informations dans la fenêtre **Métrique**. Les quatre mesures montrent le pourcentage d’utilisation moyen correspondant aux **DTU** de votre base de données. Consultez les articles [Modèle d’achat DTU](sql-database-service-tiers-dtu.md) et [Modèle d’achat vCore](sql-database-service-tiers-vcore.md) pour en savoir plus sur les niveaux de service.  

![Surveillance des niveaux de service des performances de la base de données.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Vous pouvez également configurer des alertes sur les mesures de performances. Cliquez sur le bouton **Ajouter une alerte** situé dans la fenêtre **Métrique**. Suivez l'assistant pour configurer votre alerte. Vous avez la possibilité de configurer une alerte si les mesures dépassent un certain seuil ou si la mesure tombe en dessous d’un certain seuil.

Par exemple, si vous pensez que la charge de travail dans votre base de données va augmenter, vous pouvez choisir de configurer une alerte par courrier électronique chaque fois que votre base de données atteint 80 % de n'importe quelle mesure de performances. Vous pouvez l’utiliser comme un avertissement pour déterminer le moment auquel vous devrez passer à une taille de calcul supérieure.

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
