---
title: Supervision et optimisation des performances - Azure SQL Database | Microsoft Docs
description: Conseils pour le réglage des performances dans Azure SQL Database par le biais de l’évaluation et de l’amélioration.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 5df9df1474489d7f1b1fb4e1089143cca63a3e42
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71935599"
---
# <a name="monitoring-and-performance-tuning"></a>Surveillance et optimisation des performances

Azure SQL Database fournit des outils et des méthodes qui vous permettent de superviser facilement l’utilisation d’une base de données, d’y ajouter ou d’en supprimer des ressources (processeur, mémoire ou E/S), de détecter ses problèmes potentiels et de faire des suggestions pour améliorer ses performances. Les fonctionnalités fournies dans Azure SQL Database peuvent résoudre automatiquement les problèmes dans les bases de données. 

Le réglage automatique permet à une base de données de s’adapter à la charge de travail et d’optimiser automatiquement les performances. Cependant, certains problèmes particuliers peuvent nécessiter votre attention. Cet article passe en revue plusieurs bonnes pratiques et outils qui vous seront utiles pour résoudre les problèmes de performances.

S’assurer du bon fonctionnement d’une base de données passe par ces deux activités :
- La [supervision des performances de votre base de données](#monitor-database-performance), pour vérifier que les ressources affectées à la base de données peuvent gérer la charge de travail. Si la base de données atteint ses limites de ressources, envisagez les opérations suivantes :
   - Identifier et optimiser les principales requêtes consommatrices de ressources.
   - Ajouter plus de ressources par une [mise à niveau du niveau de service](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources).
- La [résolution des problèmes de performances](#troubleshoot-performance-problems), pour identifier la raison pour laquelle un problème potentiel s’est produit ainsi que la cause initiale du problème. Après avoir identifié la cause initiale, prenez les mesures nécessaires pour résoudre le problème.

## <a name="monitor-database-performance"></a>Surveiller les performances de la base de données

Pour superviser les performances d’une base de données SQL dans Azure, commencez par superviser les ressources utilisées par rapport au niveau de performances choisi pour la base de données. Supervisez les ressources suivantes :
 - **Utilisation du processeur** : vérifiez si la base de données utilise 100 pour cent du processeur sur une longue période. Une utilisation élevée du processeur peut être le signe que vous devez identifier et optimiser les requêtes qui consomment le plus de puissance de calcul. Elle peut également indiquer que la base de données ou l’instance doit être mise à niveau vers un niveau de service supérieur. 
 - **Statistiques d’attente** : utilisez [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) pour déterminer la durée d’attente des requêtes. Les requêtes peuvent subir des attentes sur les ressources, des attentes sur la file d’attente ou des attentes externes. 
 - **Utilisation d’E/S** : vérifiez si la base de données atteint les limites d’E/S du stockage sous-jacent.
 - **Utilisation de la mémoire** : la quantité de mémoire disponible pour la base de données ou l’instance est proportionnelle au nombre de vCores. Assurez-vous que la mémoire est suffisante pour la charge de travail. L’espérance de vie d’une page est l’un des paramètres pouvant indiquer à quelle vitesse les pages sont supprimées de la mémoire.

Le service Azure SQL Database comprend des outils et des ressources pour vous aider à détecter et résoudre les éventuels problèmes de performances. Vous pouvez identifier les opportunités d’amélioration et d’optimisation des performances des requêtes sans changer les ressources, grâce aux [recommandations de réglage des performances](sql-database-advisor.md). 

Des index manquants et des requêtes optimisées de manière incorrecte sont souvent à l’origine de performances de base de données limitées. Vous pouvez appliquer les recommandations de réglage pour améliorer les performances de la charge de travail. Vous pouvez aussi laisser Azure SQL Database [optimiser automatiquement les performances des requêtes](sql-database-automatic-tuning.md) en appliquant toutes les recommandations identifiées. Il vous suffit ensuite de vérifier que les recommandations ont amélioré les performances de la base de données.

> [!NOTE]
> L’indexation est disponible uniquement dans la base de données unique et les pools élastiques. Elle n’est pas disponible dans une instance managée.

Vous avez le choix des options suivantes pour superviser les performances de la base de données et résoudre les problèmes rencontrés :

- Dans le [portail Azure](https://portal.azure.com), sélectionnez **Bases de données SQL** et sélectionnez la base de données. Dans le graphique **Supervision**, recherchez les ressources qui atteignent presque leur utilisation maximale. Consommation de DTU est affichée par défaut. Sélectionnez **Modifier** pour changer l’intervalle de temps et les valeurs affichées.
- Des outils tels que SQL Server Management Studio fournissent de nombreux rapports utiles, comme le [tableau de bord Performances](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard). Exploitez ces rapports pour superviser l’utilisation des ressources et identifier les principales requêtes consommatrices de ressources. Vous pouvez utiliser le [Magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) pour identifier les requêtes dont les performances ont régressé.
- Dans le [portail Azure](https://portal.azure.com), utilisez [Query Performance Insight](sql-database-query-performance.md) pour identifier les requêtes qui consomment la plupart des ressources. Cette fonctionnalité est disponible uniquement dans la base de données unique et les pools élastiques.
- Utilisez [SQL Database Advisor](sql-database-advisor-portal.md) pour voir des recommandations destinées à vous aider à créer et supprimer des index, paramétrer des requêtes et résoudre des problèmes de schéma. Cette fonctionnalité est disponible uniquement dans la base de données unique et les pools élastiques.
- Utilisez [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pour superviser automatiquement les performances de la base de données. Quand un problème de performances est détecté, un journal de diagnostic est généré. Le journal fournit des détails et une analyse de la cause racine du problème. Une recommandation d’amélioration des performances est faite quand cela est possible.
- [Activez le réglage automatique](sql-database-automatic-tuning-enable.md) pour laisser Azure SQL Database corriger automatiquement les problèmes de performances.
- Utilisez des [vues de gestion dynamiques (DMV)](sql-database-monitoring-with-dmvs.md), des [événements étendus](sql-database-xevent-db-diff-from-svr.md) et le [Magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) pour avoir des informations d’aide plus détaillées sur la correction des problèmes de performances.

> [!TIP]
> Après avoir identifié un problème de performances, consultez nos [conseils sur les performances](sql-database-performance-guidance.md) pour trouver des moyens d’améliorer les performances d’Azure SQL Database.

## <a name="troubleshoot-performance-problems"></a>Résoudre les problèmes de performances

Pour diagnostiquer et résoudre les problèmes de performances, commencez par déterminer l’état de chaque requête active ainsi que les conditions qui causent des problèmes de performances pour chaque état de la charge de travail. Pour améliorer les performances d’Azure SQL Database, vous devez savoir que chaque demande de requête active effectuée à partir de l’application est dans l’un de ces états : En cours d’exécution ou En attente. Quand vous résolvez un problème de performances dans Azure SQL Database, gardez en tête le diagramme ci-dessous.

![États de la charge de travail](./media/sql-database-monitor-tune-overview/workload-states.png)

Un problème de performances dans une charge de travail peut être causé par une contention du processeur (une condition *liée à l’exécution*) ou par des requêtes individuelles qui attendent quelque chose (une condition *liée à l’attente*).

Les problèmes liés à l’exécution peuvent être provoqués par :
- Des **problèmes de compilation** : l’optimiseur de requête SQL peut produire un plan non optimal à cause de statistiques périmées ou bien d’une estimation incorrecte du nombre de lignes à traiter ou de la mémoire requise. Si vous savez que la requête a été exécutée plus rapidement dans le passé ou sur une autre instance (instance managée ou instance SQL Server), comparez les plans d’exécution réels pour voir s’ils diffèrent. Essayez d’appliquer des indicateurs de requête ou de regénérer des statistiques ou des index pour obtenir le meilleur plan. Activez la correction automatique des plans dans Azure SQL Database pour atténuer automatiquement ces problèmes.
- Des **problèmes d’exécution** : si le plan de requête est optimal, il atteint probablement les limites de ressources de la base de données, comme le débit d’écriture des journaux. Il est possible aussi qu’il utilise des index fragmentés nécessitant d’être regénérés. Des problèmes d’exécution peuvent également se produire quand de nombreuses requêtes simultanées ont besoin des mêmes ressources. Les *problèmes liés à l’attente* sont généralement liés à des problèmes d’exécution, car les requêtes qui ne s’exécutent pas efficacement sont probablement en attente de certaines ressources.

Les problèmes liés à l’attente peuvent être provoqués par :
- Un **blocage** : une requête peut maintenir le verrou sur certains objets de la base de données pendant que d’autres requêtes essaient d’accéder aux mêmes objets. Vous pouvez identifier les requêtes bloquantes à l’aide des vues de gestion dynamiques (DMV) ou des outils de supervision.
- Des **problèmes d’E/S** : les requêtes peuvent attendre que les pages soient écrites dans les fichiers de données ou les fichiers journaux. Dans ce cas, examinez les statistiques d’attente `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG` ou `PAGEIOLATCH_*` dans la DMV.
- Des **problèmes avec TempDB** : si la charge de travail utilise des tables temporaires ou s’il y a des dépassements de TempDB dans les plans, les requêtes ont peut-être un problème avec le débit de TempDB. 
- Des **problèmes de mémoire** : si la charge de travail n’a pas assez de mémoire, l’espérance de vie des pages peut diminuer, ou les requêtes risquent de ne pas obtenir toute la mémoire dont elles ont besoin. Dans certains cas, l’intelligence intégrée dans l’optimiseur de requête résoudra les problèmes de mémoire.
 
Les sections suivantes expliquent comment identifier et résoudre certains types de problèmes.

## <a name="performance-problems-related-to-running"></a>Problèmes de performances liés à l’exécution

En règle générale, si l’utilisation du processeur est régulièrement égale ou supérieure à 80 pour cent, le problème de performances rencontré est lié à l’exécution. Un problème lié à l’exécution peut être dû à des ressources processeur insuffisantes. Il peut également être lié à l’une des conditions suivantes :

- Trop de requêtes en cours d’exécution
- Trop de requêtes en cours de compilation
- Une ou plusieurs requêtes en cours d’exécution qui utilisent un plan de requête non optimal

Si vous détectez un problème de performances lié à l’exécution, vous devez alors identifier le problème exact à l’aide d’une ou plusieurs méthodes. Les méthodes suivantes sont les plus courantes pour identifier les problèmes liés à l’exécution :

- Utilisez le [portail Azure](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) pour surveiller le pourcentage d’utilisation du processeur.
- Utilisez ces [vues de gestion dynamiques](sql-database-monitoring-with-dmvs.md) (DMV) :

  - La DMV [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retourne la consommation du processeur, des E/S et de la mémoire pour une base de données SQL. Il y a une ligne pour chaque intervalle de 15 secondes, même s’il n’existe aucune activité dans la base de données. L’historique des données est conservé pendant une heure.
  - La DMV [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retourne les données d’utilisation du processeur et les données sur le stockage pour Azure SQL Database. Les données sont collectées et agrégées par intervalles de cinq minutes.

> [!IMPORTANT]
> Pour résoudre les problèmes d’utilisation du processeur avec des requêtes T-SQL qui utilisent les DMV sys.dm_db_resource_stats et sys.resource_stats, consultez [Identifier les problèmes de performances de processeur](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a> Requêtes présentant des problèmes de plan sensible aux paramètres

Un problème de plan sensible aux paramètres se produit quand l’optimiseur de requête génère un plan d’exécution de requête qui est optimal uniquement pour une valeur de paramètre (ou un ensemble de valeurs) spécifique. Le plan mis en cache n’est donc pas optimal pour les valeurs de paramètre qui sont utilisées dans des exécutions consécutives. Les plans non optimaux peuvent alors provoquer des problèmes de performances des requêtes et entraîner une dégradation du débit global de la charge de travail. 

Pour plus d’informations sur la détection des paramètres et le traitement des requêtes, consultez le [Guide de l’architecture de traitement des requêtes](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Plusieurs solutions de contournement peuvent atténuer les problèmes de plan sensible aux paramètres. Chaque solution de contournement présente des avantages et des inconvénients :

- Utilisez l’indicateur de requête [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) à chaque exécution de la requête. Cette solution compense la durée de compilation et augmente l’UC pour un plan de meilleure qualité. L’option `RECOMPILE` n’est souvent pas possible pour les charges de travail exigeant un débit élevé.
- Utilisez l’indicateur de requête [OPTION (OPTIMIZE FOR...) ](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pour remplacer la valeur de paramètre réelle par une valeur de paramètre standard qui produit un plan suffisant pour la plupart des possibilités de valeurs de paramètre. Cette option nécessite une bonne compréhension des valeurs de paramètre optimal et des caractéristiques du plan associé.
- Utilisez l’indicateur de requête [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pour remplacer la valeur de paramètre réelle par la moyenne du vecteur de densité. Pour effectuer cette opération, vous pouvez également capturer les valeurs de paramètres entrantes dans des variables locales, puis utiliser ces variables locales dans des prédicats à la place des paramètres. Pour cette correction, la densité moyenne doit être *suffisante*.
- Désactivez entièrement la détection de paramètres en spécifiant l’indicateur de requête [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query).
- Utilisez l’indicateur de requête [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pour empêcher les recompilations dans le cache. Cette solution de contournement suppose que le plan courant suffisant est celui qui se trouve déjà dans le cache. Vous pouvez également désactiver les mises à jour de statistiques automatiques afin de réduire le risque d’éviction du plan suffisant et de compilation d’un plan insuffisant.
- Forcez le plan en spécifiant explicitement l’indicateur de requête [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) dans le texte de la requête. Ou définissez un plan spécifique en utilisant le Magasin des requêtes ou en activant le [réglage automatique](sql-database-automatic-tuning.md).
- Remplacez la procédure unique par un ensemble imbriqué de procédures, qui peuvent être utilisées en fonction d’une logique conditionnelle et des valeurs de paramètre associées.
- Créez des alternatives d’exécution de chaîne dynamique sur une définition de procédure statique.

Pour plus d’informations sur la résolution des problèmes de plan sensible aux paramètres, consultez ces billets de blog :

- [I smell a parameter](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [Conor vs. dynamic SQL vs. procedures vs. plan quality for parameterized queries](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL query optimization techniques in SQL Server: Parameter sniffing](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Compilations provoquées par un paramétrage incorrect

Lorsqu’une requête contient des littéraux, le moteur de base de données paramètre automatiquement l’instruction, ou un utilisateur paramètre explicitement l’instruction pour réduire le nombre de compilations. Un nombre élevé de compilations pour une requête utilisant le même modèle mais différentes valeurs littérales peut entraîner une augmentation de l’utilisation du processeur. De même, si vous ne paramétrez que partiellement une requête qui garde des littéraux, le moteur de base de données ne paramètre pas davantage la requête.  

Voici un exemple de requête partiellement paramétrée :

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Dans cet exemple, `t1.c1` prend la valeur `@p1`, mais `t2.c2` garde un GUID comme littéral. Dans ce cas, si vous changez la valeur de `c2`, la requête est traitée comme une requête différente, ce qui engendre une nouvelle compilation. Pour réduire le nombre de compilations dans cet exemple, vous pouvez également paramétrer le GUID.

La requête suivante affiche le nombre de requêtes par hachage de requête pour déterminer si une requête est correctement paramétrée :

```sql
SELECT  TOP 10  
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p 
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs 
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>Facteurs impactant les changements de plan de requête

Une recompilation du plan d’exécution de requête peut aboutir à un plan de requête généré qui diffère du plan initial mis en cache. Un plan initial existant peut être automatiquement recompilé pour diverses raisons :
- Des modifications dans le schéma sont référencées par la requête.
- Des modifications de données dans les tables sont référencées par la requête. 
- Des options de contexte de requête ont été changées.

Un plan compilé peut être supprimé du cache pour plusieurs raisons. Par exemple :

- Redémarrages d’instance.
- Changements de configuration à l’échelle de la base de données.
- Sollicitation de la mémoire.
- Requêtes explicites d’effacement du cache.

Si vous utilisez un indicateur RECOMPILE, le plan n’est pas mis en cache.

Une recompilation (ou une nouvelle compilation après éviction du cache) peut toujours aboutir à la génération d’un plan d’exécution de requête identique au plan initial. Quand le plan change par rapport au plan précédent ou au plan initial, cela s’explique généralement par l’une des raisons suivantes :

- **Modification de la conception physique** : par exemple, de nouveaux index créés couvrent plus efficacement les exigences d’une requête. Les nouveaux index peuvent être utilisés sur une nouvelle compilation si l’optimiseur de requête juge que l’utilisation des nouveaux index aboutit à un plan plus optimal que l’utilisation de la structure de données qui avait été initialement sélectionnée pour la première version de l’exécution de la requête.  Toute modification physique des objets référencés peut entraîner un nouveau choix de plan au moment de la compilation.

- **Différences au niveau des ressources serveur** : quand un plan d’un système diffère du plan d’un autre système, la disponibilité des ressources, comme le nombre de processeurs disponibles, peut déterminer le choix du plan généré.  Par exemple, si un système a davantage de processeurs, un plan parallèle peut être choisi. 

- **Statistiques différentes** : les statistiques associées aux objets référencés ont peut-être changé ou sont peut-être matériellement différentes par rapport aux statistiques du système initiales.  Si les statistiques changent et qu’une recompilation a lieu, l’optimiseur de requête utilise les statistiques à partir du changement. Les fréquences et distributions de données indiquées dans les statistiques révisées peuvent différer de celles de la compilation initiale.  Ces changements sont utilisés pour estimer la cardinalité. (Les *estimations de cardinalité* correspondent au nombre de lignes qui sont supposées être transmises dans l’arborescence de requêtes logique.) Les changements apportés aux estimations de cardinalité peuvent vous amener à choisir des opérateurs physiques et ordres d’opérations associés différents.  Même des modifications mineures apportées aux statistiques peuvent entraîner une modification du plan d’exécution de la requête.

- **Changement du niveau de compatibilité de la base de données ou de la version de l’estimateur de cardinalité** :  un changement du niveau de compatibilité de la base de données peut donner lieu à de nouvelles stratégies et caractéristiques qui peuvent entraîner un plan d’exécution de requête différent.  Au-delà du niveau de compatibilité de la base de données, l’activation ou la désactivation d’un indicateur de trace 4199 ou bien un changement d’état de l’indicateur QUERY_OPTIMIZER_HOTFIXES de configuration à l’échelle de la base de données peuvent également impacter les choix de plan d’exécution de requête au moment de la compilation.  Les indicateurs de trace 9481 (forcer le CE hérité) et 2312 (forcer le CE par défaut) impactent également le choix du plan. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Résoudre les requêtes de problème ou fournir davantage de ressources

Après avoir identifié le problème, vous pouvez soit optimiser les requêtes posant problème, soit changer la taille de calcul ou le niveau de service de façon à augmenter la capacité de votre base de données SQL pour répondre aux besoins en ressources de processeur. 

Pour plus d’informations, consultez [Mettre à l’échelle des ressources de base de données unique dans Azure SQL Database](sql-database-single-database-scale.md) et [Mettre à l’échelle un pool élastique dans Azure SQL Database](sql-database-elastic-pool-scale.md). Pour plus d’informations sur la mise à l’échelle d’une instance managée, consultez [Limites de ressources des niveaux de service](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="performance-problems-caused-by-increased-workload-volume"></a>Problèmes de performances dus à une hausse du volume de la charge de travail

Une hausse du trafic d’application et du volume de la charge de travail peut entraîner une plus forte sollicitation du processeur. Prenez toutefois le temps de bien diagnostiquer ce problème. Si vous constatez un problème d’utilisation intensive du processeur, posez-vous ces questions pour déterminer si la hausse est due à une variation du volume de la charge de travail :

- Les requêtes de l’application sont-elles à l’origine du problème d’utilisation intensive du processeur ?
- Pour les requêtes qui sont les plus grosses consommatrices du processeur, analysez les points suivants :

   - Y avait-il plusieurs plans d’exécution associés à la même requête ? Le cas échéant, pour quelle raison ?
   - Entre les requêtes ayant le même plan d’exécution, les temps d’exécution étaient-ils similaires ? Y a-t-il eu davantage d’exécutions ? Si c’est le cas, la hausse de la charge de travail est probablement à l’origine des problèmes de performances.

En résumé, si le plan d’exécution de requête s’est exécuté de la même manière, mais que l’utilisation du processeur a augmenté en même temps que le nombre d’exécutions, le problème de performances est probablement lié à une hausse de la charge de travail.

Il est souvent difficile d’identifier qu’une variation du volume de la charge de travail est à l’origine d’un problème d’utilisation du processeur. Tenez compte de ces facteurs : 

- **Changement dans l’utilisation des ressources** : par exemple, imaginez un scénario où l’utilisation du processeur a enregistré une hausse de 80 pour cent durant une période prolongée.  Cela ne suffit pas pour conclure à une variation du volume de la charge de travail. Des régressions dans le plan d’exécution de requête et des changements dans la distribution des données peuvent également entraîner une plus grande utilisation des ressources, même si l’application exécute la même charge de travail.

- **Apparence d’une nouvelle requête** : une application peut exécuter un nouvel ensemble de requêtes à des moments différents.

- **Augmentation ou diminution du nombre de requêtes** : ce scénario constitue la mesure la plus évidente de la charge de travail. Le nombre de requêtes ne correspond pas toujours à une augmentation de l’utilisation des ressources. Toutefois, cette mesure est toujours un signal important, qui suppose que d’autres facteurs restent inchangés.

## <a name="waiting-related-performance-problems"></a>Problèmes de performances liés à l’attente 

Si vous êtes sûr que votre problème de performances n’est pas lié à une utilisation intensive du processeur ou à l’exécution, le problème est lié à l’attente. Autrement dit, vos ressources processeur ne sont pas utilisées efficacement, car le processeur est en attente d’une autre ressource. Dans ce cas, identifiez quelles ressources attend le processeur. 

Ces méthodes sont couramment utilisées pour afficher les principales catégories de types d’attentes :

- Utilisez le [Magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) pour avoir des statistiques sur les attentes par requête dans le temps. Dans le magasin des requêtes, les types d’attentes sont combinés en catégories d’attentes. Vous pouvez consulter une table de mappage des catégories d’attentes avec les types d’attentes dans [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Utilisez [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) pour retourner des informations sur toutes les attentes rencontrées par les threads exécutés pendant l’opération. Vous pouvez utiliser cette vue agrégée pour diagnostiquer les problèmes de performances avec Azure SQL Database, ainsi qu’avec des requêtes et des lots spécifiques.
- Utilisez [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) pour retourner des informations sur la file d’attente des tâches qui sont en attente de certaines ressources.

Dans les scénarios d’utilisation intensive du processeur, le Magasin des requêtes et les statistiques d’attente ne reflètent pas forcément l’utilisation du processeur dans les cas suivants :

- Des requêtes grandes consommatrices de ressources processeur sont toujours en cours d’exécution.
- Les requêtes consommant beaucoup de ressources processeur étaient en cours d’exécution au moment d’un basculement.

Les vues de gestion dynamiques qui font le suivi des données du Magasin des requêtes et des statistiques d’attente affichent des résultats uniquement pour les requêtes terminées avec succès et les requêtes ayant expiré. Elles n’affichent pas de données ayant trait aux instructions en cours d’exécution tant que ces instructions ne sont pas terminées. Utilisez la vue de gestion dynamique [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) pour suivre les requêtes en cours d’exécution et la durée du worker associé.

Comme le montre le graphique au début de cet article, les attentes les plus fréquentes sont les suivantes :

- Verrous (blocage)
- E/S
- Contention liée à TempDB
- Attentes d’allocation de mémoire

> [!IMPORTANT]
> Pour voir les requêtes T-SQL qui utilisent des vues de gestion dynamiques pour résoudre les problèmes liés à l’attente, consultez :
>
> - [Identifier les problèmes de performances d’E/S](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifier les attentes d’allocation de mémoire](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [Attentes et verrous TigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>Améliorer les performances de la base de données avec davantage de ressources

Si aucun des éléments actionnables ne réussit à améliorer les performances de votre base de données, vous pouvez modifier la quantité de ressources disponibles dans Azure SQL Database. Attribuez davantage de ressources en changeant le [niveau de service DTU](sql-database-service-tiers-dtu.md) d’une base de données unique. Vous pouvez aussi augmenter les eDTU d’un pool élastique à tout moment. Ou, si vous utilisez le [modèle d’achat vCore](sql-database-service-tiers-vcore.md), changez le niveau de service ou augmentez les ressources allouées à votre base de données.

Pour les bases de données uniques, vous pouvez [changer les niveaux de service ou les tailles de calcul](sql-database-single-database-scale.md) à la demande afin d’améliorer leurs performances. S’il existe plusieurs bases de données, envisagez d’utiliser des [pools élastiques](sql-database-elastic-pool-guidance.md) pour une mise à l’échelle automatique des ressources.

## <a name="tune-and-refactor-application-or-database-code"></a>Régler et refactoriser le code d’une application ou d’une base de données

Vous pouvez optimiser le code d’application pour la base de données, modifier des index, forcer des plans, ou appliquer des conseils afin d’adapter manuellement la base de données à votre charge de travail. Pour plus d’informations sur le réglage manuel et la réécriture du code, consultez les [conseils de réglage des performances](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour activer le réglage automatique dans Azure SQL Database et laisser cette fonctionnalité gérer entièrement votre charge de travail, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).
- Pour utiliser le réglage manuel, passez en revue les [recommandations de réglage dans le portail Azure](sql-database-advisor-portal.md). Appliquez manuellement les recommandations qui améliorent les performances de vos requêtes.
- Modifiez les ressources disponibles dans votre base de données en changeant les [niveaux de service Azure SQL Database](sql-database-performance-guidance.md).
