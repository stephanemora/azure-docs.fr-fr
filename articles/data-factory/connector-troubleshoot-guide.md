---
title: Résoudre les problèmes liés aux connecteurs dans Azure Data Factory
description: Découvrez comment résoudre les problèmes liés aux connecteurs dans Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 7e818a80f9df97ce4e5f5b8f1ef9385d2e6b0ecc
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827758"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Résoudre les problèmes liés aux connecteurs dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés aux connecteurs dans Azure Data Factory.
  

## <a name="azure-blob-storage"></a>Stockage Blob Azure

### <a name="error-code--azurebloboperationfailed"></a>Code d’erreur :  AzureBlobOperationFailed

- **Message** : `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Cause** : Problème d’accès à l’opération de stockage d’objets Blob.

- **Recommandation** :  Pour plus d’informations, consultez l’erreur. Reportez-vous au document d’aide sur les blobs : https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Contactez l’équipe de stockage si vous avez besoin d’aide.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Code d’erreur :  AzureBlobServiceNotReturnExpectedDataLength

- **Message** : `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Code d’erreur :  AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Message** : `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Code d’erreur :  AzureStorageOperationFailedConcurrentWrite

- **Message** : `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Message d’erreur : La taille de la demande est trop grande

- **Symptômes** : Vous copiez des données dans Azure Cosmos DB avec la taille du lot d’écriture par défaut et vous rencontrez l’erreur *« **La taille de la demande est trop grande** »* .

- **Cause** : Cosmos DB limite la taille d’une demande unique à 2 Mo. La formule est la suivante : Taille de la demande = Taille de document unique * Taille du lot d’écriture. Si la taille de votre document est importante, le comportement par défaut entraîne une trop grande taille de demande. Vous pouvez ajuster la taille du lot d’écriture.

- **Résolution** : Dans le récepteur de l’activité de copie, réduisez la taille du lot d’écriture « Write batch size » (la valeur par défaut est 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Message d’erreur : Violation de contrainte d’index unique

- **Symptômes** : Lorsque vous copiez des données dans Cosmos DB, vous rencontrez l’erreur suivante :

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Cause** : Il existe deux causes possibles :

    - Si vous utilisez **Insérer** comme comportement d’écriture, cette erreur signifie que vos données sources ont des lignes/objets avec le même ID.

    - Si vous utilisez **Upsert** comme comportement d’écriture et que vous définissez une autre clé unique sur le conteneur, cette erreur signifie que vos données sources ont des lignes/objets avec des ID différents, mais une même valeur pour la clé unique définie.

- **Résolution** : 

    - Pour cause1, définissez **Upsert** comme comportement d’écriture.
    - Pour cause2, assurez-vous que chaque document a une valeur différente pour la clé unique définie.

### <a name="error-message-request-rate-is-large"></a>Message d’erreur : Le taux de demandes est élevé

- **Symptômes** : Lorsque vous copiez des données dans Cosmos DB, vous rencontrez l’erreur suivante :

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Cause** : Les unités de demande utilisées sont plus volumineuses que l’unité de requête disponible configurée dans Cosmos DB. Découvrez [ici](../cosmos-db/request-units.md#request-unit-considerations) comment Cosmos DB calcule l’unité de requête.

- **Résolution** : Il existe deux solutions :

    1. **Augmentez l’unité de requête du conteneur** dans Cosmos DB, ce qui améliorera les performances de l’activité de copie, mais engendrera des coûts plus élevés dans Cosmos DB. 

    2. Réduisez la valeur **writeBatchSize** (à 1000, par exemple) et spécifiez une valeur plus petite pour **parallelCopies**, telle que 1, ce qui réduira les performances d’exécution de la copie mais n’entraînera pas de frais supplémentaires dans Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Colonne manquante dans le mappage de colonnes

- **Symptômes** : Lorsque vous importez un schéma pour Cosmos DB pour le mappage de colonnes, certaines colonnes sont manquantes. 

- **Cause** : ADF déduit le schéma des 10 premiers documents Cosmos DB. Si certaines colonnes/propriétés n’ont pas de valeur dans ces documents, elles ne sont pas détectées par ADF et ne s’affichent donc pas.

- **Résolution** : Vous pouvez ajuster la requête comme ci-dessous pour forcer l’affichage de la colonne dans le jeu de résultats avec une valeur vide : (supposons que la colonne « impossible » est manquante dans les 10 premiers documents). Vous pouvez également ajouter manuellement la colonne pour le mappage.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Message d’erreur : Le GuidRepresentation pour le lecteur est CSharpLegacy

- **Symptômes** : En copiant des données à partir de Cosmos DB MongoAPI/MongoDB avec le champ UUID, vous rencontrez l’erreur suivante :

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Cause** : Il existe deux façons de représenter UUID dans BSON : UuidStandard et UuidLegacy. Par défaut, UuidLegacy est utilisé pour lire les données. Vous rencontrerez cette erreur si vos données UUID dans MongoDB sont UuidStandard.

- **Résolution** : Dans la chaîne de connexion MongoDB, ajoutez l’option « **uuidRepresentation=standard** ». Pour plus d’informations, consultez [Chaîne de connexion MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Code d’erreur :  AdlsGen2OperationFailed

- **Message** : `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Cause** : ADLS Gen2 génère l’erreur indiquant que l’opération a échoué.

- **Recommandation** :  Consultez le message d’erreur détaillé généré par ADLS Gen2. Si l’erreur est causée par une défaillance temporaire, réessayez. Si vous avez besoin d’aide supplémentaire, contactez le support du stockage Azure et fournissez l’ID de la requête dans le message d’erreur.

- **Cause** : Lorsque le message d’erreur contient « Forbidden », le principal du service ou l’identité gérée que vous utilisez ne dispose peut-être pas des autorisations suffisantes pour accéder à ADLS Gen2.

- **Recommandation** :  Reportez-vous au document d’aide : https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Cause** : Lorsque le message d’erreur contient « InternalServerError », l’erreur est retournée par ADLS Gen2.

- **Recommandation** :  Cela peut être dû à un échec temporaire, réessayez. Si le problème persiste, contactez le support du stockage Azure et fournissez l’ID de la requête dans le message d’erreur.


### <a name="error-code--adlsgen2invalidurl"></a>Code d’erreur :  AdlsGen2InvalidUrl

- **Message** : `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Code d’erreur :  AdlsGen2InvalidFolderPath

- **Message** : `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Code d’erreur :  AdlsGen2OperationFailedConcurrentWrite

- **Message** : `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Code d’erreur :  AdlsGen2TimeoutError

- **Message** : `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Message d’erreur : Le serveur distant a retourné une erreur : (403) Interdit

- **Symptômes** : L’activité de copie échoue avec l’erreur suivante : 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Cause** : L’une des causes possibles est que le principal du service ou l’identité managée que vous utilisez n’a pas l’autorisation d’accéder au dossier/fichier donné.

- **Résolution** : Accordez des autorisations appropriées sur tous les dossiers et sous-dossiers que vous devez copier. Consultez [ce document](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Message d’erreur : Échec d’obtention du jeton d’accès à l’aide du principal du service. Erreur ADAL : service_unavailable

- **Symptômes** : L’activité de copie échoue avec l’erreur suivante :

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Cause** : Lorsque le service d’émission de jeton de sécurité (STS) détenu par Azure Active Directory n’est pas disponible, c.-à-d. qu’il est trop occupé pour gérer les demandes, il renvoie une erreur HTTP 503. 

- **Résolution** : Réexécutez l’activité de copie au bout de quelques minutes.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Data Warehouse/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Code d’erreur :  SqlFailedToConnect

- **Message** : `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Cause** : Si le message d’erreur contient « SqlException »,SQL Database génère l’erreur indiquant qu’une opération spécifique a échoué.

- **Recommandation** :  Pour plus d’informations, effectuez une recherche par code d’erreur SQL dans ce document de référence : https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Si vous avez besoin d’aide supplémentaire, contactez le support Azure SQL.

- **Cause** : Si le message d’erreur contient « Le client avec l’adresse IP "…" n’est pas autorisé à accéder au serveur » et que vous essayez de vous connecter à Azure SQL Database, cela est généralement causé par un problème de pare-feu Azure SQL Database.

- **Recommandation** :  Dans Configuration du pare-feu Azure SQL Server, activez l’option « Autoriser les services et les ressources Azure à accéder à ce serveur ». Doc de référence : https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Code d’erreur :  SqlOperationFailed

- **Message** : `A database operation failed. Please search error to get more details.`

- **Cause** : Si le message d’erreur contient « SqlException »,SQL Database génère l’erreur indiquant qu’une opération spécifique a échoué.

- **Recommandation** :  Si l’erreur SQL n’est pas claire, essayez de modifier la base de données au niveau de compatibilité « 150 » le plus récent. Cela peut lever des erreurs SQL de dernière version. Reportez-vous au document détaillé : https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat.
        Pour détecter des problèmes SQL, effectuez une recherche par code d’erreur SQL dans ce document de référence pour plus d’informations : https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Si vous avez besoin d’aide supplémentaire, contactez le support Azure SQL.

- **Cause** : Si le message d’erreur contient « PdwManagedToNativeInteropException », cela est généralement dû à une incompatibilité entre les tailles de colonne source et récepteur.

- **Recommandation** :  Vérifiez la taille des colonnes source et récepteur. Si vous avez besoin d’aide supplémentaire, contactez le support Azure SQL.

- **Cause** : Si le message d’erreur contient « InvalidOperationException », cela est généralement dû à des données d’entrée non valides.

- **Recommandation** :  Pour identifier la ligne qui rencontre le problème, activez la fonctionnalité de tolérance de panne sur l’activité de copie, qui peut rediriger la ou les lignes problématiques vers le stockage pour une investigation plus poussée. Doc de référence : https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Code d’erreur :  SqlUnauthorizedAccess

- **Message** : `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Cause** : Les informations d’identification sont incorrectes ou le compte de connexion ne peut pas accéder à SQL Database.

- **Recommandation** :  Vérifiez que le compte de connexion dispose des autorisations suffisantes pour accéder à SQL Database.


### <a name="error-code--sqlopenconnectiontimeout"></a>Code d’erreur :  SqlOpenConnectionTimeout

- **Message** : `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Cause** : Il peut s’agir d’une défaillance temporaire de SQL Database.

- **Recommandation** :  Réessayez de mettre à jour la chaîne de connexion de service lié avec une plus grande valeur de délai d’attente de connexion.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Code d’erreur :  SqlAutoCreateTableTypeMapFailed

- **Message** : `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Cause** : La création automatique de la table ne peut pas répondre aux exigences de la source.

- **Recommandation** :  Mettez à jour le type de colonne en « mappings », ou créez manuellement la table du récepteur dans le serveur cible.


### <a name="error-code--sqldatatypenotsupported"></a>Code d’erreur :  SqlDataTypeNotSupported

- **Message** : `A database operation failed. Check the SQL errors.`

- **Cause** : Si le problème se produit sur la source SQL et que l’erreur est liée au dépassement de SqlDateTime, la valeur des données est supérieure à la plage du type logique (de 1/1/1753 12:00:00 à 12/31/9999 11:59:59 PM).

- **Recommandation** :  Effectuez un forçage de type en chaîne dans la requête SQL source ou, dans le mappage de colonne d’activité de copie, modifiez le type de colonne en « Chaîne ».

- **Cause** : Si le problème se produit sur un récepteur SQL et que l’erreur est liée au dépassement de SqlDateTime, la valeur des données est supérieure à la plage autorisée dans la table sink.

- **Recommandation** :  Mettez à jour le type de colonne correspondant en type « datetime2 » dans la table du récepteur.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Code d’erreur :  SqlInvalidDbStoredProcedure

- **Message** : `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Cause** : La procédure stockée spécifiée n’est pas valide. Cela peut être dû au fait que la procédure stockée ne retourne pas de données.

- **Recommandation** :  Validez la procédure stockée à l’aide des outils SQL. Assurez-vous que la procédure stockée peut renvoyer des données.


### <a name="error-code--sqlinvaliddbquerystring"></a>Code d’erreur :  SqlInvalidDbQueryString

- **Message** : `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Cause** : La requête SQL spécifiée n’est pas valide. Cela peut être dû au fait que la requête ne renvoie aucune donnée

- **Recommandation** :  Validez la requête SQL à l’aide des outils SQL. Assurez-vous que la requête peut renvoyer des données.


### <a name="error-code--sqlinvalidcolumnname"></a>Code d’erreur :  SqlInvalidColumnName

- **Message** : `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Cause** : Impossible de trouver la colonne. La configuration est peut-être incorrecte.

- **Recommandation** :  Vérifiez la colonne dans la requête, « structure » dans le jeu de données et « mappings » dans l’activité.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Code d’erreur :  SqlColumnNameMismatchByCaseSensitive

- **Message** : `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Code d’erreur :  SqlBatchWriteTimeout

- **Message** : `Timeouts in SQL write operation.`

- **Cause** : Il peut s’agir d’une défaillance temporaire de SQL Database.

- **Recommandation** :  Veuillez réessayer. Si le problème persiste, contactez le support Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Code d’erreur :  SqlBatchWriteTransactionFailed

- **Message** : `SQL transaction commits failed`

- **Cause** : Si les détails de l’exception indiquent constamment une expiration du délai de la transaction, la latence du réseau entre le runtime d’intégration et la base de données est supérieure au seuil par défaut de 30 secondes.

- **Recommandation** :  Mettez à jour la chaîne de connexion du service lié à SQL avec la valeur « connection timeout » égale à 120 ou supérieure, puis réexécutez l’activité.

- **Cause** : Si les détails de l’exception indiquent par intermittence que sqlconnection est rompu, il peut s’agir d’une défaillance temporaire du réseau ou d’un problème du côté de SQL Database.

- **Recommandation** :  Réessayez l’activité et passez en revue les métriques du côté de SQL Database.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Code d’erreur :  SqlBulkCopyInvalidColumnLength

- **Message** : `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Cause** : La copie en bloc SQL a échoué à cause de la réception d’une longueur de colonne non valide du client bcp.

- **Recommandation** :  Pour identifier la ligne qui rencontre le problème, activez la fonctionnalité de tolérance de panne sur l’activité de copie, qui peut rediriger la ou les lignes problématiques vers le stockage pour une investigation plus poussée. Doc de référence : https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Code d’erreur :  SqlConnectionIsClosed

- **Message** : `The connection is closed by SQL Database.`

- **Cause** : La connexion SQL est fermée par SQL Database lorsque le nombre d’exécutions simultanées est élevé et que le serveur termine la connexion.

- **Recommandation** :  Le serveur distant a fermé la connexion SQL. Veuillez réessayer. Si le problème persiste, contactez le support Azure SQL.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Code d’erreur :  SqlCreateTableFailedUnsupportedType

- **Message** : `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Message d’erreur : Échec lors de la conversion d’une chaîne de caractères en valeur de type uniqueidentifier

- **Symptômes** : Lorsque vous copiez des données à partir d’une source de données tabulaire (telle que SQL Server) dans Azure SQL Data Warehouse à l’aide de la copie intermédiaire et de PolyBase, vous rencontrez l’erreur suivante :

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Cause** : Azure SQL Data Warehouse PolyBase ne peut pas convertir une chaîne vide en GUID.

- **Résolution** : Dans le récepteur de l’activité de copie, sous les paramètres de Polybase, affectez la valeur false à « **Utiliser l’option de type par défaut** ».

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Message d’erreur : Type de données attendu : DECIMAL(x,x), valeur incriminée

- **Symptômes** : Lorsque vous copiez des données à partir d’une source de données tabulaire (telle que SQL Server) dans SQL Data Warehouse à l’aide de la copie intermédiaire et de PolyBase, vous rencontrez l’erreur suivante :

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Cause** : Azure SQL Data Warehouse Polybase ne peut pas insérer une chaîne vide (valeur null) dans une colonne décimale.

- **Résolution** : Dans le récepteur de l’activité de copie, sous les paramètres de Polybase, affectez la valeur false à « **Utiliser l’option de type par défaut** ».

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Message d’erreur : Message d’exception Java : HdfsBridge::CreateRecordReader

- **Symptômes** : Vous copiez des données dans Azure SQL Data Warehouse à l’aide de PolyBase et vous rencontrez l’erreur suivante :

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Cause** : La cause possible est que le schéma (largeur totale de colonne) est trop grand (plus de 1 Mo). Vérifiez le schéma de la table SQL Data Warehouse cible en ajoutant la taille de toutes les colonnes :

    - Int -> 4 octets
    - Bigint -> 8 octets
    - Varchar(n), char(n), binary(n), varbinary(n) -> n octets
    - Nvarchar(n), nchar(n) -> n*2 octets
    - Date -> 6 octets
    - Datetime/(2), smalldatetime -> 16 octets
    - Datetimeoffset -> 20 octets
    - Decimal -> 19 octets
    - Float -> 8 octets
    - Money -> 8 octets
    - Smallmoney -> 4 octets
    - Real -> 4 octets
    - Smallint -> 2 octets
    - Time -> 12 octets
    - Tinyint -> 1 octet

- **Résolution** : Réduisez la largeur de colonne à moins de 1 Mo

- Ou utilisez l’approche d’insertion en bloc en désactivant Polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Message d’erreur : La condition spécifiée avec un ou plusieurs en-têtes conditionnels HTTP n’est pas remplie

- **Symptômes** : Vous utilisez la requête SQL pour extraire des données d’Azure SQL Data Warehouse et vous pouvez rencontrez l’erreur suivante :

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Cause** : Azure SQL Data Warehouse a rencontré un problème en interrogeant la table externe dans le stockage Azure.

- **Résolution** : Exécutez la même requête dans SSMS et vérifiez si vous voyez le même résultat. Si c’est le cas, ouvrez un ticket de support pour Azure SQL Data Warehouse et indiquez votre nom de base de données et de serveur SQL Data Warehouse à des fins de dépannage.
            

## <a name="delimited-text-format"></a>Format de texte délimité

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Code d’erreur :  DelimitedTextColumnNameNotAllowNull

- **Message** : `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Cause** : Quand vous définissez « firstRowAsHeader » dans l’activité, la première ligne est utilisée comme nom de colonne. Cette erreur signifie que la première ligne contient une valeur vide. Par exemple : « ColumnA,,ColumnB ».

- **Recommandation** :  Vérifiez la première ligne et corrigez la valeur s’il s’agit d’une valeur vide.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Code d’erreur :  DelimitedTextMoreColumnsThanDefined

- **Message** : `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Cause** : Le nombre de colonnes de la ligne problématique est plus important que le nombre de colonnes de la première ligne. Cela peut être dû à un problème de données ou à un délimiteur de colonne ou des paramètres de caractère de citation incorrects.

- **Recommandation** :  Récupérez le nombre de lignes dans le message d’erreur, vérifiez la colonne de la ligne et corrigez les données.

- **Cause** : Si le nombre de colonnes attendu est « 1 » dans le message d’erreur, vous avez peut-être spécifié des paramètres de compression ou de mise en forme incorrects, ce qui a provoqué l’analyse incorrecte de vos fichiers par ADF.

- **Recommandation** :  Vérifiez les paramètres de mise en forme pour vous assurer qu’ils correspondent à vos fichiers sources.

- **Cause** : Si votre source est un dossier, il est possible que les fichiers du dossier spécifié aient un schéma différent.

- **Recommandation** :  Assurez-vous que les fichiers du dossier donné ont un schéma identique.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Code d’erreur :  DelimitedTextIncorrectRowDelimiter

- **Message** : `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Code d’erreur :  DelimitedTextTooLargeColumnCount

- **Message** : `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Code d’erreur :  DelimitedTextInvalidSettings

- **Message** : `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Code d’erreur :  DynamicsCreateServiceClientError

- **Message** : `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Cause** : Il s’agit d’un problème temporaire du côté du serveur Dynamics.

- **Recommandation** :  Exécutez à nouveau le pipeline. Si l’échec persiste, essayez de réduire le parallélisme. Si le problème n’est toujours pas résolu, contactez le support Dynamics.



## <a name="json-format"></a>Format JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Code d’erreur :  JsonInvalidArrayPathDefinition

- **Message** : `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Code d’erreur :  JsonEmptyJObjectData

- **Message** : `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Code d’erreur :  JsonNullValueInPathDefinition

- **Message** : `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Code d’erreur :  JsonUnsupportedHierarchicalComplexValue

- **Message** : `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Code d’erreur :  JsonConflictPartitionDiscoverySchema

- **Message** : `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Code d’erreur :  JsonInvalidDataFormat

- **Message** : `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Code d’erreur :  JsonInvalidDataMixedArrayAndObject

- **Message** : `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Format Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Code d’erreur :  ParquetJavaInvocationException

- **Message** : `An error occurred when invoking java, message: %javaException;.`

- **Cause** : Lorsque le message d’erreur contient « java.lang.OutOfMemory », « Java heap space » et « doubleCapacity », il s’agit généralement d’un problème de gestion de la mémoire dans l’ancienne version du runtime d’intégration.

- **Recommandation** :  Si vous utilisez des runtimes d’intégration auto-hébergés et que la version est antérieure à 3.20.7159.1, suggérez une mise à niveau vers la version la plus récente.

- **Cause** : Lorsque le message d’erreur contient « java.lang.OutOfMemory », le runtime d’intégration ne dispose pas de suffisamment de ressources pour traiter le ou les fichiers.

- **Recommandation** :  Limitez les exécutions simultanées sur le runtime d’intégration. Pour les runtimes d’intégration auto-hébergés, montez en puissance vers un ordinateur puissant doté d’une mémoire égale ou supérieure à 8 Go.

- **Cause** : Quand le message d’erreur contient « NullPointerReference », il peut s’agir d’une erreur temporaire.

- **Recommandation** :  Veuillez réessayer. Si le problème persiste, contactez le support.


### <a name="error-code--parquetinvalidfile"></a>Code d’erreur :  ParquetInvalidFile

- **Message** : `File is not a valid parquet file.`

- **Cause** : Problème de fichier Parquet.

- **Recommandation** :  Vérifiez que l’entrée est un fichier Parquet valide.


### <a name="error-code--parquetnotsupportedtype"></a>Code d’erreur :  ParquetNotSupportedType

- **Message** : `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Cause** : Le format Parquet n’est pas pris en charge dans Azure Data Factory.

- **Recommandation** :  Revérifiez les données sources. Reportez-vous au document : https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Code d’erreur :  ParquetMissedDecimalPrecisionScale

- **Message** : `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Cause** : Essaie d’analyser la précision et l’échelle des nombres, mais aucune information de ce type n’est fournie.

- **Recommandation** :  « Source » ne renvoie pas une précision et une échelle correctes. Vérifiez la précision et l’échelle de la colonne posant problème.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Code d’erreur :  ParquetInvalidDecimalPrecisionScale

- **Message** : `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Cause** : Le schéma n’est pas valide.

- **Recommandation** :  Vérifiez la précision et l’échelle de la colonne posant problème.


### <a name="error-code--parquetcolumnnotfound"></a>Code d’erreur :  ParquetColumnNotFound

- **Message** : `Column %column; does not exist in Parquet file.`

- **Cause** : Le schéma source est incompatible avec le schéma du récepteur.

- **Recommandation** :  Vérifiez la colonne « mappings » dans « activity ». Assurez-vous que la colonne source peut être mappée à la colonne du récepteur appropriée.


### <a name="error-code--parquetinvaliddataformat"></a>Code d’erreur :  ParquetInvalidDataFormat

- **Message** : `Incorrect format of %srcValue; for converting to %dstType;.`

- **Cause** : Les données ne peuvent pas être converties dans le type spécifié dans mappings.source.

- **Recommandation** :  Revérifiez les données sources ou spécifiez le type de données approprié pour cette colonne dans le mappage des colonnes de l’activité de copie. Reportez-vous au document : https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Code d’erreur :  ParquetDataCountNotMatchColumnCount

- **Message** : `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Cause** : Non-concordance du nombre de colonnes sources et du nombre de colonnes du récepteur

- **Recommandation** :  Revérifiez que le nombre de colonnes sources est identique au nombre de colonnes du récepteur dans « mapping ».


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Code d’erreur :  ParquetDataTypeNotMatchColumnType

- **Message** : Le type de données%srcType; ne correspond pas au type de colonne %dstType; donné dans la colonne « %columnIndex; ».

- **Cause** : Les données de la source ne peuvent pas être converties dans le type défini du récepteur

- **Recommandation** :  Spécifiez un type correct dans mappage.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Code d’erreur :  ParquetBridgeInvalidData

- **Message** : `%message;`

- **Cause** : Valeur de données au-delà de la limitation

- **Recommandation** :  Veuillez réessayer. Si le problème persiste, contactez-nous.


### <a name="error-code--parquetunsupportedinterpretation"></a>Code d’erreur :  ParquetUnsupportedInterpretation

- **Message** : `The given interpretation '%interpretation;' of parquet format is not supported.`

- **Cause** : Scénario non pris en charge

- **Recommandation** :  « ParquetInterpretFor » ne doit pas être « sparkSql ».


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Code d’erreur :  ParquetUnsupportFileLevelCompressionOption

- **Message** : `File level compression is not supported for Parquet.`

- **Cause** : Scénario non pris en charge

- **Recommandation** :  Supprimez « CompressionType » dans la charge utile.



## <a name="general-copy-activity-error"></a>Erreur générale de l’activité de copie

### <a name="error-code--jrenotfound"></a>Code d’erreur :  JreNotFound

- **Message** : `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Cause** : Le runtime d’intégration auto-hébergé ne parvient pas à trouver le runtime Java. Le runtime Java est requis pour la lecture d’une source particulière.

- **Recommandation** :  Vérifiez votre environnement de runtime d’intégration et reportez-vous au document de référence : https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Code d’erreur :  WildcardPathSinkNotSupported

- **Message** : `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Cause** : Le jeu de données du récepteur ne prend pas en charge les caractères génériques.

- **Recommandation** :  Vérifiez le jeu de données du récepteur et corrigez le chemin d’accès sans la valeur de caractère générique.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Code d’erreur :  MappingInvalidPropertyWithEmptyValue

- **Message** : `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Code d’erreur :  MappingInvalidPropertyWithNamePathAndOrdinal

- **Message** : `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Code d’erreur :  MappingDuplicatedOrdinal

- **Message** : `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Code d’erreur :  MappingInvalidOrdinalForSinkColumn

- **Message** : `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page de questions Microsoft Q&R](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
            
