---
title: Résoudre les problèmes liés aux connecteurs Azure Synapse Analytics, Azure SQL Database et SQL Server
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés aux connecteurs Azure Synapse Analytics, Azure SQL Database et SQL Server dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 84208764621bd03959db7d695bf0616dcccc4491
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064777"
---
# <a name="troubleshoot-the-azure-synapse-analytics-azure-sql-database-and-sql-server-connectors-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés aux connecteurs Azure Synapse Analytics, Azure SQL Database et SQL Server dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés aux connecteurs Azure Synapse Analytics, Azure SQL Database et SQL Server dans Azure Data Factory et Azure Synapse.

## <a name="error-code-sqlfailedtoconnect"></a>Code d’erreur : SqlFailedToConnect

- **Message** : `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Causes et recommandations** : Différentes causes peuvent être à l’origine de cette erreur. Consultez la liste ci-dessous pour obtenir une analyse des causes possibles et des recommandations associées.

    | Analyse de la cause                                               | Recommandation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Pour Azure SQL, si le message d’erreur contient la chaîne « SqlErrorNumber=47073 », cela signifie que l’accès au réseau public est refusé dans le paramètre de connectivité. | Dans le pare-feu Azure SQL, définissez l’option **Refuser l’accès au réseau public** sur *Non*. Pour plus d’informations, consultez les [paramètres de connectivité Azure SQL](../azure-sql/database/connectivity-settings.md#deny-public-network-access). |
    | Pour Azure SQL, si le message d’erreur contient un code d’erreur SQL, par exemple « SqlErrorNumber=[errorcode] », consultez le guide de résolution des problèmes Azure SQL. | Pour obtenir une recommandation, consultez [Résolution des problèmes de connectivité et autres erreurs avec Azure SQL Database et Azure SQL Managed Instance](../azure-sql/database/troubleshoot-common-errors-issues.md). |
    | Vérifiez si le port 1433 figure sur la liste d’autorisation du pare-feu. | Pour plus d’informations, consultez [Ports utilisés par SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Si le message d’erreur contient la chaîne « SqlException »,SQL Database génère l’erreur indiquant qu’une opération spécifique a échoué. | Pour plus d’informations, effectuez une recherche par code d’erreur SQL dans [Erreurs du moteur de base de données](/sql/relational-databases/errors-events/database-engine-events-and-errors). Si vous avez besoin d’aide supplémentaire, contactez le support Azure SQL. |
    | S’il s’agit d’un problème temporaire (par exemple, une connexion réseau instable), ajoutez une nouvelle tentative dans la stratégie d’activité à atténuer. | Pour plus d’informations, consultez [Pipelines et activités](./concepts-pipelines-activities.md#activity-policy). |
    | Si le message d’erreur contient la chaîne « Le client avec l’adresse IP "…" n’est pas autorisé à accéder au serveur » et que vous essayez de vous connecter à Azure SQL Database, l’erreur est généralement causée par un problème de pare-feu Azure SQL Database. | Dans Configuration du pare-feu Azure SQL Server, activez l’option **Autoriser les services et les ressources Azure à accéder à ce serveur**. Pour plus d’informations, consultez [Règles de pare-feu IP Azure SQL Database et Azure Synapse](../azure-sql/database/firewall-configure.md). |
    
## <a name="error-code-sqloperationfailed"></a>Code d’erreur : SqlOperationFailed

- **Message** : `A database operation failed. Please search error to get more details.`

- **Causes et recommandations** : Différentes causes peuvent être à l’origine de cette erreur. Consultez la liste ci-dessous pour obtenir une analyse des causes possibles et des recommandations associées.

    | Analyse de la cause                                               | Recommandation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Si le message d’erreur contient la chaîne « SqlException », SQL Database génère l’erreur indiquant qu’une opération spécifique a échoué. | Si l’erreur SQL n’est pas claire, essayez de régler la base de données sur le niveau de compatibilité « 150 » le plus récent. Cela peut lever des erreurs SQL de dernière version. Pour plus d’informations, consultez la [documentation](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Pour plus d’informations sur la résolution des problèmes SQL, effectuez une recherche par code d’erreur SQL dans [Erreurs du moteur de base de données](/sql/relational-databases/errors-events/database-engine-events-and-errors). Si vous avez besoin d’aide supplémentaire, contactez le support Azure SQL. |
    | Si le message d’erreur contient la chaîne « PdwManagedToNativeInteropException », cela est généralement dû à une incompatibilité entre les tailles de colonne source et récepteur. | Vérifiez la taille des colonnes source et récepteur. Si vous avez besoin d’aide supplémentaire, contactez le support Azure SQL. |
    | Si le message d’erreur contient la chaîne « InvalidOperationException », cela est généralement dû à des données d’entrée non valides. | Pour identifier la ligne qui a rencontré le problème, activez la fonctionnalité de tolérance de panne sur l’activité de copie, qui peut rediriger les lignes problématiques vers le stockage pour une investigation plus poussée. Pour plus d’informations, consultez [Tolérance de panne de l’activité de copie](./copy-activity-fault-tolerance.md). |


## <a name="error-code-sqlunauthorizedaccess"></a>Code d’erreur : SqlUnauthorizedAccess

- **Message** : `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Cause** : Les informations d’identification sont incorrectes ou le compte de connexion ne peut pas accéder à la base de données SQL.

- **Recommandation** :  Vérifiez que le compte de connexion dispose des autorisations suffisantes pour accéder à la base de données SQL.


## <a name="error-code-sqlopenconnectiontimeout"></a>Code d’erreur : SqlOpenConnectionTimeout

- **Message** : `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Cause** : Il peut s’agir d’un échec temporaire de la base de données SQL.

- **Recommandation** :  Réessayez de mettre à jour la chaîne de connexion de service lié avec une valeur de délai d’attente de connexion plus importante.


## <a name="error-code-sqlautocreatetabletypemapfailed"></a>Code d’erreur : SqlAutoCreateTableTypeMapFailed

- **Message** : `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Cause** : La table de création automatique ne peut pas répondre aux exigences de la source.

- **Recommandation** :  Mettez à jour le type de colonne dans *mappages* ou créez manuellement la table du récepteur dans le serveur cible.


## <a name="error-code-sqldatatypenotsupported"></a>Code d’erreur : SqlDataTypeNotSupported

- **Message** : `A database operation failed. Check the SQL errors.`

- **Cause** : Si le problème se produit sur la source SQL et que l’erreur est liée au dépassement de SqlDateTime, la valeur des données est supérieure à la plage du type logique (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Recommandation** :  Castez le type en chaîne dans la requête SQL source ou, dans le mappage de colonne d’activité de copie, modifiez le type de colonne en *Chaîne*.

- **Cause** : Si le problème se produit sur un récepteur SQL et que l’erreur est liée au dépassement de SqlDateTime, la valeur des données est supérieure à la plage autorisée dans la table du récepteur.

- **Recommandation** :  Mettez à jour le type de colonne correspondant en type *datetime2* dans la table du récepteur.


## <a name="error-code-sqlinvaliddbstoredprocedure"></a>Code d’erreur : SqlInvalidDbStoredProcedure

- **Message** : `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causes et recommandations** : Différentes causes peuvent être à l’origine de cette erreur. Consultez la liste ci-dessous pour obtenir une analyse des causes possibles et des recommandations associées.

  | Analyse de la cause                                               | Recommandation                                             |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | La procédure stockée spécifiée n’est pas valide. | Validez la procédure stockée à l’aide des outils SQL. Assurez-vous que la procédure stockée peut retourner des données.  |
  | L’activité Lookup requiert que la procédure stockée renvoie une valeur, mais le code de la procédure stockée ne renvoie aucune valeur. | Utilisez l’activité Stored Procedure si la procédure stockée est supposée ne renvoyer aucune donnée. |

## <a name="error-code-sqlinvaliddbquerystring"></a>Code d’erreur : SqlInvalidDbQueryString

- **Message** : `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Cause** : La requête SQL spécifiée n’est pas valide. Cela peut être dû au fait que la requête ne retourne aucune donnée.

- **Recommandation** :  Validez la requête SQL à l’aide des outils SQL. Assurez-vous que la requête peut retourner des données.


## <a name="error-code-sqlinvalidcolumnname"></a>Code d’erreur : SqlInvalidColumnName

- **Message** : `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Cause** : La colonne est introuvable, car la configuration est peut-être incorrecte.

- **Recommandation** :  Vérifiez la colonne dans la requête, *structure* dans le jeu de données et *mappages* dans l’activité.


## <a name="error-code-sqlbatchwritetimeout"></a>Code d’erreur : SqlBatchWriteTimeout

- **Message** : `Timeouts in SQL write operation.`

- **Cause** : Le problème peut être dû à un échec temporaire de la base de données SQL.

- **Recommandation** :  Retentez l’opération. Si le problème persiste, contactez le support Azure SQL.


## <a name="error-code-sqlbatchwritetransactionfailed"></a>Code d’erreur : SqlBatchWriteTransactionFailed

- **Message** : `SQL transaction commits failed.`

- **Cause** : Si les détails de l’exception indiquent constamment une expiration du délai de la transaction, la latence du réseau entre le runtime d’intégration et la base de données est supérieure au seuil par défaut, qui est de 30 secondes.

- **Recommandation** :  Mettez à jour la chaîne de connexion du service lié à SQL avec une valeur *délai de connexion* égale ou supérieure à 120, puis réexécutez l’activité.

- **Cause** : Si les détails de l’exception indiquent par intermittence que la connexion SQL est interrompue, il peut s’agir d’un échec temporaire du réseau ou d’un problème du côté de la base de données SQL.

- **Recommandation** :  Réessayez l’activité et passez en revue les métriques du côté de la base de données SQL.


## <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Code d’erreur : SqlBulkCopyInvalidColumnLength

- **Message** : `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Cause** : La copie en bloc SQL a échoué à cause d’une longueur de colonne non valide reçue du client de l’utilitaire de copie en bloc (bcp).

- **Recommandation** :  Pour identifier la ligne qui a rencontré le problème, activez la fonctionnalité de tolérance de panne sur l’activité de copie. Cela peut rediriger les lignes problématiques vers le stockage pour une investigation plus poussée. Pour plus d’informations, consultez [Tolérance de panne de l’activité de copie](./copy-activity-fault-tolerance.md).


## <a name="error-code-sqlconnectionisclosed"></a>Code d’erreur : SqlConnectionIsClosed

- **Message** : `The connection is closed by SQL Database.`

- **Cause** : La connexion SQL est fermée par la base de données SQL lorsque le nombre d’exécutions simultanées est élevé et que le serveur met fin à la connexion.

- **Recommandation** :  Relancez la connexion. Si le problème persiste, contactez le support Azure SQL.

## <a name="error-code-sqlserverinvalidlinkedservicecredentialmissing"></a>Code d’erreur : SqlServerInvalidLinkedServiceCredentialMissing

- **Message** : `The SQL Server linked service is invalid with its credential being missing.`

- **Cause** : Le service lié n’a pas été configuré correctement.

- **Recommandation** : Validez et corrigez le service lié SQL Server. 

## <a name="error-code-sqlparallelfailedtodetectpartitioncolumn"></a>Code d’erreur : SqlParallelFailedToDetectPartitionColumn

- **Message** : `Failed to detect the partition column with command '%command;', %message;.`

- **Cause** : Il n’existe aucune clé primaire ou clé unique dans la table.

- **Recommandation** : Vérifiez la table pour vous assurer qu’une clé primaire ou un index unique est créé. 

## <a name="error-code-sqlparallelfailedtodetectphysicalpartitions"></a>Code d’erreur : SqlParallelFailedToDetectPhysicalPartitions

- **Message** : `Failed to detect the physical partitions with command '%command;', %message;.`

- **Cause** : Aucune partition physique n’est créée pour la table. Vérifiez votre base de données.

- **Recommandation** : Référencez [Create Partitioned Tables and Indexes](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=sql-server-ver15&preserve-view=true) (Créer des tables partitionnées et des index) pour résoudre ce problème.

## <a name="error-code-sqlparallelfailedtogetpartitionrangesynapse"></a>Code d’erreur : SqlParallelFailedToGetPartitionRangeSynapse

- **Message** : `Failed to get the partitions for azure synapse with command '%command;', %message;.`

- **Cause** : Aucune partition physique n’est créée pour la table. Vérifiez votre base de données.

- **Recommandation** : Référencez les [tables de partitionnement dans un pool SQL dédié](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-partition.md) pour résoudre ce problème.

## <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Message d’erreur : Échec lors de la conversion d’une chaîne de caractères en valeur de type uniqueidentifier

- **Symptômes** : Lorsque vous copiez des données d’une source de données tabulaire (telle que SQL Server) vers Azure Synapse Analytics à l’aide de la copie intermédiaire et de PolyBase, vous recevez l’erreur suivante :

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Cause** : Azure Synapse Analytics PolyBase ne peut pas convertir une chaîne vide en GUID.

- **Résolution** : Dans le récepteur de l’activité de copie, sous les paramètres de PolyBase, définissez l’option **utiliser le type par défaut** sur *false*.


## <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Message d’erreur : Type de données attendu : DECIMAL(x,x), valeur incriminée

- **Symptômes** : Lorsque vous copiez des données d’une source de données tabulaire (telle que SQL Server) vers Azure Synapse Analytics à l’aide de la copie intermédiaire et de PolyBase, vous recevez l’erreur suivante :

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Cause** : Azure Synapse Analytics PolyBase ne peut pas insérer une chaîne vide (valeur Null) dans une colonne décimale.

- **Résolution** : Dans le récepteur de l’activité de copie, sous les paramètres de PolyBase, définissez l’option **utiliser le type par défaut** sur false.


## <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Message d’erreur : Message d’exception Java : HdfsBridge::CreateRecordReader

- **Symptômes** : Vous copiez des données dans Azure Synapse Analytics à l’aide de PolyBase et vous recevez l’erreur suivante :

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Cause** : Cela peut être dû au fait que le schéma (largeur totale de colonne) est trop volumineux (plus de 1 Mo). Vérifiez le schéma de la table Azure Synapse Analytics cible en ajoutant la taille de toutes les colonnes :

    - Int = 4 octets
    - Bigint = 8 octets
    - Varchar(n), char(n), binary(n), varbinary(n) = n octets
    - Nvarchar(n), nchar(n) = n*2 octets
    - Date = 6 octets
    - Datetime/(2), smalldatetime = 16 octets
    - Datetimeoffset = 20 octets
    - Decimal = 19 octets
    - Float = 8 octets
    - Money = 8 octets
    - Smallmoney = 4 octets
    - Real = 4 octets
    - Smallint = 2 octets
    - Time = 12 octets
    - Tinyint = 1 octets

- **Résolution** : 
    - Réduisez la largeur de colonne sur une taille inférieure à 1 Mo.
    - Ou, utilisez une approche d’insertion en bloc en désactivant PolyBase.


## <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Message d’erreur : La condition spécifiée avec un ou plusieurs en-têtes conditionnels HTTP n’est pas remplie

- **Symptômes** : Vous utilisez la requête SQL pour extraire des données d’Azure Synapse Analytics et vous recevez l’erreur suivante :

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Cause** : Azure Synapse Analytics a rencontré un problème en interrogeant la table externe dans Stockage Azure.

- **Résolution** : Exécutez la même requête dans SQL Server Management Studio (SSMS) et vérifiez si vous obtenez le même résultat. Si c’est le cas, ouvrez un ticket de support pour Azure Synapse Analytics et indiquez le nom de votre base de données et de votre serveur Azure Synapse Analytics.


## <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Le niveau de performance est faible et entraîne un échec de la copie

- **Symptômes** : Vous copiez des données dans Azure SQL Database et vous recevez l’erreur suivante : `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Cause** : Azure SQL Database S1 a atteint les limites d’entrée/sortie (E/S).

- **Résolution** : Mettez à niveau le niveau de performance d’Azure SQL Database pour corriger le problème. 


## <a name="sql-table-cant-be-found"></a>Table SQL introuvable 

- **Symptômes** : Vous copiez des données d’une table hybride vers une table SQL Server locale et vous recevez l’erreur suivante : `Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Cause** : Le compte SQL actuel ne dispose pas d’autorisations suffisantes pour exécuter les requêtes émises par .NET SqlBulkCopy.WriteToServer.

- **Résolution** : Passez à un compte SQL dotée de privilèges supérieurs.


## <a name="error-message-string-or-binary-data-is-truncated"></a>Message d’erreur : Les données de type chaîne ou binaire sont tronquées

- **Symptômes** : Une erreur se produit lorsque vous copiez des données dans une table Azure SQL Server locale. 

- **Cause** : La définition du schéma de table SQL Cx présente une ou plusieurs colonnes dont la longueur est inférieure à celle attendue.

- **Résolution** : Pour résoudre ce problème, essayez d’effectuer les étapes suivantes :

    1. Pour corriger les lignes problématiques, appliquez la [tolérance de panne](./copy-activity-fault-tolerance.md) du récepteur SQL, en particulier « redirectIncompatibleRowSettings ».

        > [!NOTE]
        > La tolérance de panne peut nécessiter une durée d’exécution supplémentaire, pouvant entraîner des coûts plus élevés.

    2. Examinez les données redirigées par rapport à la longueur de colonne du schéma de table SQL pour identifier les colonnes qui doivent être mises à jour.

    3. Mettez à jour le schéma de table en conséquence.

## <a name="error-code-faileddboperation"></a>Code d’erreur : FailedDbOperation

- **Message** : `User does not have permission to perform this action.`

- **Recommandation** : Assurez-vous que l’utilisateur configuré dans le connecteur Azure Synapse Analytics doit disposer de l’autorisation « CONTROL » sur la base de données cible lors de l’utilisation de PolyBase pour charger des données. Pour des informations plus détaillées, consultez [ce document](./connector-azure-sql-data-warehouse.md#required-database-permission).

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
