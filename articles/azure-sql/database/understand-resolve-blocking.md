---
title: Comprendre et résoudre les problèmes de blocage d’Azure SQL
titleSuffix: Azure SQL Database
description: Vue d’ensemble des rubriques spécifiques aux bases de données Azure SQL sur le blocage et la résolution des problèmes.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 1/14/2020
ms.openlocfilehash: b73e72969a851428034499d447ecb162a61aa9ab
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725784"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Comprendre et résoudre les problèmes de blocage d’Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Objectif

L’article décrit les blocages qui peuvent se produire dans les bases de données Azure SQL et montre comment les détecter et les résoudre. 

Dans cet article, le terme « connexion » fait référence à une session ouverte unique de la base de données. Chaque connexion s’affiche sous la forme d’un ID de session (SPID) ou session_id dans de nombreuses vues de gestion dynamique (DMV). Chacun de ces SPID est souvent appelé processus, bien qu’il ne s’agisse pas d’un contexte de processus distinct au sens habituel. Chaque SPID est plutôt constitué des ressources du serveur et des structures de données nécessaires pour traiter les requêtes d’une seule connexion d’un client donné. Une même application cliente peut avoir une ou plusieurs connexions. Du point de vue d’Azure SQL Database, il n’existe aucune différence entre plusieurs connexions à partir d’une application cliente unique sur un seul ordinateur client et plusieurs connexions à partir de plusieurs applications clientes ou de plusieurs ordinateurs clients ; elles sont atomiques. Une connexion peut bloquer une autre connexion, quel que soit le client source.

> [!NOTE]
> **Ce contenu est spécifique à Azure SQL Database.** Azure SQL Database est basé sur la dernière version stable du moteur de base de données Microsoft SQL Server. Une grande partie du contenu est donc similaire, même si les options et les outils de résolution des problèmes peuvent différer. Pour plus d’informations sur les blocages dans SQL Server, consultez [Comprendre et résoudre les problèmes de blocage de SQL Server](/troubleshoot/sql/performance/understand-resolve-blocking).

## <a name="understand-blocking"></a>Comprendre les blocages 
 
Le blocage est une caractéristique inévitable et inhérente à tout système de gestion de base de données relationnelle (SGBDR) avec concurrence basée sur les verrous. Comme mentionné précédemment, dans SQL Server, un blocage se produit lorsqu’une session maintient un verrou sur une ressource spécifique et qu’un deuxième SPID tente d’acquérir sur la même ressource un type de verrou en conflit. En règle générale, le délai d’exécution pendant lequel le premier SPID verrouille la ressource est court. Lorsque la session propriétaire libère le verrou, la deuxième connexion est alors libre d’acquérir son propre verrou sur la ressource et de poursuivre le traitement. Ce comportement est normal et peut se produire plusieurs fois au cours d’une journée, sans effet notable sur les performances du système.

La durée et le contexte de transaction d’une requête déterminent la durée de conservation de ses verrous et, par conséquent, leur effet sur les autres requêtes. Si la requête n’est pas exécutée dans le cadre d’une transaction (et qu’aucun indicateur de verrou n’est utilisé), les verrous des instructions SELECT ne sont conservés que sur une ressource au moment où elle est réellement lue, et non pendant la requête. Pour les instructions INSERT, UPDATE et DELETE, les verrous sont maintenus pendant la requête, à la fois pour la cohérence des données et pour permettre la restauration de la requête si nécessaire.

Pour les requêtes exécutées dans le cadre d’une transaction, la durée pendant laquelle les verrous sont maintenus est déterminée par le type de requête, le niveau d’isolation de la transaction et si les indicateurs de verrou sont utilisés dans la requête. Pour obtenir une description du verrouillage, des indicateurs de verrou et des niveaux d’isolation des transactions, consultez les articles suivants :

* [Verrouillage du moteur de base de données](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [Personnalisation du verrouillage et du contrôle de version de ligne](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Modes de verrouillage](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Compatibilité de verrouillage](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [Transactions](/sql/t-sql/language-elements/transactions-transact-sql)

Lorsque le verrouillage et le blocage persistent au point de nuire aux performances du système, cela est dû à l’une des raisons suivantes :

* Un SPID maintient des verrous sur un ensemble de ressources pendant une période prolongée avant de libérer ces ressources. Ce type de blocage se résout de lui-même avec le temps, mais peut entraîner une détérioration des performances.

* Un SPID maintient des verrous sur un ensemble de ressources et ne les libère jamais. Ce type de blocage ne se résout pas de lui-même et empêche indéfiniment l’accès aux ressources concernées.

Dans le premier scénario, la situation peut être très fluide, car différents SPID provoquent un blocage sur différentes ressources au fil du temps, créant ainsi une cible mobile. Ces situations sont difficiles à résoudre en utilisant [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) pour limiter le problème aux requêtes individuelles. En revanche, la deuxième situation aboutit à un état cohérent qui peut être plus facile à diagnostiquer.

## <a name="applications-and-blocking"></a>Applications et blocage

Les utilisateurs ont tendance à se concentrer sur les problèmes de plateforme et de réglage côté serveur lorsqu’ils sont confrontés à un problème de blocage. Toutefois, l’attention portée uniquement à la base de données peut ne pas aboutir à une résolution et peut absorber du temps et de l’énergie mieux consacrés à l’examen de l’application cliente et des requêtes qu’elle envoie. Quel que soit le niveau de visibilité que l’application expose en ce qui concerne les appels à la base de données, un problème de blocage requiert souvent l’inspection des instructions SQL exactes soumises par l’application et du comportement exact de l’application concernant l’annulation des requêtes, la gestion des connexions, l’extraction de toutes les lignes de résultats, etc. Si l’outil de développement ne permet pas un contrôle explicite de la gestion des connexions, de l’annulation des requêtes, du délai d’attente des requêtes, de l’extraction des résultats, et ainsi de suite, les problèmes de blocage peuvent ne pas être résolus. Ce potentiel doit être examiné attentivement avant de sélectionner un outil de développement d’applications pour Azure SQL Database, en particulier pour les environnements OLTP sensibles aux performances. 

Faites attention aux performances de la base de données pendant la phase de conception et de construction de la base de données et de l’application. En particulier, la consommation des ressources, le niveau d’isolation et la longueur du chemin de la transaction doivent être évalués pour chaque requête. Chaque requête et chaque transaction doivent être aussi simples que possible. Une bonne discipline en matière de gestion des connexions doit être exercée, sans quoi l’application peut sembler avoir des performances acceptables lorsque le nombre d’utilisateurs est faible, mais les performances peuvent se dégrader de manière significative à mesure que le nombre d’utilisateurs augmente. 

Si la conception d’application et de requête est appropriée, Azure SQL Database peut prendre en charge plusieurs milliers d’utilisateurs simultanés sur un seul serveur, avec peu de blocage.

> [!Note]
> Pour plus de conseils sur le développement d’applications, consultez [Résolution des problèmes de connectivité et autres erreurs avec Azure SQL Database et Azure SQL Managed Instance](troubleshoot-common-errors-issues.md) et [Gestion des erreurs temporaires](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling).

## <a name="troubleshoot-blocking"></a>Résoudre les problèmes de blocage

Quelle que soit la situation de blocage dans laquelle nous nous trouvons, la méthodologie de résolution des problèmes de verrouillage est la même. Ces séparations logiques définissent le reste de la composition de cet article. Le concept consiste à trouver le bloqueur principal et à identifier ce que fait cette requête et la raison pour laquelle elle crée un blocage. Une fois que la requête problématique est identifiée (c’est-à-dire, ce qui maintient les verrous pendant une période prolongée), l’étape suivante consiste à analyser et à déterminer la cause du blocage. Une fois que nous avons compris la raison, nous pouvons apporter des modifications en reconcevant la requête et la transaction.

Étapes de résolution du problème :

1. Identifier la session de blocage principale (bloqueur principal)

2. Rechercher la requête et la transaction à l’origine du blocage (qu’est-ce qui maintient les verrous pendant une période prolongée)

3. Analyser/comprendre les raisons du blocage prolongé

4. Résoudre le problème bloquant en reconcevant la requête et la transaction

Voyons maintenant comment identifier la session de blocage principale grâce à une capture de données appropriée.

## <a name="gather-blocking-information"></a>Collecter les informations de blocage

Pour pallier la difficulté de résolution des problèmes de blocage, un Administrateur de base de données peut utiliser des scripts SQL qui surveillent en permanence l’état du verrouillage et du blocage dans la base de données Azure SQL. Pour recueillir ces données, il existe essentiellement deux méthodes. 

La première consiste à interroger les objets de gestion dynamique (DMO) et à stocker les résultats à des fins de comparaison dans le temps. Certains objets mentionnés dans cet article sont des vues de gestion dynamique (DMV) et d’autres des fonctions de gestion dynamique (DMF). La seconde méthode consiste à utiliser des XEvents pour capturer ce qui est en cours d’exécution. 


## <a name="gather-information-from-dmvs"></a>Collecter des informations à partir de DMV

Le référencement des DMV pour résoudre les problèmes de blocage a pour objectif d’identifier le SPID (ID de session) en tête de la chaîne de blocage et de l’instruction SQL. Recherchez les SPID victimes qui sont bloqués. Si un SPID est bloqué par un autre SPID, examinez le SPID propriétaire de la ressource (le SPID bloquant). Ce SPID propriétaire est-il également bloqué ? Vous pouvez parcourir la chaîne pour rechercher le bloqueur principal, puis rechercher la raison pour laquelle il conserve son verrou.

N’oubliez pas d’exécuter chacun de ces scripts dans la base de données Azure SQL cible.

* Les commandes sp_who et sp_who2 sont des commandes plus anciennes permettant d’afficher toutes les sessions en cours. La DMV sys.dm_exec_sessions renvoie plus de données dans un jeu de résultats plus facile à interroger et à filtrer. Vous trouverez sys.dm_exec_sessions au cœur d’autres requêtes. 

* Si vous avez déjà identifié une session particulière, vous pouvez utiliser `DBCC INPUTBUFFER(<session_id>)` pour rechercher la dernière instruction qui a été soumise par une session. Des résultats similaires peuvent être retournés avec la fonction de gestion dynamique(DMF) sys.dm_exec_input_buffer, dans un jeu de résultats plus facile à interroger et à filtrer, en fournissant le session_id et le request_id. Par exemple, pour retourner la dernière requête soumise par session_id 66 et request_id 0 :

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Consultez la sys.dm_exec_requests et reportez-vous à la colonne blocking_session_id. Lorsque blocking_session_id = 0, aucune session n’est bloquée. Si sys.dm_exec_requests répertorie uniquement les requêtes en cours d’exécution, toutes les connexions (actives ou non) sont répertoriées dans sys.dm_exec_sessions. La requête suivante s’appuiera sur cette jointure commune entre sys.dm_exec_requests et sys.dm_exec_sessions.

* Exécutez cet exemple de requête pour rechercher les requêtes en cours d’exécution et leur texte actuel de lot SQL ou de mémoire tampon d’entrée en utilisant les DMV [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) ou [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql). Si les données retournées par le champ `text` de sys.dm_exec_sql_text ont la valeur NULL, la requête n’est pas en cours d’exécution. Dans ce cas, le champ `event_info` de sys.dm_exec_input_buffer contiendra la dernière chaîne de commande transmise au moteur SQL. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Pour repérer les transactions durables ou non validées, utilisez un autre ensemble de DMV pour visualiser les transactions ouvertes actuelles, notamment [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) et sys.dm_exec_sql_text. Il existe plusieurs DMV associées au suivi des transactions : voir plus de [DMV sur les transactions](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) ici. 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* Référencez [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) qui se trouve au niveau de la couche de thread/tâche de SQL. Cela renvoie des informations sur le type d’attente SQL que la requête rencontre actuellement. Comme pour sys.dm_exec_requests, seules les requêtes actives sont retournées par sys.dm_os_waiting_tasks. 

> [!Note]
> Pour plus d’informations sur les types d’attente, y compris les statistiques agrégées sur l’attente dans le temps, consultez la DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database). Cette DMV renvoie des statistiques agrégées sur l’attente uniquement pour la base de données actuelle.

* Utilisez la DMV [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) pour obtenir des informations plus granulaires sur les verrous placés par les requêtes. Cette DMV peut renvoyer de grandes quantités de données sur un serveur SQL de production et est utile pour diagnostiquer quels verrous sont actuellement maintenus. 

En raison de la jointure interne sur sys.dm_os_waiting_tasks, la requête suivante limite la sortie de sys.dm_tran_locks uniquement aux requêtes actuellement bloquées, à leur état d’attente et à leurs verrous :

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* Grâce aux DMV, le stockage des résultats de requête dans le temps fournira des points de données qui vous permettront d’examiner le blocage sur un intervalle de temps donné afin d’identifier les blocages persistants ou les tendances. 

## <a name="gather-information-from-extended-events"></a>Collecter des informations à partir d’événements étendus

Outre les informations ci-dessus, il est souvent nécessaire de capturer une trace des activités sur le serveur pour examiner minutieusement un problème bloquant sur Azure SQL Database. Par exemple, si une session exécute plusieurs instructions dans une transaction, seule la dernière instruction soumise sera représentée. Toutefois, l’une des instructions précédentes peut être la raison pour laquelle les verrous sont toujours maintenus. Une trace vous permet de voir toutes les commandes exécutées par une session dans la transaction en cours.

Il existe deux façons de capturer des traces dans SQL Server : les événements étendus (XEvents) et les traces du profileur. Toutefois, la technologie de trace [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) est déconseillée et n’est pas prise en charge pour Azure SQL Database. Les [événements étendus](/sql/relational-databases/extended-events/extended-events) constituent la nouvelle technologie de suivi qui permet une plus grande polyvalence et un impact moindre sur le système observé, et son interface est intégrée à SQL Server Management Studio (SSMS). 

Reportez-vous au document qui explique comment utiliser l’[Assistant Nouvelle session d’événements étendus](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) dans SSMS. Toutefois, pour les bases de données Azure SQL, SSMS fournit un sous-dossier Événements étendus sous chaque base de données dans Explorateur d’objets. Utilisez l’Assistant Session XEvent pour capturer ces événements utiles : 

-   Catégorie Erreurs :
    -   Attention
    -   Error_reported  
    -   Execution_warning

-   Catégorie Avertissements : 
    -   Missing_join_predicate

-   Catégorie Exécution :
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Verrou
    -   Lock_deadlock

-   session
    -   Existing_connection
    -   Connexion
    -   Logout

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Identifier et résoudre les scénarios de blocage courants

En examinant les informations ci-dessus, vous pouvez déterminer la cause de la plupart des problèmes de blocage. Le reste de cet article explique comment utiliser ces informations pour identifier et résoudre certains scénarios de blocage courants. Cette discussion part du principe que vous avez utilisé les scripts de blocage (référencés précédemment) pour capturer des informations sur les SPID bloquants et que vous avez capturé l’activité de l’application en utilisant une session XEvent.

## <a name="analyze-blocking-data"></a>Analyser les données de blocage 

* Examinez la sortie des DMV sys.dm_exec_requests et sys.dm_exec_sessions pour déterminer les têtes des chaînes de blocage, en utilisant blocking_these et session_id. Cela permet d’identifier plus clairement les requêtes qui sont bloquées et celles qui bloquent. Examinez plus en détail les sessions bloquées et bloquantes. Existe-t-il un point commun ou une racine à la chaîne de blocage ? Elles partagent probablement une table commune, et une ou plusieurs sessions impliquées dans une chaîne de blocage effectuent une opération d’écriture. 

* Examinez la sortie des DMV sys.dm_exec_requests et sys.dm_exec_sessions pour obtenir des informations sur les SPID en tête de la chaîne de blocage. Recherchez les champs suivants : 

    -    `sys.dm_exec_requests.status`  
    Cette colonne indique l’état d’une requête particulière. En règle générale, un état de veille indique que l’exécution du SPID est terminée et qu’il attend que l’application envoie une autre requête ou un autre lot. Un état Exécutable ou Exécution en cours indique que le SPID est en train de traiter une requête. Le tableau suivant fournit une brève explication des différentes valeurs d’état.

    | Statut | Signification |
    |:-|:-|
    | Arrière-plan | Le SPID exécute une tâche en arrière-plan, telle qu’une détection de blocage, l’écriture dans un journal ou un point de contrôle. |
    | En état de veille | Le SPID n’est pas en cours d’exécution. Cela indique généralement que le SPID attend une commande de l’application. |
    | Exécution en cours | Le SPID est actuellement en cours d’exécution sur un planificateur. |
    | Exécutable | Le SPID est dans la file d’attente exécutable d’un planificateur et attend d’obtenir l’heure du planificateur. |
    | Interrompu | Le SPID attend une ressource, telle qu’un verrou. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    Ce champ indique le nombre de transactions ouvertes dans cette session. Si cette valeur est supérieure à 0, le SPID se trouve dans une transaction ouverte et peut contenir des verrous acquis par toute instruction dans la transaction.

    -    `sys.dm_exec_requests.open_transaction_count`  
    De même, ce champ indique le nombre de transactions ouvertes dans cette requête. Si cette valeur est supérieure à 0, le SPID se trouve dans une transaction ouverte et peut contenir des verrous acquis par toute instruction dans la transaction.

    -   `sys.dm_exec_requests.wait_type`, `wait_time` et `last_wait_type`  
    Si la  `sys.dm_exec_requests.wait_type` a la valeur NULL, la requête n’attend rien et la valeur  `last_wait_type` indique le dernier  `wait_type` que la requête a rencontrée. Pour plus d’informations sur  `sys.dm_os_wait_stats` et pour obtenir une description des types d’attente les plus courants, consultez [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql). La valeur de  `wait_time` peut être utilisée pour déterminer si la requête progresse. Lorsqu’une interrogation de la table sys.dm_exec_requests renvoie une valeur dans la colonne  `wait_time` inférieure à la valeur  `wait_time` d’une précédente interrogation de sys.dm_exec_requests, cela indique que le verrou précédent a été acquis et libéré et qu’il attend maintenant un nouveau verrou (en supposant une valeur `wait_time` différente de zéro). Cela peut être vérifié en comparant la valeur `wait_resource` de la sortie de sys.dm_exec_requests, qui affiche la ressource pour laquelle la requête est en attente.

    -   `sys.dm_exec_requests.wait_resource` Ce champ indique la ressource qu’une requête bloquée attend. Le tableau suivant énumère les formats courants de  `wait_resource` et leur signification :

    | Resource | Format | Exemple | Explication | 
    |:-|:-|:-|:-|
    | Table de charge de travail | DatabaseID:ObjectID:IndexID | TAB: 5:261575970:1 | Dans ce cas, l’ID de base de données 5 est l’exemple de base de données « pubs », l’ID d’objet 261575970 est la table « titles » et 1 est l’index cluster. |
    | Page | DatabaseID:FileID:PageID | PAGE: 5:1:104 | Dans ce cas, l’ID de base de données 5 est « pubs », l’ID de fichier 1 est le fichier de données primaire et la page 104 est une page appartenant à la table « titles ». Pour identifier l’object_id auquel la page appartient, utilisez la fonction de gestion dynamique [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql), en transmettant les DatabaseID, FileId et PageId de `wait_resource`. | 
    | Clé | DatabaseID:Hobt_id (valeur de hachage pour la clé d’index) | KEY: 5:72057594044284928 (3300a4f361aa) | Dans ce cas, l’ID de base de données 5 est « pubs », Hobt_ID 72057594044284928 correspond à index_id 2 pour object_id 261575970 (table « titles »). Utilisez l’affichage catalogue sys.partitions pour associer le hobt_id à un index_id et à un object_id particuliers. Il n’existe aucun moyen de décomposer le hachage de la clé d’index en une valeur de clé spécifique. |
    | Ligne | DatabaseID:FileID:PageID:Slot(row) | RID: 5:1:104:3 | Dans ce cas, l’ID de base de données 5 est « pubs », l’ID de fichier 1 est le fichier de données primaire, la page 104 est une page appartenant à la table « titles » et l’emplacement 3 indique la position de la ligne sur la page. |
    | Compiler  | DatabaseID:FileID:PageID:Slot(row) | RID: 5:1:104:3 | Dans ce cas, l’ID de base de données 5 est « pubs », l’ID de fichier 1 est le fichier de données primaire, la page 104 est une page appartenant à la table « titles » et l’emplacement 3 indique la position de la ligne sur la page. |

    -    `sys.dm_tran_active_transactions` La DMV [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) contient des données sur les transactions ouvertes qui peuvent être jointes à d’autres DMV pour obtenir une image complète des transactions en attente de validation ou de restauration. Utilisez la requête suivante pour renvoyer des informations sur les transactions ouvertes, jointes à d’autres DMV, y compris [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql). Tenez compte de l’état actuel d’une transaction, de `transaction_begin_time`, et d’autres données de situation pour évaluer si elle peut être une source de blocage.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   D’autres colonnes

        Les colonnes restantes dans [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) et [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) peuvent également fournir des indications sur la racine d’un problème. Leur utilité varie selon les circonstances du problème. Par exemple, vous pouvez déterminer si le problème se produit uniquement à partir de certains clients (hostname), sur certaines bibliothèques réseau (net_library), quand le dernier lot soumis par un SPID a été `last_request_start_time` dans sys.dm_exec_sessions, la durée d’exécution d’une requête à l’aide de `start_time` dans sys.dm_exec_requests, et ainsi de suite.


## <a name="common-blocking-scenarios"></a>Scénarios de blocage courants

Le tableau ci-dessous établit une correspondance entre les symptômes courants et leurs causes probables.  

Les colonnes `wait_type`, `open_transaction_count` et `status` font référence aux informations retournées par [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql), et les autres colonnes peuvent être retournées par [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql). La colonne « Est résolu en ? » indique si le blocage se résoudra de lui-même ou si la session doit être supprimée à l’aide de la commande `KILL`. Pour plus d’informations, consultez [KILL (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql).

| Scénario | Waittype | Open_Tran | Statut | Est résolu en ? | Autres symptômes |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NOT NULL | >= 0 | exécutable | Oui, à la fin de la requête. | Dans sys.dm_exec_sessions, les colonnes **reads**, **cpu_time** et/ou **memory_usage** augmenteront avec le temps. La durée de la requête sera élevée une fois l’opération terminée. |
| 2 | NULL | \>0 | en veille | Non, mais le SPID peut être tué. | Un signal d’attention peut apparaître dans la session XEvent pour ce SPID, indiquant qu’un délai d’attente ou une annulation de requête a eu lieu. |
| 3 | NULL | \>= 0 | exécutable | Non. Ne sera pas résolu tant que le client n’aura pas extrait toutes les lignes ou fermé la connexion. Le SPID peut être tué, mais cela peut prendre jusqu’à 30 secondes. | Si open_transaction_count = 0 et que le SPID maintient des verrous alors que le niveau d’isolation de la transaction est par défaut (READ COMMMITTED), c’est une cause probable. |  
| 4 | Variable | \>= 0 | exécutable | Non. Ne sera pas résolu tant que le client n’a pas annulé les requêtes ou fermé les connexions. Les SPID peuvent être tués, mais cela peut prendre jusqu’à 30 secondes. | Le colonne **hostname** dans la sys.dm_exec_sessions du SPID à la tête d’une chaîne de blocage sera la même que celle du SPID qu’il bloque. |  
| 5 | NULL | \>0 | restaurer | Oui. | Un signal d’attention peut apparaître dans la session XEvent pour ce SPID, indiquant qu’un délai d’attente ou une annulation de requête a eu lieu ou simplement qu’une instruction de restauration a été émise. |  
| 6 | NULL | \>0 | en veille | À terme. Lorsque Windows NT détermine que la session n’est plus active, la connexion Azure SQL Database est interrompue. | La valeur `last_request_start_time` dans sys.dm_exec_sessions est bien antérieure à l’heure actuelle. |

Les scénarios suivants développeront ces scénarios. 

1.  Blocage causé par une requête normalement en cours d’exécution avec une durée d’exécution longue

    **Résolution** : La solution à ce type de problème de blocage consiste à rechercher des moyens d’optimiser la requête. En fait, cette classe de problème de blocage peut n’être qu’un problème de performances et vous obliger à la poursuivre en tant que tel. Pour plus d’informations sur le dépannage d’une requête spécifique à exécution lente, consultez [Comment résoudre les problèmes de requête à exécution lente sur SQL Server](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). Pour plus d’informations, consultez [Surveiller et régler les performances](/sql/relational-databases/performance/monitor-and-tune-for-performance). 

    Les rapports de [Magasin des requêtes](/sql/relational-databases/performance/best-practice-with-the-query-store) dans SSMS sont également un outil précieux et hautement recommandé pour identifier les requêtes les plus coûteuses, à savoir les plans d’exécution non optimaux. Consultez également la section [Performances intelligentes](intelligent-insights-overview.md) du portail Azure pour la base de données Azure SQL, y compris [Query Performance Insight](query-performance-insight-use.md).

    Si vous disposez d’une requête durable qui bloque d’autres utilisateurs et ne peut pas être optimisée, envisagez de la déplacer d’un environnement OLTP à un système dédié de création de rapports, un [réplica synchrone en lecture seule de la base de données](read-scale-out.md).

1.  Blocage causé par un SPID en veille dont une transaction n’est pas validée

    Ce type de blocage peut souvent être identifié par un SPID en veille ou en attente d’une commande, mais dont le niveau d’imbrication des transactions (`@@TRANCOUNT`, `open_transaction_count` de sys.dm_exec_requests) est supérieur à zéro. Cela peut se produire si l’application rencontre un délai d’attente de requête ou émet une annulation sans émettre également le nombre requis d’instructions ROLLBACK et/ou COMMIT. Lorsqu’un SPID reçoit un délai d’attente de requête ou une annulation, il termine la requête et le traitement en cours, mais ne restaure pas ou ne valide pas automatiquement la transaction. L’application est responsable de cette opération, car Azure SQL Database ne peut pas supposer qu’une transaction entière doit être restaurée en raison de l’annulation d’une seule requête. Le délai d’attente ou l’annulation de la requête s’affiche sous la forme d’un événement de signal ATTENTION pour le SPID dans la session XEvent.

    Pour illustrer une transaction explicite non validée, émettez la requête suivante :

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Ensuite, exécutez cette requête dans la même fenêtre :
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    La sortie de la deuxième requête indique que le niveau d’imbrication de la transaction est 1. Tous les verrous acquis dans la transaction sont conservés jusqu’à ce que la transaction soit validée ou restaurée. Si les applications ouvrent et valident explicitement des transactions, une erreur de communication ou autre peut laisser la session et sa transaction dans un état ouvert. 

    Utilisez le script ci-dessus basé sur sys.dm_tran_active_transactions pour identifier les transactions actuellement non validées.

    **Résolutions** :

     -   En outre, cette classe de problème de blocage peut également être un problème de performances et vous obliger à la poursuivre en tant que tel. Si la durée d’exécution de la requête peut être réduite, le délai d’attente ou l’annulation de la requête ne se produit pas. Il est important que l’application soit capable de gérer les scénarios de délai d’attente ou d’annulation s’ils se produisent, mais vous pouvez également tirer parti de l’examen des performances de la requête. 
     
     -    Les applications doivent gérer correctement les niveaux d’imbrication des transactions, sinon elles peuvent entraîner un problème bloquant suite à l’annulation de la requête de cette manière. Tenez compte des éléments suivants :  

            *    Dans le gestionnaire d’erreurs de l’application cliente, exécutez `IF @@TRANCOUNT > 0 ROLLBACK TRAN` après toute erreur, même si l’application cliente ne pense pas qu’une transaction est ouverte. La vérification des transactions ouvertes est obligatoire, car une procédure stockée appelée pendant le traitement peut avoir démarré une transaction à l’insu de l’application cliente. Certaines conditions, telles que l’annulation de la requête, empêchent la procédure de s’exécuter au-delà de l’instruction actuelle. Par conséquent, même si la procédure a une logique pour vérifier `IF @@ERROR <> 0` et abandonner la transaction, ce code de restauration ne sera pas exécuté dans ces cas.  
            *    Si le regroupement de connexions est utilisé dans une application qui ouvre la connexion et exécute un petit nombre de requêtes avant de libérer la connexion et de la rendre au pool, par exemple une application web, la désactivation temporaire du regroupement de connexions peut aider à atténuer le problème jusqu’à ce que l’application cliente soit modifiée pour gérer les erreurs de manière appropriée. Si vous désactivez le regroupement de connexions, le fait de libérer la connexion entraînera la déconnexion physique de la connexion Azure SQL Database, amenant le serveur à restaurer toute transaction ouverte.  
            *    Utilisez `SET XACT_ABORT ON` pour la connexion ou dans toutes les procédures stockées qui commencent des transactions et qui ne sont pas nettoyées après une erreur. En cas d’erreur d’exécution, ce paramètre abandonne toutes les transactions ouvertes et rend le contrôle au client. Pour plus d’informations, consultez [SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql).
    > [!NOTE]
    > La connexion n’est pas réinitialisée tant qu’elle n’est pas réutilisée à partir du pool de connexions. Il est donc possible qu’un utilisateur ouvre une transaction puis libère la connexion pour le pool de connexion. Cependant, celle-ci pourrait ne pas être réutilisée pendant plusieurs secondes, période pendant laquelle la transaction resterait ouverte. Si la connexion n’est pas réutilisée, la transaction est abandonnée lorsque le délai d’attente de la connexion expire et est supprimée du pool de connexions. Il est donc optimal pour l’application cliente d’abandonner les transactions dans son gestionnaire d’erreurs ou d’utiliser `SET XACT_ABORT ON` pour éviter ce retard potentiel.

    > [!CAUTION]
    > Après `SET XACT_ABORT ON`, les instructions T-SQL qui suivent une instruction causant une erreur ne seront pas exécutées. Cela peut nuire au flux prévu du code existant.

1.  Blocage causé par un SPID dont l’application cliente correspondante n’a pas permis d’extraire toutes les lignes de résultat

    Après l’envoi d’une requête au serveur, toutes les applications doivent extraire immédiatement toutes les lignes de résultat jusqu’au bout. Si une application n’extrait pas toutes les lignes de résultats, des verrous peuvent être maintenus sur les tables et bloquer d’autres utilisateurs. Si vous utilisez une application qui envoie en toute transparence des instructions SQL au serveur, l’application doit extraire toutes les lignes de résultat. Si elle ne le fait pas (et si elle ne peut pas être configurée pour le faire), vous ne pourrez peut-être pas résoudre le problème de blocage. Pour éviter ce problème, vous pouvez limiter les applications au comportement médiocre à un signalement ou à une base de données d’aide à la décision.
    
    > [!NOTE]
    > Consultez l’[aide relative à la logique de nouvelle tentative](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) pour les applications se connectant à Azure SQL Database. 
    
    **Résolution** : L’application doit être réécrite afin d’extraire toutes les lignes du résultat. Cela n’exclut pas l’utilisation de [OFFSET et FETCH dans la clause ORDER BY](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) d’une requête pour effectuer la pagination côté serveur.

1.  Blocage causé par un SPID en état de restauration

    Une requête de modification des données qui est tuée ou annulée en dehors d’une transaction définie par l’utilisateur est restaurée. Cela peut également être un effet secondaire de la déconnexion de la session réseau du client ou se produire lorsqu’une requête est sélectionnée comme victime d’un blocage. Cela peut souvent être identifié en observant la sortie de sys.dm_exec_requests, qui peut indiquer la **commande** ROLLBACK, et la **colonne percent_complete** peut afficher la progression. 

    Grâce à la [fonctionnalité de récupération de base de données accélérée](../accelerated-database-recovery.md) introduite en 2019, les restaurations longues devraient être rares.
    
    **Résolution** : Attendez que le SPID termine de restaurer les modifications apportées. 

    Pour éviter cette situation, n’effectuez pas d’opérations d’écriture par lots volumineux, de création d’index ou d’opérations de maintenance pendant les heures de forte utilisation des systèmes OLTP. Si possible, effectuez ces opérations pendant les périodes de faible activité.

1.  Blocage causé par une connexion orpheline

    Si l’application cliente intercepte des erreurs ou que la station de travail cliente est redémarrée, la session réseau sur le serveur peut ne pas être annulée immédiatement dans certaines conditions. Du point de vue d’Azure SQL Database, le client semble toujours être présent et tous les verrous acquis peuvent toujours être conservés. Pour plus d’informations, consultez [Comment résoudre des problèmes de connexion orpheline dans SQL Server](/sql/t-sql/language-elements/kill-transact-sql#remarks). 

    **Résolution** : Si l’application cliente s’est déconnectée sans nettoyer correctement ses ressources, vous pouvez terminer le SPID à l’aide de la commande `KILL`. La commande `KILL` prend la valeur du SPID comme entrée. Par exemple, pour tuer SPID 99, émettez la commande suivante :

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Voir aussi

* [Supervision et réglage des performances dans Azure SQL Database et Azure SQL Managed Instance](/monitor-tune-overview.md)
* [Surveillance des performances à l’aide du magasin des requêtes](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [Guide du verrouillage des transactions et du contrôle de version de ligne](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [SET TRANSACTION ISOLATION LEVEL](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Démarrage rapide : Événements étendus dans SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Utilisation de l’intelligence artificielle pour superviser et résoudre les problèmes de performances de la base de données par Intelligent Insights](intelligent-insights-overview.md)

## <a name="learn-more"></a>En savoir plus

* [Azure SQL Database : Amélioration du réglage des performances grâce au réglage automatique](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Améliorer les performances d’Azure SQL Database grâce au réglage automatique](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Offrez des performances cohérentes avec Azure SQL](/learn/modules/azure-sql-performance/)
* [Résolution des problèmes de connectivité et autres erreurs avec Azure SQL Database et Azure SQL Managed Instance](troubleshoot-common-errors-issues.md)
* [Transient Fault Handling](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)