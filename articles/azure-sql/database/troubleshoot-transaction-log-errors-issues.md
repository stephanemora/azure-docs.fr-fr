---
title: Résoudre les problèmes liés au journal des transactions
titleSuffix: Azure SQL Database and Azure SQL Managed Instance
description: Indique la procédure pour résoudre des problèmes de journal des transactions Azure SQL Database dans Azure SQL Database ou Azure SQL Managed Instance
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 07/23/2021
ms.openlocfilehash: 0b9e47d23968c29a0c69a2da198bcf8d183f1bb0
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675107"
---
# <a name="troubleshooting-transaction-log-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Résolution des erreurs du journal des transactions avec Azure SQL Database et Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Les erreurs 9002 ou 40552 peuvent s’afficher quand le journal des transactions est plein et qu’il ne peut pas accepter de nouvelles transactions. Ces erreurs se produisent quand le journal des transactions de la base de données, managé par Azure SQL Database ou Azure SQL Managed Instance, dépasse les seuils d’espace et qu’il ne peut pas continuer à accepter des transactions. 

Ces erreurs sont similaires aux problèmes liés à un journal des transactions complet dans SQL Server, mais leur résolution est différente dans Azure SQL Database ou Azure SQL Managed Instance.

> [!NOTE]
> **Cet article se concentre sur Azure SQL Database et Azure SQL Managed Instance.** Azure SQL Database et Azure SQL Managed Instance sont basés sur la dernière version stable du moteur de base de données Microsoft SQL Server. Une grande partie du contenu est donc similaire, même si les options et les outils de résolution des problèmes peuvent différer. Pour plus d’informations sur la résolution des problèmes liés à un journal des transactions dans SQL Server, consultez [Résoudre les problèmes liés à la saturation du journal des transactions (erreur 9002 SQL Server)](/sql/relational-databases/logs/troubleshoot-a-full-transaction-log-sql-server-error-9002).

## <a name="automated-backups-and-the-transaction-log"></a>Sauvegardes automatisées et journal des transactions

La gestion de l’espace des fichiers de base de données présente certaines différences clés dans Azure SQL Database et Azure SQL Managed Instance. 

- Dans Azure SQL Database ou Azure SQL Managed Instance, la sauvegarde du journal des transactions est effectuée automatiquement. Pour obtenir des informations sur la fréquence, la rétention et autres, consultez [Sauvegardes automatisées – Azure SQL Database et SQL Managed Instance](automated-backups-overview.md). 
- Dans Azure SQL Database, l’espace disque disponible, la croissance du fichier de base de données et l’emplacement des fichiers sont également managés, de sorte que les causes et les résolutions classiques des problèmes du journal des transactions diffèrent de SQL Server. 
- Dans Azure SQL Managed Instance, l’emplacement et le nom des fichiers de base de données ne peuvent pas être managés, mais les administrateurs peuvent gérer les fichiers de base de données et les paramètres de croissance automatique des fichiers. Les causes et résolutions classiques des problèmes de journal des transactions sont similaires à celles de SQL Server. 

Comme pour SQL Server, le journal des transactions de chaque base de données est tronqué chaque fois qu’une sauvegarde de fichier journal est effectuée. La troncation laisse un espace vide dans le fichier journal, qui peut ensuite accéder aux nouvelles transactions. Quand le fichier journal ne peut pas être tronqué par les sauvegardes du journal, le fichier journal augmente pour accueillir les nouvelles transactions. Si le fichier journal atteint les limites maximales dans Azure SQL Database ou Azure SQL Managed Instance, les nouvelles transactions ne peuvent pas être acceptées. Ce scénario est très rare.

## <a name="prevented-transaction-log-truncation"></a>Troncation du journal des transactions empêchée

Pour découvrir ce qui empêche la troncation du journal dans un cas donné, reportez-vous à `log_reuse_wait_desc` dans `sys.databases`. La description « log reuse wait » vous indique les conditions ou les causes qui empêchent la troncation du journal des transactions par une sauvegarde de fichier journal normale. Pour plus d’informations, consultez [sys.databases &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql). 

```sql
SELECT [name], log_reuse_wait_desc FROM sys.databases;
```

Les valeurs suivantes de `log_reuse_wait_desc` dans `sys.databases` peuvent indiquer la raison du blocage de la troncation du journal des transactions de la base de données :

| log_reuse_wait_desc | Diagnostic | Réponse requise |
|--|--|--|
| **NOTHING** | État classique. Rien de bloque la troncation du journal. | Non. |
| **CHECKPOINT** | Un point de contrôle est nécessaire pour la troncation du journal. Rare. | Aucune réponse n’est requise, sauf si le problème persiste. Si le problème persiste, soumettez une demande de support auprès du [Support Azure](https://portal.azure.com/#create/Microsoft.Support). | 
| **LOG BACKUP** | Une sauvegarde du journal est en cours. | Aucune réponse n’est requise, sauf si le problème persiste. Si le problème persiste, soumettez une demande de support auprès du [Support Azure](https://portal.azure.com/#create/Microsoft.Support). | 
| **ACTIVE BACKUP OR RESTORE** | Une sauvegarde de base de données est en cours. | Aucune réponse n’est requise, sauf si le problème persiste. Si le problème persiste, soumettez une demande de support auprès du [Support Azure](https://portal.azure.com/#create/Microsoft.Support). | 
| **ACTIVE TRANSACTION** | Une transaction en cours empêche la troncation du journal. | Le fichier journal ne peut pas être tronqué en raison de transactions actives et/ou non validées. Consultez la section suivante.| 
| **REPLICATION** | Dans Azure SQL Database, cela est probablement dû à la fonctionnalité de [capture des changements de données (CDC)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).<BR>Dans Azure SQL Managed Instance, cela est dû à la [réplication](../managed-instance/replication-transactional-overview.md) ou à la fonctionnalité CDC. | Dans Azure SQL Database, interrogez [sys.dm_cdc_errors](/sql/relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-errors) et résolvez les erreurs. Si les problèmes ne peuvent pas être résolus, soumettez une demande de support auprès du [Support Azure](https://portal.azure.com/#create/Microsoft.Support).<BR>Dans Azure SQL Managed Instance, si le problème persiste, examinez les agents impliqués dans la fonctionnalité CDC ou la réplication. Pour la résolution des problèmes de capture des changements de données, interrogez les tâches dans [msdb.dbo.cdc_jobs](/sql/relational-databases/system-tables/dbo-cdc-jobs-transact-sql). Le cas échéant, ajoutez via [sys.sp_cdc_add_job](/sql/relational-databases/system-stored-procedures/sys-sp-cdc-add-job-transact-sql). Pour la réplication, prenez en compte [Résolution des problèmes de réplication transactionnelle](/sql/relational-databases/replication/troubleshoot-tran-repl-errors). Si les problèmes ne peuvent pas être résolus, soumettez une demande de support auprès du [Support Azure](https://portal.azure.com/#create/Microsoft.Support). | 
| **AVAILABILITY_REPLICA** | La synchronisation avec le réplica secondaire est en cours. | Aucune réponse n’est requise, sauf si le problème persiste. Si le problème persiste, soumettez une demande de support auprès du [Support Azure](https://portal.azure.com/#create/Microsoft.Support). | 

### <a name="log-truncation-prevented-by-an-active-transaction"></a>Troncation du journal empêchée par une transaction active

Le scénario le plus courant quand un journal des transactions ne peut pas accepter de nouvelles transactions est une transaction durable ou bloquée.

Exécutez cet exemple de requête pour rechercher les transactions non validées ou actives, et leurs propriétés.

- Retourne des informations sur les propriétés de transaction à partir de [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql).
- Retourne les informations de connexion de session, à partir de [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql).
- Retourne les informations de requête (pour les requêtes actives) à partir de [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql). Vous pouvez également utiliser cette requête pour identifier les sessions bloquées, recherchez `request_blocked_by`. Pour plus d’informations sur les blocages, consultez [Collecter les informations de blocage](understand-resolve-blocking.md#gather-blocking-information). 
- Retourne le texte de la requête actuelle ou le texte de la mémoire tampon d’entrée à l’aide des vues DMV [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) ou [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql). Si les données retournées par le champ `text` de `sys.dm_exec_sql_text` ont la valeur NULL, cela signifie que la requête n’est pas en cours d’exécution mais qu’une transaction est en cours. Dans ce cas, le champ `event_info` de `sys.dm_exec_input_buffer` contient la dernière chaîne de commande transmise au moteur de base de données. 

```sql
SELECT [database_name] = db_name(s.database_id)
, tat.transaction_id, tat.transaction_begin_time, tst.session_id 
, session_open_transaction_count = tst.open_transaction_count --uncommitted and unrolled back transactions open. 
, transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime())
, input_buffer = ib.event_info
, request_text = CASE  WHEN r.statement_start_offset = 0 and r.statement_end_offset= 0 THEN left(est.text, 4000)
                       ELSE    SUBSTRING ( est.[text],    r.statement_start_offset/2 + 1, 
                                           CASE WHEN r.statement_end_offset = -1 THEN LEN (CONVERT(nvarchar(max), est.[text])) 
                                                ELSE r.statement_end_offset/2 - r.statement_start_offset/2 + 1
                                           END  )  END
, request_status = r.status
, request_blocked_by = r.blocking_session_id
, transaction_state = CASE tat.transaction_state    
                     WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                     WHEN 1 THEN 'The transaction has been initialized but has not started.'
                     WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                     WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                     WHEN 4 THEN 'The commit process has been initiated on the distributed transaction. This is for distributed transactions only. The distributed transaction is still active but further processing cannot take place.'
                     WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                     WHEN 6 THEN 'The transaction has been committed.'
                     WHEN 7 THEN 'The transaction is being rolled back.'
                     WHEN 8 THEN 'The transaction has been rolled back.' END 
, transaction_name = tat.name
, azure_dtc_state    --Applies to: Azure SQL Database only
             =    CASE tat.dtc_state 
                 WHEN 1 THEN 'ACTIVE'
                 WHEN 2 THEN 'PREPARED'
                 WHEN 3 THEN 'COMMITTED'
                 WHEN 4 THEN 'ABORTED'
                 WHEN 5 THEN 'RECOVERED' END
, transaction_type = CASE tat.transaction_type    WHEN 1 THEN 'Read/write transaction'
                                             WHEN 2 THEN 'Read-only transaction'
                                             WHEN 3 THEN 'System transaction'
                                             WHEN 4 THEN 'Distributed transaction' END
, tst.is_user_transaction
, local_or_distributed = CASE tst.is_local WHEN 1 THEN 'Local transaction, not distributed' WHEN 0 THEN 'Distributed transaction or an enlisted bound session transaction.' END
, transaction_uow    --for distributed transactions. 
, s.login_time, s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
, session_cpu_time = s.cpu_time, session_logical_reads = s.logical_reads, session_reads = s.reads, session_writes = s.writes
, observed = sysdatetimeoffset()
FROM sys.dm_tran_active_transactions AS tat 
INNER JOIN sys.dm_tran_session_transactions AS tst  on tat.transaction_id = tst.transaction_id
INNER JOIN Sys.dm_exec_sessions AS s on s.session_id = tst.session_id 
LEFT OUTER JOIN sys.dm_exec_requests AS r on r.session_id = s.session_id
CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib 
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) AS est;
```


### <a name="file-management-to-free-more-space"></a>Gestion des fichiers pour libérer de l’espace

Si le journal des transactions ne peut pas être tronqué, libérer de l’espace dans l’allocation des fichiers de base de données peut contribuer à résoudre le problème. Toutefois, il est indispensable de résoudre la racine de la condition empêchant la troncation du fichier journal de transactions. 

Dans certains cas, la création temporaire de plus d’espace disque permet l’exécution d’une transaction durable, ce qui supprime la condition empêchant la troncation du fichier journal des transactions lors d’une sauvegarde normale du journal des transactions. Toutefois, libérer de l’espace dans l’allocation peut constituer une résolution temporaire uniquement jusqu’à ce que le journal des transactions augmente de nouveau. 

Pour plus d’informations sur la gestion de l’espace de fichier des bases de données et des pools élastiques, consultez [Gérer l’espace de fichier des bases de données dans Azure SQL Database](file-space-manage.md).


### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Erreur 40552 : La session a été arrêtée en raison de l’utilisation excessive de l’espace réservé au journal des transactions

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Pour corriger ce problème, essayez les méthodes suivantes :

1. Ce problème est dû à des opérations d’insertion, de mise à jour ou de suppression. Passez en revue la transaction pour éviter les écritures inutiles. Essayez de réduire le nombre de lignes qui sont sollicitées immédiatement en implémentant le traitement par lot ou en les fractionnant en plusieurs transactions plus petites. Pour en savoir plus, consultez l’article [Comment utiliser le traitement par lots pour améliorer les performances des applications de base de données SQL](../performance-improve-use-batching.md).
2. Ce problème est dû à des opérations de reconstruction d’index. Pour éviter ce problème, assurez-vous que la formule suivante est vraie : (nombre de lignes affectées dans la table) multiplié par (taille moyenne du champ mis à jour en octets + 80) < 2 gigaoctets (Go). Pour les tables volumineuses, envisagez de créer des partitions et de procéder à la maintenance des index uniquement sur certaines partitions de la table. Pour plus d’informations, consultez l’article [Créer des tables partitionnées et des index](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=azuresqldb-current&preserve-view=true).
3. Si vous effectuez des insertions en bloc à l’aide de l’utilitaire `bcp.exe` ou de la classe `System.Data.SqlClient.SqlBulkCopy`, essayez d’utiliser les options `-b batchsize` ou `BatchSize` permettant de limiter le nombre de lignes copiées sur le serveur à chaque transaction. Pour plus d’informations, consultez [bcp Utility](/sql/tools/bcp-utility).
4. Si vous regénérez un index en utilisant l’instruction `ALTER INDEX`, essayez d’utiliser les `SORT_IN_TEMPDB = ON` et `ONLINE = ON`. Pour plus d’informations, consultez [ALTER INDEX (Transact-SQL)](/sql/t-sql/statements/alter-index-transact-sql).

> [!NOTE]
> Pour plus d’informations sur les autres erreurs de Resource Governor, consultez [Erreurs de gouvernance des ressources](troubleshoot-common-errors-issues.md#resource-governance-errors).

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes de connectivité et autres erreurs avec Azure SQL Database et Azure SQL Managed Instance](troubleshoot-common-errors-issues.md)
- [Résoudre les erreurs de connexion temporaires dans SQL Database et SQL Managed Instance](troubleshoot-common-connectivity-issues.md)


Pour plus d’informations sur la taille des journaux de transactions, consultez : 
- Pour connaître les limites de ressources vCore d’une base de données unique, consultez l’article consacré aux [limites de ressources pour les bases de données uniques suivant le modèle d’achat vCore](resource-limits-vcore-single-databases.md)
- Pour connaître les limites de ressources vCore des pools élastiques, consultez l’article consacré aux [limites de ressources pour les pools élastiques suivant le modèle d’achat vCore](resource-limits-vcore-elastic-pools.md)
- Pour connaître les limites de ressources DTU d’une base de données unique, consultez l’article consacré aux [limites de ressources pour les bases de données uniques suivant le modèle d’achat DTU](resource-limits-dtu-single-databases.md)
- Pour connaître les limites de ressources DTU des pools élastiques, consultez l’article consacré aux [limites de ressources pour les pools élastiques suivant le modèle d’achat DTU](resource-limits-dtu-elastic-pools.md)
- Pour connaître les limites de ressources pour SQL Managed Instance, consultez l’article sur les [limites de ressources pour SQL Managed Instance](../managed-instance/resource-limits.md).

