---
title: Résolution des problèmes de connectivité de Microsoft Azure SQL Database | Microsoft Docs
description: Cet article explique comment résoudre les problèmes de connectivité dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 9de6d85e1fc54d60f999cfa18665067b3998a432
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390676"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>Résolution des problèmes de connectivité de Microsoft Azure SQL Database

En cas d’échec de la connexion à Azure SQL Database, vous recevez des messages d’erreur. Ces problèmes de connexion peuvent découler d’une reconfiguration de Microsoft SQL Azure Database, des paramètres de pare-feu, de l’expiration d’un délai de connexion ou d’informations de connexion incorrectes. En outre, vous ne pourrez plus vous connecter à Azure SQL Database si la limite de certaines de ses ressources est atteinte.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Erreur 40613: La base de données < x > sur le serveur < y > n’est pas disponible actuellement

**Erreur détaillée**

``40613: Database <DBname> on server < server name > is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '< Tracing ID >'.``

Pour résoudre ce problème :

1. Recherchez des pannes connues dans le [tableau de bord des services Microsoft Azure](https://status.azure.com/status). 
2. S’il n’y a aucune panne connue, accédez au [site web du support Microsoft Azure](http://azure.microsoft.com/support/options) pour ouvrir un cas de support.

Pour plus d’informations, consultez [Dépannage de l’erreur « La base de données sur le serveur n’est pas disponible actuellement »](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>Une erreur liée au réseau ou propre à une instance s’est produite lors de l’établissement d’une connexion à SQL Server

Ce problème se produit si l’application ne peut pas se connecter au serveur.

Pour le résoudre, suivez la procédure pas-à-pas de la section intitulée [Étapes pour résoudre les problèmes de connexion courants](#steps-to-fix-common-connection-issues).

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Le serveur est introuvable ou inaccessible (Erreurs 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>Erreur 26 : Erreur lors de la localisation du serveur/de l’instance spécifiés

**Erreur détaillée**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>Erreur 40 : Impossible d’ouvrir une connexion à SQL Server

**Erreur détaillée**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Erreur 10053 : Une erreur de niveau transport s’est produite lors de la réception des résultats à partir du serveur.

**Erreur détaillée**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Ces problèmes se produisent car l’application ne peut pas se connecter au serveur.

Pour le résoudre, suivez la procédure pas-à-pas de la section intitulée [Étapes pour résoudre les problèmes de connexion courants](#steps-to-fix-common-connection-issues).

## <a name="cannot-connect-to-servername-due-to-firewall-issues"></a>Impossible de se connecter à <servername> en raison de problèmes de pare-feu

### <a name="error-40615-cannot-connect-to--servername-"></a>Erreur 40615 : Impossible de se connecter à <nom_serveur>

Pour résoudre ce problème, [configurez les paramètres du pare-feu sur SQL Database via le portail Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

### <a name="error-5-cannot-connect-to--servername-"></a>Erreur 5 : Impossible de se connecter à <nom_serveur>

Pour résoudre ce problème, vérifiez que le port 1433 est ouvert pour les connexions sortantes sur tous les pare-feu situés entre le client et Internet.

Pour plus d’informations, consultez [Configurer le Pare-feu Windows pour autoriser l’accès à SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Impossible de se connecter au serveur (Erreurs 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Échec de la connexion pour l’utilisateur '<nom_utilisateur>'

**Erreur détaillée**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Pour résoudre ce problème, contactez votre administrateur de service afin d’obtenir un nom d’utilisateur et un mot de passe SQL Server valides.

En règle générale, l’administrateur de service peut utiliser les étapes suivantes pour ajouter les informations d’identification de connexion :

1. Connectez-vous au serveur à l’aide de SQL Server Management Studio (SSMS).
2. Exécutez la requête SQL suivante pour vérifier que le nom de connexion est bien désactivé :

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Si le nom correspondant est désactivé, activez-le à l’aide de l’instruction suivante : 

   ```
   Alter login <User name> enable
   ```

4. Si le nom d’utilisateur de connexion SQL n’existe pas, créez-le en procédant comme suit :

   1. Dans SSMS, double-cliquez sur **Sécurité** pour le développer. 
   2. Cliquez avec le bouton droit sur **Connexions**, puis sélectionnez **Nouvelle connexion**. 
   3. Dans le script généré avec des espaces réservés, modifiez et exécutez la requête SQL suivante :
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Double-cliquez sur **Base de données**. 
6. Sélectionnez la base de données dont vous souhaitez autoriser l’accès.
7. Double-cliquez sur **Sécurité**. 
8. Cliquez avec le bouton droit sur **Utilisateurs**, puis sélectionnez **Nouvel utilisateur**. 
9. Dans le script généré avec des espaces réservés, modifiez et exécutez la requête SQL suivante : 

   ```
   CREATE USER <user_name, sysname, user_name>          
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```
   
   > [!NOTE]
   > Vous pouvez également utiliser `sp_addrolemember` pour mapper des utilisateurs spécifiques à des rôles de base de données spécifiques.

Pour en savoir plus, consultez [Gestion des bases de données et des connexions dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Erreurs de délai d’expiration de connexion

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904) : Délai d’expiration de la connexion dépassé

**Erreur détaillée**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904) : Délai expiré

**Erreur détaillée**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException: Échec du fournisseur sous-jacent sur Ouverture

**Erreur détaillée**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Impossible de se connecter à <nom_serveur>

**Erreur détaillée**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Ces exceptions peuvent se produire en raison de problèmes de connexion ou de requête. Pour confirmer que cette erreur est due à des problèmes de connectivité, consultez la section [Vérifier si une erreur est due à un problème de connectivité](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Des expirations de délai de connexion se produisent car l’application ne peut pas se connecter au serveur. Pour le résoudre, suivez la procédure pas-à-pas de la section intitulée [Étapes pour résoudre les problèmes de connexion courants](#steps-to-fix-common-connection-issues).

## <a name="transient-errors-errors-40197-40545"></a>Erreurs temporaires (Erreurs 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Erreur 40197 : Le service a rencontré une erreur lors du traitement de votre demande. Réessayez. Code d’erreur <code>

Ce problème est dû à une erreur temporaire rencontrée lors de la reconfiguration ou du basculement sur le back-end.

Pour résoudre ce problème, patientez un peu et réessayez. Il n’est pas nécessaire d’ouvrir un cas de support, sauf si le problème persiste.

Nous vous recommandons de vérifier qu’une logique de nouvelle tentative est en place. Pour plus d’informations sur la logique de nouvelle tentative, consultez [Gestion des problèmes de connexion et des erreurs temporaires de base de données SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="connection-terminated-because-of-a-system-defined-limit"></a>La connexion a été interrompue en raison d’une limite définie par le système

### <a name="error-10928-resource-id-d"></a>Erreur 10928 : ID de la ressource : %d

**Erreur détaillée**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Pour contourner ce problème, essayez d’appliquer l’une des méthodes suivantes :

* Vérifiez s’il existe des requêtes de longue durée.

  > [!NOTE]
  > Il s’agit d’une approche minimaliste. Elle risque de ne pas suffire pour résoudre le problème.

  1. Exécutez la requête SQL suivante pour vérifier la vue [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql), afin de voir toutes les requêtes bloquantes :

             ```
             SELECT * FROM dm_exec_requests
             ```

  2. Déterminez la **mémoire tampon d’entrée** pour le bloqueur d’en-tête.
  3. Paramétrez la requête du bloqueur d’en-tête.

    Pour obtenir une procédure de dépannage détaillée, consultez [Is my query running fine in the cloud?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx) (Ma requête s’exécute-t-elle correctement dans le cloud ?).

* Si la base de données atteint constamment sa limite malgré la résolution des problèmes liés aux requêtes longues et bloquantes, effectuez une mise à niveau vers l’une des nouvelles éditions Preview (par exemple [Standard ou Premium](https://azure.microsoft.com/pricing/details/sql-database/)).

Pour plus d’informations sur les options de tarification de SQL Database, veuillez consulter la page [Tarification Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

Pour plus d’informations sur les vues de gestion dynamique, veuillez consulter la page [Vues de gestion dynamique système](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Pour plus d’informations sur ce message d’erreur, consultez [Limites de ressources SQL Database des serveurs Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Erreur 10929 : ID de ressource : 1

**Erreur détaillée**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

Pour plus d’informations sur cette erreur, veuillez consulter [Messages d’erreur pour les programmes clients SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages).

### <a name="error-40501-the-service-is-currently-busy"></a>Erreur 40501 : Le service est actuellement occupé.

**Erreur détaillée**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Il s’agit d’une erreur de limitation du moteur, indiquant que les limites de ressources sont dépassées.

Pour plus d’informations sur les limites de ressources, consultez [Limites de ressources du serveur de base de données](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Erreur 40544 : La base de données a atteint son quota de taille.

**Erreur détaillée**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Cette erreur se produit quand la base de données a atteint son quota de taille.

La procédure suivante peut vous aider à contourner le problème ou vous fournir des options supplémentaires :

1. Vérifiez la taille actuelle de la base de données à l’aide du tableau de bord dans le portail Azure.

   > [!NOTE]
   > Pour identifier les tables qui consomment le plus d’espace et les candidats potentiels au nettoyage, exécutez la requête SQL suivante :

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Si la taille actuelle ne dépasse pas la taille maximale prise en charge pour votre édition, vous pouvez utiliser ALTER DATABASE pour augmenter le paramètre MAXSIZE. 
3. Si la base de données dépasse déjà la taille maximale prise en charge pour votre édition, essayez l’une de ces procédures :
   - Nettoyez normalement votre base de données. Par exemple, nettoyez les données indésirables en utilisant truncate/delete. Vous pouvez aussi déplacer les données à l’aide de SQL Server Integration Services (SSIS) ou de l’utilitaire Bulk Copy Program (BCP).
   - Partitionnez ou supprimez des données, supprimez des index ou consultez la documentation pour connaître les résolutions possibles.

   - Pour plus d’informations sur la mise à l’échelle des bases de données, consultez [Mettre à l’échelle des ressources de base de données unique](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) et [Mettre à l’échelle des ressources de pool élastique](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Erreur 40549 : La session est arrêtée, car l’une des transactions est de longue durée.

**Erreur détaillée**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Si cette erreur survient à plusieurs reprises, vous pouvez essayer de la résoudre en procédant ainsi : 

1. Consultez la vue sys.dm_exec_requests pour voir toutes les sessions ouvertes qui ont une valeur élevée pour la colonne total_elapsed_time. Cette vérification s’effectue en exécutant le script SQL suivant :

   ```
   SELECT * FROM dm_exec_requests
   ```
2. Déterminez la mémoire tampon d’entrée pour la longue requête. 
3. Ajustez la requête.

Vous pouvez également traiter vos requêtes par lot. Pour plus d’informations sur le traitement par lot, consultez [Comment utiliser le traitement par lot pour améliorer les performances des applications de base de données SQL](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Pour obtenir une procédure de dépannage détaillée, consultez [Is my query running fine in the cloud?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx) (Ma requête s’exécute-t-elle correctement dans le cloud ?).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Erreur 40551 : La session a été arrêtée en raison d’une utilisation excessive de TEMPDB

**Erreur détaillée**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Pour contourner ce problème, effectuez les étapes suivantes :

1. Modifiez les requêtes afin de réduire l’utilisation de l’espace de table temporaire. 
2. Supprimez les objets temporaires une fois qu’ils ne sont plus nécessaires. 
3. Tronquez les tables ou supprimez les tables inutilisées.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Erreur 40552 : La session a été arrêtée en raison de l’utilisation excessive de l’espace réservé au journal des transactions

**Erreur détaillée**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Pour corriger ce problème, essayez les méthodes suivantes :

* Ce problème est dû à des opérations d’insertion, de mise à jour ou de suppression. Essayez de réduire le nombre de lignes qui sont sollicitées immédiatement en implémentant le traitement par lot ou en les fractionnant en plusieurs transactions plus petites.
* Ce problème est dû à des opérations de reconstruction d’index. Pour contourner ce problème, veillez à respecter la formule suivante : nombre de lignes affectées dans la table * (taille moyenne du champ mis à jour en octets + 80) < 2 Go

  > [!NOTE]
  > Pour la reconstruction d’index, la taille moyenne du champ mis à jour doit être remplacée par la taille moyenne de l’index.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Erreur 40553 : La session a été arrêtée en raison d’une utilisation excessive de la mémoire

**Erreur détaillée**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Pour contourner ce problème, essayez d’optimiser la requête.

Pour obtenir une procédure de dépannage détaillée, consultez [Is my query running fine in the cloud?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx) (Ma requête s’exécute-t-elle correctement dans le cloud ?).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Impossible d’ouvrir la base de données « Master » demandée par la connexion. La connexion a échoué.

Ce problème se produit car le compte ne dispose pas de l’autorisation d’accès à la base de données MASTER. Toutefois, par défaut, SQL Server Management Studio (SSMS) tente de se connecter à la base de données MASTER.

Pour résoudre ce problème, effectuez les étapes suivantes :

1. Sur l’écran de connexion de SSMS, sélectionnez **Options**, puis **Propriétés de la connexion**. 
2. Dans le champ **Connexion à une base de données**, entrez le nom de la base de données par défaut de l’utilisateur comme base de données de connexion par défaut, puis sélectionnez **Se connecter**.

   ![Propriétés de connexion](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Vérifier si une erreur est due à un problème de connectivité

Pour vérifier si une erreur est due à un problème de connectivité, consultez la trace de la pile pour les frames qui montrent des appels visant à ouvrir une connexion, comme les suivants (notez la référence à la classe **SqlConnection**) :

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Quand l’exception est déclenchée par des problèmes de requête, vous remarquerez une pile des appels semblable à la suivante (notez la référence à la classe **SqlCommand**). Dans ce cas, [affinez vos requêtes](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
Pour obtenir des conseils supplémentaires sur l’affinage des performances, consultez les ressources suivantes :

* [Comment mettre à jour les index et les statistiques Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Ajustement manuel des performances de requêtes dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Supervision des performances d’Azure SQL Database à l’aide de vues de gestion dynamique](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Utilisation du Magasin des requêtes dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>Étapes pour résoudre les problèmes de connexion courants

1. Vérifiez que l’adresse TCP/IP est activée en tant que protocole client sur le serveur d’applications. Pour plus d’informations, consultez [Configurer des protocoles clients](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Sur les serveurs d’applications où aucun outil SQL Server n’est installé, vérifiez que l’adresse TCP/IP est activée en exécutant **cliconfg.exe** (utilitaire réseau du client SQL Server). 
2. Vérifiez la chaîne de connexion de l’application pour être sûr qu’elle est configurée correctement. Par exemple, vérifiez que la chaîne de connexion spécifie le port correct (1433) et le nom complet du serveur.
Voir [Obtenir des informations de connexion SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Essayez d’augmenter la valeur du délai d’expiration de connexion. Nous vous recommandons d’utiliser un délai d’expiration de connexion d’au moins 30 secondes. 
4. Testez la connectivité entre le serveur d’applications et la base de données SQL Azure à l’aide de [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), un fichier UDL, ping ou Telnet. Pour plus d’informations, consultez [Résolution des erreurs de connectivité à SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) et [Diagnostics pour les problèmes de connectivité](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > En guise d’étape de résolution des problèmes, vous pouvez également tester la connectivité sur un autre ordinateur client.

5. Nous vous recommandons de vérifier que la logique de nouvelle tentative est en place. Pour plus d’informations sur la logique de nouvelle tentative, consultez [Gestion des problèmes de connexion et des erreurs temporaires de Microsoft Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Si ces procédures ne permettent pas de résoudre votre problème, essayez de collecter davantage de données, puis contactez le support. Si votre application est un service cloud, activez la journalisation. Cette étape retourne l’horodatage UTC de l’échec. En outre, SQL Azure retourne l’ID de suivi. Les [services de support technique Microsoft](http://azure.microsoft.com/support/options/) peuvent utiliser ces informations. 

Pour plus d’informations sur la façon d’activer la journalisation, consultez [Activer la journalisation des diagnostics pour les applications dans Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

**Documents associés**

* [Architecture de connectivité Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [Contrôles d’accès réseau Azure SQL Database et Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
