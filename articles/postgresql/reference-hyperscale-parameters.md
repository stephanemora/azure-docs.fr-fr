---
title: Paramètres serveur - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Paramètres de l’API SQL d’Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136588"
---
# <a name="server-parameters"></a>Paramètres de serveur

Il existe différents paramètres de serveur qui affectent le comportement d’Hyperscale (Citus), les deux paramètres de la norme PostgreSQL et les paramètres spécifiques à Hyperscale (Citus). Pour en savoir plus sur les paramètres de configuration de PostgreSQL, vous pouvez consulter la section de la [configuration de l’exécution](http://www.postgresql.org/docs/current/static/runtime-config.html) de la documentation PostgreSQL.

Le reste de cette référence vise à discuter des paramètres de configuration spécifiques à l’Hyperscale (Citus). Ces paramètres peuvent être définis dans le Portail Azure sous **paramètres du nœud Worker** sous **Paramètres** pour un groupe de serveurs Hyperscale (Citus).

> [!NOTE]
>
> Les groupes de serveurs Hyperscale exécutant des versions antérieures du CItus Engine peuvent ne pas proposer tous les paramètres listés ci-dessous.

## <a name="general-configuration"></a>Configuration générale

### <a name="citususe_secondary_nodes-enum"></a>citus.use\_secondary\_nodes (enum)

Définit la stratégie à utiliser lors du choix des nœuds pour les requêtes SELECT. S’il est défini sur « always », le planificateur interroge uniquement les nœuds marqués comme noderole « secondaires » dans [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

Les valeurs prises en charge pour cette énumération sont les suivantes :

-   **never :** (par défaut) Toutes les lectures se produisent sur les nœuds principaux.
-   **always :** Les lectures sont exécutées sur les nœuds secondaires à la place et les commandes insert/update sont désactivées.

### <a name="cituscluster_name-text"></a>citus.cluster\_name (texte)

Informe le responsable du nœud coordinateur du cluster qu’il coordonne. Une fois le cluster\_name défini, le planificateur interroge uniquement les nœuds worker dans ce cluster.

### <a name="citusenable_version_checks-boolean"></a>citus.enable\_version\_checks (booléen)

Mettre à niveau la version d’Hyperscale (Citus) nécessite un redémarrage serveur (pour récupérer la nouvelle bibliothèque partagée), suivie de la commande ALTER EXTENSION UPDATE.  L’échec de l’exécution des deux étapes peut entraîner des erreurs ou des blocages.
Hyperscale (Citus) valide donc la version du code et celle de l’extension, et renvoie une erreur si elles ne correspondent pas.

Cette valeur est définie par défaut sur true et est effective sur le coordinateur. Dans de rares cas, les processus de mise à niveau complexes peuvent nécessiter l’affectation de la valeur false à ce paramètre, ce qui désactive la vérification.

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus.log\_distributed\_deadlock\_detection (boléen)

Indique s’il faut enregistrer le traitement lié à la détection de blocage distribué dans le journal du serveur. La valeur par défaut est « False ».

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus.distributed\_deadlock\_detection\_factor (virgule flottante)

Définit le délai d’attente avant de vérifier les blocages distribués. En particulier, le délai d’attente sera cette valeur multipliée par le paramètre [deadlock\_timeout](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) de PostgreSQL. La valeur par défaut est `2`. Une valeur de `-1` désactive la détection de blocage distribué.

### <a name="citusnode_connection_timeout-integer"></a>citus.node\_connection\_timeout (integer)

Le GUC `citus.node_connection_timeout` définit la durée maximale (en millisecondes) d’attente de l’établissement de la connexion. Hyperscale (Citus) génère une erreur si le délai d’attente s’écoule avant qu’au moins une connexion Worker soit établie. Ce GUC affecte les connexions entre le coordinateur et les workers, ainsi qu’entre les workers.

-   Default : cinq secondes
-   Minimum : 10 millisecondes
-   Maximum : une heure

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>Statistiques sur les requêtes

### <a name="citusstat_statements_purge_interval-integer"></a>citus.stat\_statements\_purge\_interval (integer)

Définit la fréquence à laquelle le démon de maintenance supprime les enregistrements de [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) qui n’ont pas de correspondance pas dans `pg_stat_statements`. Cette valeur de configuration définit l’intervalle de temps entre les suppressions, en secondes, avec une valeur par défaut de 10. La valeur 0 désactive les suppressions.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Ce paramètre est effectif sur le coordinateur et peut être modifié au moment de l’exécution.

## <a name="data-loading"></a>Chargement des données

### <a name="citusmulti_shard_commit_protocol-enum"></a>citus.multi\_shard\_commit\_protocol (enum)

Définit le protocole de validation à utiliser lors de l’exécution d’une copie sur une table distribuée de hachage. À chaque sélection élective de partition individuelle, la commande COPY est effectuée dans un bloc de transaction pour garantir qu’aucune donnée n’est ingérée si une erreur se produit pendant la copie. Toutefois, il existe un cas de défaillance particulier dans lequel la commande COPY est réussie sur toutes les sélections électives, mais une défaillance (matérielle) se produit avant la validation de toutes les transactions. Ce paramètre peut être utilisé pour éviter la perte de données dans ce cas en choisissant entre les protocoles de validation suivants :

-   **2pc :** (par défaut) Les transactions dans lesquelles la copie est effectuée sur les sélections électives de partition sont d’abord préparées à l’aide d\'une [une validation en deux phases](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) de PostgreSQL puis validées. Les validations ayant échoué peuvent être récupérées ou annulées manuellement à l’aide de COMMIT PREPARED ou ROLLBACK PREPARED, respectivement.
    Lors de l’utilisation de 2pc, [max\_prepared\_transactions](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) doit être augmenté sur tous les workers, généralement sur la même valeur que le nombre maximal de connexions\_.
-   **1pc :** Les transactions dans lesquelles la commande COPY est effectuée sur les sélections électives de partition sont validées en une seule fois. Les données peuvent être perdues si une validation échoue après la fin de la copie sur toutes les sélections électives (rare).

### <a name="citusshard_replication_factor-integer"></a>citus.shard\_replication\_factor (integer)

Définit le facteur de réplication pour partitions, c’est-à-dire le nombre de nœuds sur lesquels les partitions seront placées, et la valeur par défaut est 1. Ce paramètre peut être défini au moment de l’exécution et est effectif sur le coordinateur. La valeur idéale pour ce paramètre dépend de la taille du cluster et du taux d’échec du nœud.  Par exemple, vous souhaiterez peut-être augmenter ce facteur de réplication si vous exécutez des clusters de grande taille et observez les défaillances de nœuds plus fréquemment.

### <a name="citusshard_count-integer"></a>citus.shard\_count (integer)

Définit le nombre de partitions pour les tables partitionnées par hachage et par défaut à 32.  Cette valeur est utilisée par l’UDF (fonction définie par l’utilisateur) [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) lors de la création de tables partitionnées par hachage. Ce paramètre peut être défini au moment de l’exécution et est effectif sur le coordinateur.

### <a name="citusshard_max_size-integer"></a>citus.shard\_max\_size (integer)

Définit la taille maximale que doit atteindre une partition avant qu’elle ne soit fractionnée et est par défaut de 1 Go. Lorsque la taille du fichier source (utilisée pour la mise en lots) pour une partition dépasse cette valeur de configuration, la base de données s\'assure qu’une nouvelle partition est créée. Ce paramètre peut être défini au moment de l’exécution et est effectif sur le coordinateur.

## <a name="planner-configuration"></a>Configuration du planificateur

### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus.limit\_clause\_row\_fetch\_count (integer)

Définit le nombre de lignes à extraire par tâche pour l’optimisation de la clause LIMIT.
Dans certains cas, les requêtes SELECT avec des clauses LIMIT peuvent avoir besoin d’extraire toutes les lignes de chaque tâche pour générer des résultats. Dans ces cas-là, et lorsqu’une approximation produirait des résultats significatifs, cette valeur de configuration définit le nombre de lignes à extraire à partir de chaque partition. Les approximations de limites sont désactivées par défaut et ce paramètre est définit sur -1. Cette valeur peut être définie au moment de l’exécution et est effective sur le coordinateur.

### <a name="cituscount_distinct_error_rate-floating-point"></a>citus.count\_distinct\_error\_rate (floating point)

Hyperscale (Citus) peut calculer le nombre count(distinct) approximatif à l’aide de l’extension postgresql-hll. Cette entrée de configuration définit le taux d’erreur souhaité lors du calcul du count(distinct). 0,0, qui est la valeur par défaut, désactive les approximations pour count(distinct); et 1,0 ne fournit aucune garantie quant à l’exactitude des résultats. Nous vous recommandons de définir ce paramètre sur 0,005 pour obtenir de meilleurs résultats. Cette valeur peut être définie au moment de l’exécution et est effective sur le coordinateur.

### <a name="citustask_assignment_policy-enum"></a>citus.task\_assignment\_policy (enum)

> [!NOTE]
> Ce paramètre GUC s’applique uniquement lorsque [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) est supérieur à un, ou pour les requêtes sur [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Définit la stratégie à utiliser lors de l’affectation de tâches aux workers. Le coordinateur affecte des tâches aux workers en fonction des emplacements de partition. Cette valeur de configuration spécifie la stratégie à utiliser lors de ces affectations.
Actuellement, il existe trois stratégies d’attribution de tâches possibles qui peuvent être utilisées.

-   **greedy :** La stratégie greedy est la stratégie par défaut et vise à répartir uniformément les tâches entre les workers.
-   **round-robin :** La stratégie round-robin attribue des tâches aux workers dans un mode round robin alternant entre les différents réplicas. Cette stratégie permet une meilleure utilisation du cluster lorsque le nombre de partitions pour une table est faible par rapport au nombre de workers.
-   **first-replica :** La stratégie first-replica assigne des tâches en fonction de l’ordre d’insertion des placements (réplicas) pour les partitions. En d’autres termes, la requête de fragment pour une partition est assignée au worker qui a le premier réplica de cette partition.
    Cette méthode vous permet d’avoir des garanties fortes quant à quelle partition sera utilisée sur quel nœuds (autrement dit, des garanties fichiers restant en mémoire plus fortes).

Ce paramètre peut être défini au moment de l’exécution et est effectif sur le coordinateur.

## <a name="intermediate-data-transfer"></a>Transfert des données intermédiaires

### <a name="citusbinary_worker_copy_format-boolean"></a>citus.binary\_worker\_copy\_format (boolean)

Utilisez le format de copie binaire pour transférer des données intermédiaires entre les workers.
Au cours des jointures de tables volumineuses, Hyperscale (Citus) peut être amené à repartitionner dynamiquement et à répartir les données entre différents Workers. Par défaut, ces données sont transférées au format texte. L’activation de ce paramètre indique à la base de données d’utiliser le format de sérialisation binaire PostgreSQL pour transférer ces données. Ce paramètre est effectif sur les Workers et doit être modifié dans le fichier PostgreSQL. conf. Après avoir modifié le fichier de configuration, les utilisateurs peuvent envoyer un signal SIGHUP ou redémarrer le serveur pour que cette modification prenne effet.

### <a name="citusbinary_master_copy_format-boolean"></a>citus.binary\_master\_copy\_format (booléen)

Utilisez le format de copie binaire pour transférer des données entre les coordinateurs et les Workers. Lors de l’exécution de requêtes distribuées, les Workers transfèrent leurs résultats intermédiaires au coordinateur pour l’agrégation finale. Par défaut, ces données sont transférées au format texte. L’activation de ce paramètre indique à la base de données d’utiliser le format de sérialisation binaire PostgreSQL pour transférer ces données.
Ce paramètre peut être défini au moment de l’exécution et est effectif sur le coordinateur.

### <a name="citusmax_intermediate_result_size-integer"></a>citus.max\_intermediate\_result\_size (integer)

Taille maximale, en Ko, des résultats intermédiaires pour les expressions de table commune qui ne peuvent pas être transférées vers les nœuds Worker pour exécution, et pour les sous-requêtes complexes. La valeur par défaut est 1 Go et la valeur -1 signifie aucune limite.
Les requêtes qui dépassent la limite sont annulées et génèrent un message d’erreur.

## <a name="ddl"></a>DDL

### <a name="citusenable_ddl_propagation-boolean"></a>citus.enable\_ddl\_propagation (booléen)

Spécifie s’il faut propager automatiquement les modifications DDL du coordinateur à tous les Workers. La valeur par défaut est true. Étant donné que certaines modifications de schéma nécessitent un verrou d’accès exclusif sur les tables et que la propagation automatique s’applique à tous les traitements de manière séquentielle, elle peut rendre un cluster Hyperscale (Citus) temporairement moins réactif. Vous pouvez choisir de désactiver ce paramètre et de propager les modifications manuellement.

## <a name="executor-configuration"></a>Configuration de l’exécuteur

### <a name="general"></a>Général

#### <a name="citusall_modifications_commutative"></a>citus.all\_modifications\_commutative

Hyperscale (Citus) applique les règles de commutativité et acquiert les verrous appropriés pour les opérations de modification afin de garantir l’exactitude du comportement. Par exemple, il suppose qu’une instruction INSERT est commutative avec une autre instruction INSERT, mais pas avec une instruction UPDATE ou DELETE. De même, il suppose qu’une instruction UPDATE ou DELETE ne commute pas avec une autre instruction UPDATE ou DELETE. Cette précaution signifie que les instructions UPDATE et DELETE nécessitent Hyperscale (Citus) pour acquérir des verrous plus forts.

Si vous avez des instructions UPDATE qui sont commutatives avec vos INSERT ou d’autres UPDATE, vous pouvez assouplir ces hypothèses de commutativité en affectant à ce paramètre la valeur true. Lorsque ce paramètre est défini sur true, toutes les commandes sont considérées comme commutatives et revendiquent un verrou partagé, ce qui peut améliorer le débit global. Ce paramètre peut être défini au moment de l’exécution et est effectif sur le coordinateur.

#### <a name="citusremote_task_check_interval-integer"></a>citus.remote\_task\_check\_interval (integer)

Définit la fréquence à laquelle Hyperscale (Citus) vérifie les états des tâches gérés par l’exécuteur de suivi de tâches. La valeur par défaut est 10 ms. Le coordinateur attribue des tâches aux Workers, puis vérifie régulièrement la progression de chaque tâche. Cette valeur de configuration définit l’intervalle de temps entre deux contrôles. Ce paramètre est effectif sur le coordinateur et peut être défini au moment de l’exécution.

#### <a name="citustask_executor_type-enum"></a>citus.task\_executor\_type (enum)

Hyperscale (Citus) a trois types d’exécuteur pour exécuter des requêtes SELECT distribuées.  L’exécuteur souhaité peut être sélectionné en définissant ce paramètre de configuration. Les valeurs acceptables pour ce paramètre sont :

-   **adaptive :** Valeur par défaut. Elle est optimale pour les réponses rapides aux requêtes qui impliquent des agrégations et des jointures en colocation s’étendant sur plusieurs partitions.
-   **task-tracker :** L’exécuteur de suivi des tâches est adapté aux requêtes durables et complexes qui nécessitent une réutilisation aléatoire des données sur les nœuds Worker et une gestion efficace des ressources.
-   **real-time :** (déconseillé) A un objectif similaire à celui de l’exécuteur adaptatif, mais il est moins flexible et peut entraîner une plus grande sollicitation des connexions sur les nœuds Worker.

Ce paramètre peut être défini au moment de l’exécution et est effectif sur le coordinateur.

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus.multi\_task\_query\_log\_level (enum) {#multi_task_logging}

Définit un niveau de journal pour toute requête qui génère plus d’une tâche (c’est-à-dire, qui atteint plusieurs partitions). La journalisation est utile lors d’une migration d’applications multi-entité, car vous pouvez choisir d’émettre des erreurs ou des avertissements pour de telles requêtes, pour les trouver et leur ajouter un filtre client\_id- Ce paramètre peut être défini au moment de l’exécution et est effectif sur le coordinateur. La valeur par défaut de ce paramètre est \'off\'.

Les valeurs prises en charge pour cette enum sont les suivantes :

-   **off :** Désactivez la journalisation des requêtes qui génèrent plusieurs tâches (autrement dit, s’étendent sur plusieurs partitions)
-   **debug :** Journalise l’instruction au niveau de gravité DEBUG.
-   **log :** Journalise l’instruction au niveau de gravité LOG. La ligne de journal inclut la requête SQL qui a été exécutée.
-   **notice :** Journalise l’instruction au niveau de gravité NOTICE.
-   **warning :** Journalise l’instruction au niveau de gravité WARNING.
-   **error :** Journalise l’instruction au niveau de gravité ERROR.

Il peut être utile d’utiliser `error` lors des tests de développement, ainsi qu’un niveau de journalisation plus bas comme `log` au cours du déploiement réel.
Si vous choisissez `log` , les requêtes multitâches apparaitront dans les journaux de base de données avec la requête elle-même affichée après \"STATEMENT.\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

#### <a name="citusenable_repartition_joins-boolean"></a>citus.enable\_repartition\_joins (booléen)

En règle générale, la tentative d’exécution de jointures de répartition avec l’exécuteur adaptatif échoue avec un message d’erreur.  Toutefois, l’affectation de la valeur true à `citus.enable_repartition_joins` permet à Hyperscale (Citus) de basculer temporairement dans l’exécuteur de suivi des tâches pour effectuer la jointure.  La valeur par défaut est false.

### <a name="task-tracker-executor-configuration"></a>Configuration de l’exécuteur du suivi des tâches

#### <a name="citustask_tracker_delay-integer"></a>citus.task\_tracker\_delay (integer)

Ce paramètre définit la durée de veille du suivi des tâches entre les phases de gestion des tâches et la valeur par défaut de 200 ms. Le processus du suivi des tâches se réveille régulièrement, passe en revue toutes les tâches qui lui sont affectées et planifie et exécute ces tâches.  Ensuite, le suivi des tâches se met en veille pendant une période de temps avant de parcourir ces tâches à nouveau.
Cette valeur de configuration détermine la durée de cette période de sommeil. Ce paramètre est effectif sur les Workers et doit être modifié dans le fichier PostgreSQL. conf. Après avoir modifié le fichier de configuration, les utilisateurs peuvent envoyer un signal SIGHUP ou redémarrer le serveur pour que cette modification prenne effet.

Ce paramètre peut être réduit pour diminuer le délai dû à l’exécuteur du suivi des tâches en réduisant l’intervalle de temps entre les phases de gestion.
La diminution du délai est utile dans les cas où les requêtes de partition sont courtes et, par conséquent, mettent régulièrement à jour leur état.

#### <a name="citusmax_assign_task_batch_size-integer"></a>citus.max\_assign\_task\_batch\_size (integer)

L’exécuteur du suivi des tâches sur le coordinateur assigne de façon synchrone des tâches par lots au démon sur les Workers. Ce paramètre définit le nombre maximal de tâches à assigner dans un lot unique. Le choix d’une taille de lot plus importante permet une attribution plus rapide des tâches. Toutefois, si le nombre de Workers est élevé, cela peut prendre plus de temps pour que tous les Workers obtiennent des tâches.  Ce paramètre peut être défini au moment de l’exécution et est effectif sur le coordinateur.

#### <a name="citusmax_running_tasks_per_node-integer"></a>citus.max\_running\_tasks\_per\_node (integer)

Le processus du suivi des tâches planifie et exécute les tâches qui lui sont assignées, le cas échéant. Cette valeur de configuration définit le nombre maximal de tâches à exécuter simultanément sur un nœud à tout moment donné et la valeur par défaut est 8.

La limite vous permet de ne pas avoir de nombreuses tâches sur le disque en même temps et d\'éviter la contention I/O de disque. Si vos requêtes sont servies à partir de mémoire ou de disques SSD, vous pouvez augmenter max\_running\_tasks\_per\_node sans grand problème.

#### <a name="cituspartition_buffer_size-integer"></a>citus.partition\_buffer\_size (integer)

Définit la taille de la mémoire tampon à utiliser pour les opérations de partition et la valeur par défaut est de 8 Mo.
Hyperscale (Citus) permet de repartitionner les données de table en plusieurs fichiers lorsque deux tables volumineuses sont jointes. Une fois cette mémoire tampon remplie, les données de nouveau partitionnées sont vidées dans des fichiers sur le disque.  Cette entrée de configuration peut être définie au moment de l’exécution et est effective sur les Workers.

### <a name="explain-output"></a>Sortie explain

#### <a name="citusexplain_all_tasks-boolean"></a>citus.explain\_all\_tasks (booléen)

Par défaut, Hyperscale (Citus) affiche le résultat d’une tâche arbitraire unique lors de l’exécution d’[EXPLAIN](http://www.postgresql.org/docs/current/static/sql-explain.html) sur une requête distribuée. Dans la plupart des cas, le résultat d’explain sera similaire entre les tâches. Parfois, certaines tâches sont planifiées différemment ou ont des durées d’exécution bien supérieures. Dans ce cas, il peut être utile d’activer ce paramètre, après quoi le résultat EXPLAIN inclura toutes les tâches. L’appliquer à toutes les tâches peut entraîner un délai plus long pour EXPLAIN.

## <a name="next-steps"></a>Étapes suivantes

* Les [options de configuration](concepts-hyperscale-configuration-options.md)de ressource d’un groupe de serveur Hyperscale (Citus) sont une autre forme de configuration.
* La base de données PostgreSQL sous-jacente a également des [paramètres de configuration](http://www.postgresql.org/docs/current/static/runtime-config.html).
