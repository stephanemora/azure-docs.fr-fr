---
title: Résoudre les problèmes de latence de réplication - Azure Database pour MySQL
description: Découvrez comment résoudre les problèmes de latence de réplication avec des réplicas en lecture Azure Database pour MySQL
keywords: mysql, résoudre des problèmes, latence de réplication en secondes
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876807"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Résoudre les problèmes de latence de réplication dans Azure Database pour MySQL

La fonctionnalité de [réplica en lecture](concepts-read-replicas.md) vous permet de répliquer les données d’un serveur Azure Database pour MySQL sur un serveur réplica en lecture seule. Les réplicas en lecture sont utilisés pour effectuer un scale-out de la charge de travail en acheminant des requêtes de lecture/rapport de l’application vers des serveurs réplicas. Cela réduit la pression sur le serveur principal et améliore les performances globales et la latence de l’application au fur et à mesure qu’elle est mise à l’échelle. Les réplicas sont mis à jour de manière asynchrone à l’aide de la technologie de réplication selon la position du fichier journal binaire (binlog) native au moteur MySQL. Pour en savoir plus sur la réplication binlog, consultez la [vue d’ensemble de la réplication binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

Le décalage de réplication sur les réplicas en lecture secondaires dépend du nombre de facteurs, notamment, mais sans s’y limiter 

- Latence du réseau
- Volume de transactions sur le serveur source
- Niveau de calcul du serveur source et réplica en lecture secondaire
- Requêtes en cours d’exécution sur le serveur principal et le serveur secondaire. 

Dans ce document, vous allez découvrir comment résolution des problèmes liés à la latence de réplication dans Azure Database pour MySQL. Vous allez également comprendre certaines des causes courantes de l’augmentation de la latence de réplication sur les serveurs réplicas.

## <a name="replication-concepts"></a>Concepts de réplication

Lorsque le journal binaire est activé, le serveur source écrit la transaction validée dans le journal binaire qui est utilisé pour la réplication. Le journal binaire est activé par défaut pour tous les serveurs nouvellement approvisionnés qui prennent en charge jusqu’à 16 To de stockage. Sur les serveurs réplicas, deux threads s’exécutent par serveur réplica, l’un appelé le thread d’E/S et l’autre le thread SQL.

- Le **thread d’E/S** se connecte au serveur source et demande les journaux binaires mis à jour. Une fois que ce thread a reçu les mises à jour du journal binaire, celles-ci sont enregistrées sur un serveur réplica dans un journal local appelé le journal de relais.
- Le **thread SQL** lit le journal de relais et applique la ou les modifications de données sur les serveurs réplicas.

## <a name="monitoring-replication-latency"></a>Surveillance de la latence de réplication

Azure Database pour MySQL fournit la métrique Décalage de la réplication en secondes dans [Azure Monitor](concepts-monitoring.md). Cette métrique est disponible sur les serveurs réplicas uniquement. Cette métrique est calculée à l’aide de la métrique seconds_behind_master disponible dans MySQL. Pour comprendre la cause racine de l’augmentation de la latence de réplication, connectez-vous au serveur réplica à l’aide de [MySQL Workbench](connect-workbench.md) ou d’[Azure Cloud Shell](https://shell.azure.com) et exécutez la commande suivante :

 Remplacez les valeurs par le nom réel de votre serveur réplica et le nom de la connexion d’utilisateur administrateur. Le nom d’utilisateur administrateur requiert « @\<servername> » pour Azure Database pour MySQL :

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Voici comment se présente l’expérience utilisateur dans le terminal Cloud Shell
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  Dans le même terminal Azure Cloud Shell, exécutez la commande suivante

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  Une sortie typique se présente comme suit :
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Surveillance de la latence de réplication":::


La sortie contient un grand nombre d’informations mais, en règle générale, il est important de se concentrer sur les colonnes suivantes :

|Métrique|Description|
|---|---|
|Slave_IO_State| État actuel du thread d’E/S. L’état est généralement « En attente de l’envoi d’un événement par le maître » s’il est en cours de synchronisation. Toutefois, si vous voyez un état tel que « Connexion au maître », cela signifie que le réplica a perdu la connexion au serveur maître. Vérifiez si le serveur maître est en cours d’exécution ou si un pare-feu bloque la connexion.|
|Master_Log_File| Fichier journal binaire dans lequel le maître écrit.|
|Read_Master_Log_Pos| Représente la position dans le fichier journal binaire ci-dessus dans lequel le maître écrit.|
|Relay_Master_Log_File| Représente le fichier journal binaire que le serveur réplica lit à partir du maître.|
|Slave_IO_Running| Indique si le thread d’E/S est en cours d’exécution. La valeur doit être « Oui ». Si la valeur est « Non », la réplication est probablement interrompue.|
|Slave_SQL_Running| Indique si le thread SQL est en cours d’exécution. La valeur doit être « Oui ». Si la valeur est « Non », la réplication est probablement interrompue.|
|Exec_Master_Log_Pos| Affiche la position de Relay_Master_Log_File que le réplica applique. En cas de latence, cette séquence de position doit être inférieure à Read_Master_Log_Pos.|
|Relay_Log_Space|Affiche la limite supérieure de la taille du journal de relais. Vous pouvez vérifier la taille en interrogeant des variables globales telles que « relay_log_space_limit ».|
|Seconds_Behind_Master| Affiche la latence de réplication en secondes.|
|Last_IO_Errno|Affiche le code d’erreur de thread d’E/S, le cas échéant. Pour plus d’informations sur ces codes, reportez-vous à la [documentation MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_IO_Error| Affiche le message d’erreur de thread d’E/S, le cas échéant.|
|Last_SQL_Errno|Affiche le code d’erreur de thread SQL, le cas échéant. Pour plus d’informations sur ces codes, reportez-vous à la [documentation MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Affiche le message d’erreur de thread SQL, le cas échéant.|
|Slave_SQL_Running_State| Indique l’état actuel du thread SQL. Notez que le message « Verrouillage du système » affiché dans cet état est un comportement normal. Il est normal de voir l’état « En attente de validation de transaction dépendante ». Celui-ci indique que le réplica attend que le maître mette à jour les transactions validées.|

Si Slave_IO_Running a la valeur Oui et que Slave_SQL_Running a la valeur Oui, la réplication s’exécute bien. 

Vous devez ensuite vérifier les valeurs Last_IO_Errno, Last_IO_Error, Last_SQL_Errno et Last_SQL_Error.  Ces champs contiennent le numéro d’erreur et le message d’erreur de l’erreur la plus récente qui a provoqué l’arrêt du thread SQL. Un numéro d’erreur 0 et un message vide signifient qu’il n’y a pas d’erreur. Une valeur différente de zéro dans l’erreur doit être examinée de manière plus approfondie en recherchant le code d’erreur dans la [documentation MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Scénarios courants de latence de réplication élevée

### <a name="network-latency-or-high-cpu-on-source-server"></a>Latence du réseau ou processeur élevé sur le serveur source

Si vous observez les valeurs suivantes, la cause la plus courante de la latence de réplication est une latence du réseau élevée ou une consommation élevée du processeur sur le serveur source. Dans ce cas, le thread d’E/S est en cours d’exécution et en attente sur le maître. Le maître (serveur source) a déjà écrit 20 lignes dans le fichier journal binaire alors que le réplica n’a reçu que jusqu’à 10 fichiers. Les principaux facteurs de latence de réplication élevée dans ce scénario sont la vitesse du réseau ou une utilisation élevée du processeur sur le serveur source.  Dans Azure, la latence du réseau au sein d’une région est généralement de l’ordre de millisecondes peut atteindre quelques secondes entre les régions. Dans la plupart des cas, le retard de connexion au serveur source dans le thread d’E/S est dû à une utilisation élevée du processeur sur le serveur source, ce qui entraîne un traitement lent du thread d’E/S. Cela peut être détecté en surveillant l’utilisation du processeur et en observant le nombre de connexions simultanées sur le serveur source à l’aide d’Azure Monitor.

Si vous ne voyez pas une utilisation élevée du processeur sur le serveur source, la latence du réseau peut être une cause possible. Si vous constatez une latence réseau anormalement et soudainement élevée, nous vous recommandons de consulter la [page de l’état Azure](https://status.azure.com/status) pour vous assurer qu’il n’y a pas de problèmes ou de pannes non connus. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>Forte rafale de transactions sur le serveur source

Si vous observez les valeurs suivantes, la cause la plus courante de la latence de réplication est une forte rafale de transactions sur le serveur source. Dans la sortie ci-dessous, bien que le réplica puisse récupérer le journal binaire derrière le maître, le thread d’E/S du réplica indique que l’espace du journal de relais est déjà plein. La vitesse du réseau n’est donc pas à l’origine du retard, car le réplica a déjà essayé de rattraper le plus rapidement possible. Au lieu de cela, la taille du journal binaire mis à jour dépasse la limite supérieure de l’espace du journal de relais. Pour résoudre ce problème, le [journal des requêtes lentes](concepts-server-logs.md) doit être activé sur le serveur maître. Les journaux des requêtes lentes vous permettent d’identifier les transactions longues sur le serveur source. Les requêtes identifiées doivent être paramétrées pour réduire la latence sur le serveur. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Voici les causes courantes de la latence dans cette catégorie :

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>Latence de réplication due au chargement charge de données sur le serveur source
Dans certains cas, il existe des chargements de données hebdomadaires ou mensuels sur les serveurs sources. Malheureusement, la latence de réplication est inévitable dans ce cas. Dans ce scénario, les serveurs réplicas finissent par rattraper une fois le chargement de données sur le serveur source terminé.


### <a name="slowness-on-the-replica-server"></a>Lenteur du serveur réplica

Si vous observez les valeurs suivantes, la cause la plus courante peut être un problème sur le serveur réplica qui nécessite une investigation plus approfondie. Dans ce scénario, comme indiqué dans la sortie, les threads d’E/S et SQL s’exécutent correctement et le réplica lit le même fichier journal binaire que celui que le maître écrit. Toutefois, une certaine latence se produit sur le serveur réplica pour refléter la même transaction du serveur source. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

Voici les causes courantes de la latence dans cette catégorie :

#### <a name="no-primary-or-unique-key-on-a-table"></a>Aucune clé primaire ou unique sur une table

Azure Database pour MySQL utilise la réplication basée sur les lignes. Avec la réplication basée sur les lignes, le serveur maître écrit des événements dans le journal binaire sur la modification de ligne de table individuelle. Le thread SQL applique à son tour ces modifications aux lignes de table correspondantes sur le serveur réplica. Aucune clé primaire ou unique sur une table n’est l’une des causes courantes de la latence de réplication. L’absence de clés primaires ou uniques provoque l’analyse de toutes les lignes de la table cible par le thread SQL pour appliquer les modifications.

Dans MySQL, la clé primaire est un index associé qui garantit des performances des requêtes rapides, car il ne peut pas inclure de valeurs NULL. Avec le moteur de stockage InnoDB, les données de la table sont organisées physiquement pour effectuer des recherches ultra-rapides et être triées en fonction de la clé primaire. Par conséquent, il est recommandé d’ajouter une clé primaire sur les tables du serveur source avant de créer le serveur réplica. Dans ce scénario, vous devez ajouter des clés primaires sur le serveur source et recréer des réplicas en lecture pour améliorer la latence de réplication.

Vous pouvez utiliser la requête suivante pour déterminer les tables dont la clé primaire est manquante sur le serveur source :

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>Latence de réplication due à des requêtes longues sur le serveur réplica

Il est possible que la charge de travail sur le serveur réplica empêche le thread SQL de suivre le thread d’E/S. C’est l’une des causes courantes de latence de réplication élevée en cas de requête longue sur le serveur réplica. Dans ce cas, le [journal des requêtes lentes](concepts-server-logs.md) doit être activé sur le serveur réplica pour aider à résoudre le problème. Les requêtes lentes peuvent augmenter l’utilisation ressources ou ralentir le serveur de telle sorte que le réplica ne soit pas en mesure de rattraper le maître. Dans ce scénario, vous devez paramétrer les requêtes lentes. Des requêtes plus rapides empêchent le blocage du thread SQL et améliorent considérablement la latence de réplication.


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>Latence de réplication due à des requêtes DDL sur le serveur source
Une commande DDL longue telle que [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) est exécutée sur le serveur source et elle indique une durée d’exécution d’1 heure. Pendant ce temps, des milliers d’autres requêtes peuvent s’exécuter en parallèle sur le serveur source. Lorsque le DDL est répliqué sur le réplica, pour garantir la cohérence de la base de données, le moteur MySQL doit exécuter le DDL dans un thread de réplication unique. Toutes les autres requêtes répliquées seront alors bloquées et devront attendre une heure ou plus jusqu’à ce que l’opération DDL soit terminée sur le serveur réplica. Cela est vrai indépendamment de l’opération DDL en ligne. Avec les opérations DDL, la réplication est censée voir une latence de réplication supérieure.

Si le [journal des requêtes lentes](concepts-server-logs.md) est activé sur le serveur source, ce scénario peut être détecté en consultant les journaux des requêtes lentes afin de déterminer si une commande DDL a été exécutée sur le serveur source. Bien que la suppression, le changement de nom et la création d’index doivent utiliser l’algorithme INPLACE pour ALTER TABLE, ceci peut impliquer la copie des données de la table, puis la régénération de la table. En règle générale, le DML simultané de l’algorithme INPLACE est pris en charge, mais un verrou de métadonnées exclusif sur la table peut être appliqué temporairement pendant les phases de préparation et d’exécution de l’opération. Ainsi, pour l’instruction CREATE INDEX, les clauses ALGORITHM et LOCK peuvent être utilisées pour influencer la méthode de copie de table et le niveau de concurrence pour la lecture et l’écriture, mais l’ajout d’un index FULLTEXT ou SPATIAL empêchera toujours les opérations DML. Voici un exemple de création d’un index avec des clauses ALGORITHM et LOCK :

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Malheureusement, pour une instruction DDL qui requiert un verrou, la latence de réplication ne peut pas être évitée. Au lieu de cela, ces types d’opérations DDL doivent être exécutés pendant les heures creuses, par exemple la nuit, afin de réduire l’impact potentiel.

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>Latence de réplication due à une référence SKU inférieure du serveur réplica

Dans Azure Database pour MySQL, les réplicas en lecture sont créés avec la même configuration de serveur que le serveur maître. La configuration du serveur réplica peut être modifiée après la création de ce dernier. Toutefois, si le serveur réplica est rétrogradé, la charge de travail peut entraîner une plus grande consommation de ressources qui, à son tour, peut entraîner une latence de réplication. Cela peut être observé en surveillant l’utilisation du processeur et de la mémoire du serveur réplica à partir d’Azure Monitor. Dans ce scénario, il est recommandé de maintenir la configuration du serveur réplica à des valeurs égales ou supérieures à celles du serveur source pour garantir que le réplica sera à la hauteur du serveur maître.

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>Amélioration de la latence de réplication à l’aide du réglage des paramètres serveur sur le serveur source

Dans Azure Database pour MySQL, la réplication est optimisée pour s’exécuter par défaut avec des threads parallèles sur les réplicas. Pour les grands nombres de charges de travail simultanées sur le serveur source sur lequel le serveur réplica ne parvient pas à rattraper le problème, la latence de réplication peut être améliorée en configurant le paramètre binlog_group_commit_sync_delay sur le serveur source. Ce paramètre contrôle la durée en microsecondes de l’attente de la validation du journal binaire avant la synchronisation du fichier journal binaire. L’avantage est que, au lieu d’appliquer immédiatement chaque transaction validée, le maître envoie les mises à jour de journal binaire en bloc. Cela réduit les E/S sur le réplica et contribue à améliorer les performances. Dans ce scénario, il peut être utile de définir binlog_group_commit_sync_delay sur 1000 ou autre et de surveiller la latence de réplication. Ce paramètre doit être défini avec prudence et utilisé pour les grands nombres de charges de travail simultanées uniquement. Dans un scénario de faible concurrence avec de nombreuses transactions singleton, la définition de binlog_group_commit_sync_delay peut ajouter à la latence, car le thread d’E/S attend des mises à jour du journal binaire en bloc alors que seules quelques transactions peuvent être validées. 

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la [Vue d’ensemble de la réplication binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
