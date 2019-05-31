---
title: Surveillance et optimisation des performances - Azure SQL Database | Microsoft Docs
description: Conseils pour le réglage des performances dans Azure SQL Database par le biais de l’évaluation et de l’amélioration.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: cae0fbd450e6b392e1689d4642181f6e5279752b
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393208"
---
# <a name="monitoring-and-performance-tuning"></a>Surveillance et optimisation des performances

Permet de base de données SQL Azure vous permettent de surveiller facilement l’utilisation, ajouter ou supprimer des ressources (processeur, mémoire, e/s), dépanner les problèmes potentiels et trouver des recommandations qui peuvent améliorer les performances de votre base de données. Base de données SQL Azure propose de nombreuses fonctionnalités qui peuvent résoudre automatiquement les problèmes dans vos bases de données si vous souhaitez laisser la base de données s’adapter à votre charge de travail et optimiser automatiquement les performances. Toutefois, il existe certains problèmes personnalisés que vous devrez peut-être résoudre les problèmes. Cet article explique certaines meilleures pratiques et outils que vous pouvez utiliser pour résoudre les problèmes de performances.

Il existe deux activités principales que vous devez effectuer afin de garantir que cette base de données vous est en cours d’exécution sans problème :
- [Surveillance des performances de la base de données](#monitoring-database-performance) afin de vous assurer que les ressources affectées à votre base de données peuvent gérer votre charge de travail. Si vous voyez que vous atteignez les limites de ressources, vous devez identifier les principales requêtes consommatrices de ressources et d’optimiser les ou ajouter des ressources en mettant à niveau le niveau de service.
- [Résoudre les problèmes de performances](#troubleshoot-performance-issues) afin d’identifier la raison pour laquelle un problème potentiel est devenu, identifier la cause première du problème et l’action permettant de corriger le problème.

## <a name="monitoring-database-performance"></a>Supervision des performances de la base de données

L’analyse des performances d’une base de données SQL dans Azure démarre par l’analyse de l’utilisation des ressources par rapport au niveau de performances que vous avez choisi pour votre base de données. Vous devez surveiller les ressources suivantes :
 - **L’utilisation du processeur** -vous devez vérifier sont atteindre 100 % d’utilisation du processeur dans une longue période de temps. Cela peut indiquer que vous devrez peut-être mettre à niveau votre base de données ou d’instance ou identifier et régler les requêtes qui utilisent la majeure partie de la puissance de calcul.
 - **Statistiques d’attente** -vous devez vérifier ce qui explique pourquoi vos requêtes en attente pour certaines ressources. Queriesmig attendre que les données doivent être extraites ou enregistrées pour les fichiers de base de données, en attente, car une limite de ressources est atteint, etc.
 - **L’utilisation d’e/s** -vous devez vérifier vous avez atteint les limites d’e/s du stockage sous-jacent.
 - **Utilisation de la mémoire** -la quantité de mémoire disponible pour votre base de données ou d’une instance est proportionnelle au nombre de VCORE, et vous devez vérifier est-il suffisant pour votre charge de travail. Espérance de vie de page est l’un des paramètres qui peuvent indiquer sont vos pages supprimées rapidement à partir de la mémoire.

Base de données SQL Azure **fournit les donne des conseils qui peuvent vous aider à dépanner et résolvent les éventuels problèmes de performances**. Vous pouvez facilement identifier les opportunités pour améliorer et optimiser les performances de requête sans modifier les ressources en examinant [recommandations de réglage des performances](sql-database-advisor.md). Des index manquants et des requêtes optimisées de manière incorrecte sont souvent à l’origine de performances de base de données limitées. Vous pouvez appliquer ces recommandations de paramétrage pour améliorer les performances de votre charge de travail. Vous pouvez également laisser la base de données Azure SQL [optimiser automatiquement les performances de vos requêtes](sql-database-automatic-tuning.md) en appliquant toutes les recommandations identifiées et en vérifiant qu’elles améliorent les performances de la base de données.

Vous disposez des options suivantes pour la surveillance et la résolution des problèmes de performances de bases de données :

- Dans le [portail Azure](https://portal.azure.com), cliquez sur **Bases de données SQL**, sélectionnez la base de données, puis utilisez le graphique de surveillance pour rechercher des ressources proches de leur maximum. Consommation de DTU est affichée par défaut. Cliquez sur **Modifier** pour modifier l’intervalle de temps et les valeurs affichées.
- Outils tels que SQL Server Management Studio fournissent de nombreux rapports utiles comme un [tableau de bord performances](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) où vous pouvez surveiller l’utilisation des ressources et identifier les principales requêtes, consommatrices de ressources ou [requête Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)où vous pouvez identifier les requêtes aux performances diminuées.
- Utilisez [Query Performance Insight](sql-database-query-performance.md) le [Azure portal](https://portal.azure.com) pour identifier les requêtes qui passent le plus de ressources. Cette fonctionnalité est uniquement disponible dans la base de données unique et des Pools élastiques.
- Utilisez [SQL Database Advisor](sql-database-advisor-portal.md) pour obtenir des recommandations concernant la création et la suppression d’index, le paramétrage des requêtes et la résolution des problèmes de schéma. Cette fonctionnalité est uniquement disponible dans la base de données unique et des Pools élastiques.
- Utilisez [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pour le monitoring automatique des performances de votre base de données. Si un problème de performances est détecté, un journal de diagnostic est généré avec les détails correspondants et une analyse de la cause première du problème. Des recommandations sur l’amélioration des performances sont fournies quand cela est possible.
- [Activez le réglage automatique](sql-database-automatic-tuning-enable.md) et laissez Azure SQL Database corriger automatiquement les problèmes de performances identifiés.
- Utilisez des [vues de gestion dynamique (DMV)](sql-database-monitoring-with-dmvs.md), des [événements étendus](sql-database-xevent-db-diff-from-svr.md) et le [magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) pour savoir comment corriger les problèmes de performances.

> [!TIP]
> Consultez les [conseils sur les performances](sql-database-performance-guidance.md) pour connaître les techniques que vous pouvez utiliser afin d’améliorer les performances d’Azure SQL Database après avoir identifié l’origine du problème de performances au moyen de l’une des méthodes ci-dessus.

## <a name="troubleshoot-performance-issues"></a>Résoudre les problèmes de performances

Pour diagnostiquer et résoudre les problèmes de performances, vous devez d’abord connaître l’état de chaque requête active et les conditions qui entraînent des problèmes de performances pour chaque état de la charge de travail. Pour améliorer les performances d’Azure SQL Database, il faut comprendre que chaque demande de requête active effectuée depuis votre application est dans un état En cours d’exécution ou En attente. Lors du dépannage d’un problème de performances dans Azure SQL Database, gardez à l’esprit le graphique suivant pour diagnostiquer et résoudre les problèmes de performances.

![États de la charge de travail](./media/sql-database-monitor-tune-overview/workload-states.png)

Pour une charge de travail, les problèmes de performances peuvent être dus à une contention de l’UC (une condition **liée à l’exécution**) ou à des requêtes individuelles en attente de quelque chose (une condition **liée à l’attente**).

Les causes ou **en cours d’exécution liées** problèmes peuvent être :
- **Problèmes de compilation** -optimiseur de requête SQL peut produire un plan optimaux en raison de statistiques obsolètes, incorrecte estimation du nombre de lignes qui seront traités ou l’estimation de mémoire nécessaire. Si vous connaissez que cette requête a été exécutée plus rapidement dans le passé ou sur une autre instance (instance Managed Instance ou SQL Server), mettez les plans d’exécution réels et comparaison soient visibles sont différents. Essayez d’appliquer des indicateurs de requête ou reconstructions d’index ou les statistiques pour obtenir le meilleur plan. Activer la correction automatique du plan dans la base de données SQL Azure pour atténuer automatiquement ces problèmes.
- **Problèmes d’exécution** : si le plan de requête est optimal puis il atteint probablement certaines limites de ressources dans la base de données telles que de débit d’écriture de journal ou il peut utiliser des index défragmentés qui doivent être reconstruits. Un grand nombre de requêtes simultanées qui passent les ressources peut-être également être la cause de problèmes d’exécution. **Dépendant de l’attente** problèmes sont dans la plupart des cas liés aux problèmes d’exécution, car les requêtes qui sont exécutent pas efficacement sont probablement en attente pour certaines ressources.

Les causes ou **dépendant de l’attente** problèmes peuvent être :
- **Blocage** -une seule requête peut contenir le verrou sur certains objets de base de données, tandis que d’autres essaie d’accéder aux mêmes objets. Vous pouvez facilement identifier les requêtes bloquantes à l’aide des DMV ou outils de surveillance.
- **Les problèmes d’e/s** -requêtes peuvent être en attente pour les pages à écrire dans les fichiers journaux ou de données. Dans ce cas, vous verrez `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`, ou `PAGEIOLATCH_*` statistiques dans la vue de gestion dynamique d’attente.
- **Problèmes de TempDB** : Si vous utilisez un grand nombre de tables temporaires ou que vous voyez un grand nombre de TempDB répand dans vos plans de vos requêtes, vous avez peut-être un problème avec un débit de TempDB. 
- **Problèmes liés à la mémoire** -vous ne disposez pas suffisamment de mémoire pour votre charge de travail afin que votre espérance de vie de page peut supprimer ou de vos requêtes sont bien moins allocation de mémoire que nécessaire. Dans certains cas, l’intelligence intégrée dans l’optimiseur de requête sera résoudre ces problèmes.
 
Dans les sections suivantes sera expliqué comment identifier et résoudre certains de ces problèmes.

## <a name="running-related-performance-issues"></a>Problèmes de performances liés à l’exécution

En règle générale, si votre utilisation de l’UC est constamment d’au moins 80 %, vous avez un problème de performances lié à l’exécution. Si vous rencontrez un problème d’exécution, il est peut-être dû à des ressources processeur insuffisantes ou à l’une des conditions suivantes :

- Trop de requêtes en cours d’exécution
- Trop de requêtes en cours de compilation
- Une ou plusieurs requêtes en cours d’exécution utilisent un plan de requête qui n’est pas le plan optimal

Si vous avez identifié un problème de performances lié à l’exécution, vous devez alors identifier le problème exact à l’aide d’une ou plusieurs méthodes. Les méthodes les plus courantes pour identifier les problèmes liés à l’exécution sont les suivantes :

- Utilisez le [portail Azure](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) pour surveiller le pourcentage d’utilisation de l’UC.
- Utilisez les [vues de gestion dynamique](sql-database-monitoring-with-dmvs.md) suivantes :

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retourne la consommation d’UC, d’E/S et de mémoire pour une base de données Azure SQL Database. Il existe une ligne pour toutes les 15 secondes, même s’il n’existe aucune activité dans la base de données. L’historique des données est conservé pendant une heure.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retourne les données d’utilisation de l’UC et les données sur le stockage pour une base de données Azure SQL Database. Les données sont collectées et agrégées par intervalles de cinq minutes.

> [!IMPORTANT]
> Pour obtenir un ensemble de requêtes T-SQL utilisant ces vues de gestion dynamique pour résoudre les problèmes d’utilisation du processeur, consultez [Identifier les problèmes de performances d’UC](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a> Résoudre les problèmes de plan de requête de sensible aux paramètres d’exécution des requêtes

Le problème de plan sensible aux paramètres fait référence à un scénario dans lequel l’optimiseur de requête génère un plan d’exécution optimal pour une valeur de paramètre spécifique (ou un ensemble de valeurs) et le plan mis en cache est ensuite non optimal pour les valeurs de paramètre utilisées dans des exécutions consécutives. Les plans non optimaux peuvent conduire à des problèmes de performances de requête et à une dégradation du débit de charge de travail globale. Pour plus d’informations sur la détection des paramètres et de traitement des requêtes, consultez la [Guide d’Architecture de traitement des requêtes](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Plusieurs solutions s’offrent à vous pour atténuer les problèmes, chacun présentant des inconvénients et des avantages :

- Utilisez l’indicateur de requête [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) à chaque exécution de la requête. Cette solution compense la durée de compilation et augmente l’UC pour un plan de meilleure qualité. L’utilisation de l’option `RECOMPILE` n’est souvent pas possible pour les charges de travail exigeant un débit élevé.
- Utilisez l’indicateur de requête [OPTION (OPTIMIZE FOR...) ](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pour remplacer la valeur de paramètre réel par une valeur de paramètre classique qui produit un plan suffisamment adapté pour la plupart des possibilités de valeur de paramètre.   Cette option nécessite une bonne compréhension des valeurs de paramètre optimal et des caractéristiques du plan associé.
- Utilisez l’indicateur de requête [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pour remplacer la valeur de paramètre réel en échange de l’utilisation de la moyenne de vecteur de densité. Pour effectuer cette opération, vous pouvez également capturer les valeurs de paramètre entrant dans des variables locales, puis utiliser les variables locales dans des prédicats plutôt que les paramètres en eux-mêmes. La densité moyenne doit être *suffisante* avec ce correctif particulier.
- Désactivez entièrement la détection de paramètres avec l’indicateur de requête [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query).
- Utilisez l’indicateur de requête [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) pour empêcher les recompilations pendant la mise en cache. Cette solution suppose que le plan courant *suffisamment adapté* est celui se trouvant déjà dans le cache. Vous pouvez également désactiver les mises à jour automatiques des statistiques, afin de réduire le risque d’exclusion du plan adapté et de compilation d’un plan inadapté.
- Forcez le plan en utilisant explicitement l’indicateur de requête [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) (en spécifiant explicitement, en définissant un plan spécifique à l’aide de Magasin de données des requêtes ou en activant le [réglage automatique](sql-database-automatic-tuning.md)).
- Remplacez la procédure unique par un ensemble imbriqué de procédures, qui peuvent être utilisées en fonction d’une logique conditionnelle et des valeurs de paramètre associées.
- Créez des alternatives d’exécution de chaîne dynamique sur une définition de procédure statique.

Pour plus d’informations sur la résolution de ces types de problèmes, consultez :

- Cela [il y a un paramètre](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) billet de blog
- Ce billet de blog [Conor vs. Dynamic SQL vs. Procedures vs. Plan Quality for Parameterized Queries](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) (Conor vs SQL dynamique vs procédures vs requêtes paramétrables)
- Cela [Techniques d’optimisation de requête SQL dans SQL Server : Détection de paramètre](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) billet de blog

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Résolution des problèmes de l’activité de compilation en raison d’un paramétrage incorrect

Lorsqu’une requête contient des littéraux, le moteur de base de données choisit de paramétrer automatiquement l’instruction, ou un utilisateur peut explicitement la paramétrer afin de réduire le nombre de compilations. Un nombre élevé de compilations d’une requête utilisant le même modèle mais différentes valeurs littérales peut entraîner une augmentation de l’utilisation d’UC. De même, si vous ne paramétrez que partiellement une requête qui continue à avoir des littéraux, le moteur de base de données ne la paramètre pas davantage.  Vous trouverez ci-dessous un exemple de requête partiellement paramétrable :

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

Dans l’exemple précédent, `t1.c1` prend `@p1` mais `t2.c2` continue de prendre GUID en tant que littéral. Dans ce cas, si vous modifiez la valeur de `c2`, la requête est traitée comme une requête différente et une nouvelle compilation se produit. Pour réduire les compilations dans l’exemple précédent, la solution consiste également à paramétrer le GUID.

La requête suivante affiche le nombre de requêtes par hachage de requête pour déterminer si une requête est correctement paramétrable ou non :

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

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Résoudre les requêtes de problème ou fournir davantage de ressources

Une fois que vous avez identifié le problème, vous pouvez soit optimiser les requêtes associées, ou bien mettre à niveau la taille de calcul ou le niveau de service de façon à augmenter la capacité de votre base de données Azure SQL pour absorber les besoins en ressources de processeur. Pour plus d’informations sur la mise à l’échelle des ressources pour les bases de données uniques, consultez [Mettre à l’échelle des ressources de base de données unique dans Azure SQL Database](sql-database-single-database-scale.md) et, pour la mise à l’échelle des ressources pour les pools élastiques, consultez [Mettre à l’échelle des ressources de pool élastique dans Azure SQL Database](sql-database-elastic-pool-scale.md). Pour plus d’informations sur la mise à l’échelle d’une instance gérée, consultez [Limites de ressources au niveau d’une instance](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Déterminer si les problèmes d’exécution sont dus à l’augmentation du volume de charge de travail

Une augmentation de la charge de travail et du trafic d’application peut expliquer l’augmentation de l’utilisation de l’UC, mais vous devez veiller à diagnostiquer correctement ce problème. Dans un scénario avec une UC élevée, répondez aux questions suivantes pour déterminer si une augmentation de l’UC est effectivement due à des modifications du volume de charge de travail :

1. Les requêtes de l’application sont-elles à l’origine du problème d’UC élevée ?
2. Pour les principales requêtes de consommation d’UC (qui peuvent être identifiées) :

   - Déterminez s’il y a plusieurs plans d’exécution associés à la même requête. Le cas échéant, déterminez pourquoi.
   - Pour les requêtes avec le même plan d’exécution, déterminez si les durées d’exécution sont cohérentes et si le nombre d’exécutions a augmenté. Si oui, il existe probablement des problèmes de performances en raison d’une augmentation de charge de travail.

Pour résumer, si le plan d’exécution de requête ne s’est pas exécuté de façon différente, mais que l’utilisation de l’UC a augmenté avec le nombre d’exécutions, il existe probablement un problème de performances en lien avec une augmentation de charge de travail.

Il est souvent difficile d’arriver à la conclusion qu’une modification de charge de travail est à l’origine d’un problème d’UC.   Facteurs à prendre en considération : 

- **Modification de l’utilisation des ressources**

  Par exemple, imaginez un scénario où l’UC a enregistré une augmentation de 80 % pendant une période de temps prolongée.  L’utilisation de l’UC uniquement ne signifie pas que le volume de charge de travail a été modifié.  Des modifications de la distribution des données et des régressions du plan d’exécution de requête peuvent également contribuer à une utilisation plus élevée des ressources, même si l’application exécute la même charge de travail.

- **Une nouvelle requête est apparue**

   Une application peut exécuter un nouvel ensemble de requêtes à des moments différents.

- **Le nombre de requêtes a augmenté ou diminué**

   Ce scénario est la mesure la plus évidente de charge de travail. Le nombre de requêtes ne correspond pas toujours à une augmentation de l’utilisation des ressources. Toutefois, cette mesure est toujours un signal important, qui suppose que d’autres facteurs restent inchangés.

## <a name="waiting-related-performance-issues"></a>Problèmes de performances liés à l’attente

Après vous être assuré qu’il ne s’agit pas d’un problème de performances lié à l’exécution, avec une utilisation intensive de l’UC, vous êtes confronté à un problème de performances lié à l’attente. Autrement dit, vos ressources de processeur ne sont pas utilisées efficacement car le processeur est en attente d’une autre ressource. Dans ce cas, l’étape suivante consiste à identifier ce qu’attendent vos ressources de processeur. Les méthodes les plus courantes pour visualiser les catégories des types d’attentes les plus longues sont les suivantes :

- Le [magasin des requêtes](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) fournit des statistiques sur les attentes par requête au fil du temps. Dans le magasin des requêtes, les types d’attentes sont combinés en catégories d’attentes. Le mappage des catégories d’attentes au types d’attentes est disponible dans [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) retourne des informations sur toutes les attentes rencontrées par les threads exécutés pendant l’opération. Vous pouvez utiliser cette vue agrégée pour diagnostiquer les problèmes de performances avec Azure SQL Database, ainsi qu’avec des requêtes et des lots spécifiques.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) retourne des informations sur la file d’attente des tâches qui sont en attente de certaines ressources.

Dans les scénarios avec une UC élevée, le Magasin de données des requêtes et les statistiques d’attente ne reflètent pas toujours l’utilisation de l’UC pour deux raisons :

- Des requêtes de consommation élevée d’UC peuvent toujours être en cours d’exécution et les requêtes non terminées
- Les requêtes consommant beaucoup d’UC étaient en cours d’exécution quand un basculement s’est produit

Le Magasin de données des requêtes et les vues de gestion dynamique du suivi des statistiques d’attente affichent uniquement des résultats pour les requêtes ayant expiré ou ayant été complètement terminées, et n’affichent pas de données pour les instructions en cours d’exécution (tant qu’elles ne sont pas terminées). La vue de gestion dynamique [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) vous permet de suivre les requêtes en cours d’exécution et la durée du rôle de travail associé.

Comme illustré dans le graphique précédent, les attentes les plus courantes sont :

- Verrous (blocage)
- E/S
- Contention liée à `tempdb`
- Attentes d’allocation de mémoire

> [!IMPORTANT]
> Pour obtenir un ensemble de requêtes T-SQL utilisant ces vues de gestion dynamique pour résoudre ces problèmes d’attente, consultez les sections suivantes :
>
> - [Identifier les problèmes de performances d’E/S](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifier les problèmes de performances `tempdb`](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifier les attentes d’allocation de mémoire](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox - attentes et verrous](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Amélioration des performances de base de données avec davantage de ressources

Enfin, si aucun élément ne peut être modifié pour améliorer les performances de votre base de données, vous pouvez modifier la quantité de ressources disponibles dans Azure SQL Database. Vous pouvez affecter davantage de ressources en modifiant le [niveau de service DTU](sql-database-service-tiers-dtu.md) d’une base de données unique, ou augmenter les eDTU d’un pool élastique à tout moment. Ou, si vous utilisez le [modèle d’achat vCore](sql-database-service-tiers-vcore.md), vous pouvez changer le niveau de service ou augmenter les ressources allouées à votre base de données.

1. Pour les bases de données uniques, vous pouvez [changer les niveaux de service](sql-database-single-database-scale.md) et les [ressources de calcul](sql-database-single-database-scale.md) à la demande afin d’améliorer les performances.
2. S’il existe plusieurs bases de données, envisagez d’utiliser des [pools élastiques](sql-database-elastic-pool-guidance.md) pour une mise à l’échelle automatique des ressources.

## <a name="tune-and-refactor-application-or-database-code"></a>Régler et refactoriser le code d’une application ou d’une base de données

Vous pouvez modifier le code d’une application pour optimiser l’utilisation de la base de données, modifier des index, forcer des plans ou utiliser des conseils afin d’adapter manuellement la base de données à votre charge de travail. Pour obtenir des directives et des conseils sur le réglage manuel et la réécriture du code, consultez la [rubrique de conseils de performances](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour activer le réglage automatique dans Azure SQL Database et permettre à cette fonction de gérer entièrement votre charge de travail, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).
- Pour utiliser le réglage manuel, vous pouvez consulter les [recommandations de réglage dans le portail Azure](sql-database-advisor-portal.md) et appliquer manuellement les recommandations qui amélioreront les performances de vos requêtes.
- Modifier les ressources disponibles dans votre base de données en changeant les [niveaux de service Azure SQL Database](sql-database-performance-guidance.md)
