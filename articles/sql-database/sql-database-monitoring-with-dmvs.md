---
title: Analyse de base de données SQL Azure à l’aide de vues de gestion dynamique | Microsoft Docs
description: Apprenez à détecter et à diagnostiquer des problèmes de performances courants à l’aide de vues de gestion dynamique pour surveiller une base de données SQL Microsoft Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 08/08/2018
ms.openlocfilehash: 97907eee9982fdf6a804bc13edbf8c14efa4ce42
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161384"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Analyse d’une base de données SQL Azure à l’aide de vues de gestion dynamique
La Base de données SQL Microsoft Azure active un sous-ensemble de vues de gestion dynamique permettant de diagnostiquer des problèmes de performances qui peuvent être causés par des requêtes bloquées ou longues, des goulots d’étranglement des ressources, des plans de requête médiocres, et ainsi de suite. Cette rubrique fournit des informations sur la façon de détecter des problèmes de performances courants à l’aide des vues de gestion dynamique.

La base de données SQL prend partiellement en charge trois catégories de vues de gestion dynamique :

* vues de gestion dynamique liées à la base de données ;
* vues de gestion dynamique liées à l’exécution ;
* vues de gestion dynamique liées à la transaction.

Pour plus d’informations sur les vues de gestion dynamique, voir [Fonctions et vues de gestion dynamique (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) dans la documentation en ligne de SQL Server.

## <a name="permissions"></a>Autorisations
Dans la base de données SQL, l’interrogation d’une vue de gestion dynamique nécessite des autorisations **AFFICHER L'ÉTAT DE LA BASE DE DONNÉES** . L’autorisation **AFFICHER L'ÉTAT DE LA BASE DE DONNÉES** renvoie des informations sur tous les objets de la base de données active.
Pour accorder l’autorisation **AFFICHER L'ÉTAT DE LA BASE DE DONNÉES** à un utilisateur de base de données spécifique, exécutez la requête suivante :

```GRANT VIEW DATABASE STATE TO database_user; ```

Dans une instance de SQL Server local, des vues de gestion dynamiques renvoient des informations sur l’état du serveur. Dans Base de données SQL, elles renvoient des informations relatives à votre base de données logique actuelle.

## <a name="calculating-database-size"></a>Calcul de la taille de la base de données
La requête suivante renvoie la taille de votre base de données en mégaoctets :

```
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

La requête suivante retourne la taille d’objets individuels (en mégaoctets) dans votre base de données :

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Analyse des connexions
La vue [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) permet de récupérer des informations sur les connexions établies avec un serveur de base de données SQL Azure spécifique et les détails de chaque connexion. En outre, la vue [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) permet de récupérer des informations sur toutes les connexions utilisateur et tâches internes actives.
La requête suivante récupère des informations sur la connexion en cours :

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Lorsque vous exécutez **sys.dm_exec_requests** et **sys.dm_exec_sessions views**, si vous disposez d’une autorisation **AFFICHER L’ÉTAT DE LA BASE DE DONNÉES** sur la base de données, vous voyez toutes les sessions en cours d’exécution sur la base de données. Sinon, vous voyez uniquement la session en cours.
> 
> 

## <a name="monitor-resource-use"></a>Surveiller l’utilisation des ressources

Vous pouvez surveiller l’utilisation des ressources à l’aide de [SQL Database Query Performance Insight](sql-database-query-performance.md) et du [magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx).

Vous pouvez également surveiller l’utilisation à l’aide de ces deux vues :

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Vous pouvez utiliser la vue [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dans chaque SQL Database. La vue **sys.dm_db_resource_stats** représente les données d’utilisation récente des ressources par rapport au niveau de service. Les pourcentages moyens d’UC, d’E/S des données, d’écritures du journal et de mémoire sont enregistrés toutes les 15 secondes et conservés pendant une heure.

Étant donné que cette vue fournit un aperçu plus granulaire de l’utilisation des ressources, utilisez d’abord **sys.dm_db_resource_stats** pour n’importe quelle analyse d’état actuel ou pour la résolution des problèmes. Par exemple, cette requête affiche l’utilisation moyenne et maximale des ressources pour la base de données actuelle sur la dernière heure :

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data IO in percent',
        MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Pour les autres requêtes, consultez les exemples dans [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats
La vue [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) de la base de données **master** fournit des informations supplémentaires vous permettant de superviser les performances de votre base de données SQL par rapport à son niveau de service et à sa taille de calcul. Les données, qui sont collectées toutes les cinq minutes, sont conservées pendant environ 14 jours. Cette vue est utile pour une analyse historique de plus long terme sur l’utilisation des ressources par votre base de données SQL.

Le graphique suivant illustre l’utilisation des ressources d’UC pour une base de données Premium avec la taille de calcul P2 pour chaque heure de la semaine. Ce graphique spécifique commence un lundi, affiche 5 journées de travail, puis un week-end où l’application connaît une activité réduite.

![Utilisation des ressources de base de données SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

D’après les données, cette base de données présente actuellement une charge d’UC maximale légèrement supérieure à 50 % de l’utilisation de l’UC, par rapport à la taille de calcul P2 (le mardi, à la mi-journée). Si l’UC est le facteur dominant dans le profil de ressource de l’application, vous pouvez décider que P2 est la taille de calcul permettant de garantir que la charge de travail est toujours adaptée. Si vous prévoyez une croissance de l’application au fil du temps, vous avez tout intérêt à avoir une mémoire-tampon de ressources supplémentaires, afin que l’application n’atteigne jamais le plafond de performances. En augmentant la taille de calcul, vous pouvez éviter les erreurs visibles par les clients, qui sont susceptibles de se produire lorsqu’une application ne dispose pas de la puissance nécessaire pour traiter efficacement les requêtes, plus particulièrement dans les environnements sensibles à la latence. Comme exemple, considérons une base de données qui prend en charge une application remplissant les pages web en fonction des résultats des appels de la base de données.

D’autres types d’applications peuvent interpréter différemment le même graphique. Par exemple, si une application essaie de traiter les données de paie chaque jour et obtient le même graphique, ce genre de modèle de « traitement par lot » peut convenir avec une taille de calcul P1. La taille de calcul P1 comprend 100 DTU, contre 200 DTU pour la taille de calcul P2. La taille de calcul P1 fournit la moitié des performances de la taille de calcul P2. Par conséquent, 50 % d’utilisation de l’UC au niveau P2 correspond à 100 % d’utilisation de l’UC au niveau de performance P1. Si l’application n’a pas de délai d’expiration, le fait qu’une tâche volumineuse s’exécute en 2 h ou 2 h 30 peut ne pas avoir d’importance à condition qu’elle soit effectuée le jour même. Une application de cette catégorie peut probablement utiliser une taille de calcul P1. Vous pouvez tirer parti du fait qu’il y a des périodes pendant la journée où l’utilisation des ressources est moindre, ce qui signifie que toute période de pointe peut déborder sur l’un des creux plus tard dans la journée. La taille de calcul P1 peut convenir pour une application de ce type (et permettre de réaliser des économies) tant que les travaux peuvent se terminer à temps chaque jour.

La SQL Database Azure expose les informations sur les ressources utilisées pour chaque base de données active dans la vue **sys.resource_stats** de la base de données **master** dans chaque serveur. Les données de la table sont agrégées par intervalle de 5 minutes. Avec les niveaux de service De base, Standard et Premium, les données peuvent prendre plus de 5 minutes pour apparaître dans la table, ce qui signifie qu’elles conviennent mieux aux analyses historiques qu’aux analyses en temps réel. L’interrogation de la vue **sys.resource_stats** affiche l’historique récent d’une base de données et confirme si la réservation sélectionnée a fourni la performance souhaitée lorsque c’était nécessaire.

> [!NOTE]
> Vous devez être connecté à la base de données **master** de votre serveur SQL Database logique pour interroger **sys.resource_stats** dans les exemples suivants.
> 
> 

Cet exemple illustre l’exposition des données dans cette vue :

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![La vue de catalogue sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

L’exemple suivant vous montre différentes manières d’utiliser la vue du catalogue **sys.resource_stats** pour obtenir des informations sur l’utilisation des ressources par votre SQL Database :

1. Pour consulter l’utilisation des ressources pour la base de données userdb1 au cours de la semaine passée, exécutez cette requête :
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Afin d’évaluer l’adéquation entre votre charge de travail et la taille de calcul, vous devez étudier les différents aspects des métriques de ressources : UC, lectures, écritures, nombre de Workers et nombre de sessions. Voici une requête modifiée à l’aide de **sys.resource_stats** afin d’indiquer les valeurs moyennes et maximales de ces mesures de ressources :
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Avec les informations ci-dessus relatives aux valeurs moyennes et maximales de chaque métrique de ressources, vous pouvez évaluer l’adéquation entre votre charge de travail et la taille de calcul que vous avez choisie. En général, les valeurs moyennes de **sys.resource_stats** vous offrent une bonne référence à utiliser par rapport à la taille cible. Elles doivent constituer votre principale jauge de mesure. Par exemple, vous pouvez utiliser le niveau de service Standard avec la taille de calcul S2. Les pourcentages d’utilisation moyens pour les lectures et écritures d’UC et d’E/S se situent en deçà de 40 %, le nombre moyen de Workers est inférieur à 50 et le nombre moyen de sessions est inférieur à 200. Votre charge de travail peut être prise en charge par la taille de calcul S1. Il est facile de voir si votre base de données s’intègre dans les limites de Workers et de sessions. Pour voir si une base de données peut s’adapter à une taille de calcul inférieure en ce qui concerne l’UC, les lectures et les écritures, divisez le nombre de DTU de la taille de calcul inférieure par le nombre de DTU de votre taille de calcul actuelle, puis multipliez le résultat par 100 :
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Le résultat correspond à la différence de performances relative entre les deux tailles de calcul en pourcentage. Si votre utilisation des ressources ne dépasse pas ce résultat, votre charge de travail peut être traitée par la taille de calcul inférieure. Toutefois, vous devez également examiner toutes les plages de valeurs d’utilisation des ressources, et déterminer, d’après le pourcentage, combien de fois la charge de travail de votre base de données pourrait s’adapter à la taille de calcul inférieure. La requête suivante génère le pourcentage d’adéquation par dimension de ressource, selon le seuil de 40 % calculé dans cet exemple :
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    En fonction du niveau de service de votre base de données, vous pouvez décider si votre charge de travail peut s’adapter à la taille de calcul inférieure. Si l’objectif de charge de travail de votre base de données est de 99,9 % et que la requête précédente retourne des valeurs supérieures à 99,9 % pour les trois dimensions de ressources, il est probable que votre charge de travail puisse s’adapter à la taille de calcul inférieure.
   
    En examinant le pourcentage d’adéquation, vous savez par ailleurs si vous devez passer à la taille de calcul supérieure pour atteindre votre objectif. Par exemple, l’utilisation d’UC suivante est indiquée pour la base de données userdb1 pour la semaine passée :
   
   | Pourcentage moyen d’UC | Pourcentage maximum d’UC |
   | --- | --- |
   | 24,5 |100,00 |
   
    Le pourcentage moyen d’UC représente environ un quart de la limite de la taille de calcul, ce qui correspondrait bien à la taille de calcul de la base de données. Toutefois, la valeur maximale montre que la base de données atteint la limite de la taille de calcul. Devez-vous passer à la taille de calcul supérieure ? Regardez le nombre de fois où votre charge de travail atteint 100 % et comparez ce chiffre à l’objectif de charge de travail de votre base de données.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Si cette requête retourne une valeur inférieure à 99,9 % pour l’une des trois dimensions de ressources, passez à la taille de calcul supérieure, ou employez des techniques de paramétrage pour l’application afin de réduire la charge sur la base de données SQL.
4. Cet exercice tient également compte de l’augmentation prévue de votre charge de travail à l’avenir.

Pour les pools élastiques, vous pouvez surveiller des bases de données individuelles dans le pool avec les techniques décrites dans cette section. Mais vous pouvez également surveiller le pool dans son ensemble. Pour plus d’informations, consultez l’article [Surveiller et gérer un pool élastique](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Nombre maximal de requêtes simultanées
Pour afficher le nombre de requêtes simultanées, exécutez cette requête Transact-SQL sur votre base de données SQL :

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Pour analyser la charge de travail d’une base de données SQL Server locale, modifiez cette requête pour la filtrer selon la base de données spécifique que vous analysez. Par exemple, si vous utilisez une base de données locale nommée MyDatabase, cette requête Transact-SQL renvoie le nombre de requêtes simultanées dans cette base de données :

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Il s’agit simplement d’un instantané à un point unique dans le temps. Pour obtenir une meilleure compréhension de votre charge de travail et des exigences liées aux demandes simultanées, il vous faut collecter plusieurs échantillons au fil du temps.

### <a name="maximum-concurrent-logins"></a>Nombre maximal de connexions simultanées
Vous pouvez analyser vos modèles d'utilisateur et d'application pour avoir une idée de la fréquence des connexions. Vous pouvez également exécuter des charges réelles dans un environnement de test pour vous assurer que vous n’atteignez pas cette limite ou d’autres limites décrites dans cet article. Il n’existe aucune requête ou vue de gestion dynamique qui peut vous indiquer le nombre de connexions simultanées ou un historique dédié.

Si plusieurs clients utilisent la même chaîne de connexion, le service authentifie chaque connexion. Si 10 utilisateurs se connectent simultanément à une base de données avec les mêmes nom d’utilisateur et mot de passe, dix connexions simultanées seront établies. Cette limite s’applique uniquement à la durée de la connexion et de l’authentification. Si ces mêmes 10 utilisateurs se connectent séquentiellement à la base de données, le nombre de connexions simultanées ne sera jamais supérieur à 1.

> [!NOTE]
> Actuellement, cette limite ne s’applique pas aux bases de données de pools élastiques.
> 
> 

### <a name="maximum-sessions"></a>Nombre maximal de sessions
Pour afficher le nombre de sessions simultanément actives, exécutez cette requête Transact-SQL sur votre base de données SQL :

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Si vous analysez une charge de travail SQL Server locale, modifiez la requête pour vous concentrer sur une base de données spécifique. Cette requête vous aide à déterminer les éventuels besoins de votre session pour la base de données si vous envisagez de la déplacer vers Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Là encore, ces requêtes renvoient un nombre à un point dans le temps. Si vous collectez plusieurs échantillons au fil du temps, vous bénéficiez d’une meilleure compréhension de l’utilisation de votre session.

Pour une analyse SQL Database, vous pouvez obtenir des statistiques d’historique sur les sessions en interrogeant la vue [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) et en consultant la colonne **active_session_count**. 

## <a name="monitoring-query-performance"></a>Analyse des performances des requêtes
Des requêtes lentes ou longues peuvent consommer des ressources système significatives. Cette section montre comment utiliser des vues de gestion dynamique pour détecter quelques problèmes courants liés aux performances de requête. L’article [Résolution des problèmes de performances dans SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) sur Microsoft TechNet constitue une référence plus ancienne, mais toujours utile, pour le dépannage.

### <a name="finding-top-n-queries"></a>Recherche des N premières requêtes
L’exemple suivant renvoie des informations relatives aux cinq premières requêtes, classées sur la base du temps processeur moyen. Cet exemple agrège les requêtes selon leur hachage de requête et, logiquement, les requêtes équivalentes sont regroupées sur la base de leur consommation cumulée de ressources.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Surveillance des requêtes bloquées
Des requêtes lentes ou longues peuvent contribuer à une consommation excessive de ressources et être la conséquence de requêtes bloquées. La cause du blocage peut être une conception médiocre d’application, de mauvais plans de requête, le manque d’index utiles, et ainsi de suite. La vue sys.dm_tran_locks permet d’obtenir des informations sur l’activité de verrouillage actuelle dans votre base de données SQL Azure. Pour un exemple de code, voir [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) dans la documentation en ligne de SQL Server.

### <a name="monitoring-query-plans"></a>Analyse des plans de requête
Un plan de requête inefficace peut également augmenter la consommation du processeur. L’exemple suivant utilise la vue [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) pour déterminer quelle requête utilise le plus de temps processeur total.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Voir aussi
[Présentation de la base de données SQL](sql-database-technical-overview.md)

