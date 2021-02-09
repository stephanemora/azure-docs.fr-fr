---
title: Résoudre les problèmes liés aux connecteurs dans Azure Data Factory
description: Découvrez comment résoudre les problèmes liés aux connecteurs dans Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: fdc4bbd463c45fecfc9e3961e42f81ed93d820ae
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054634"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Résoudre les problèmes liés aux connecteurs dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explore les façons courantes de résoudre les problèmes liés aux connecteurs dans Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Stockage Blob Azure

### <a name="error-code-azurebloboperationfailed"></a>Code d’erreur : AzureBlobOperationFailed

- **Message** : « L’opération blob a échoué. ContainerName: %containerName;, path: %path;. »

- **Cause** : Problème lié au fonctionnement du stockage Blob.

- **Recommandation** :  Pour vérifier les détails de l’erreur, consultez [Codes d’erreur du stockage Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes). Pour obtenir de l’aide, contactez l’équipe Stockage Blob.


### <a name="invalid-property-during-copy-activity"></a>Propriété non valide durant l’activité de copie

- **Message** : `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Cause** : Le type défini dans le jeu de données n’est pas cohérent avec le type de source/récepteur défini dans l’activité de copie.

- **Résolution** : Modifiez la définition JSON du jeu de données ou du pipeline pour rendre les types cohérents, puis réexécutez le déploiement.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Message d’erreur : La taille de la demande est trop grande

- **Symptômes** : Lorsque vous copiez des données dans Azure Cosmos DB avec une taille du lot d’écriture par défaut, vous recevez l’erreur suivante : `Request size is too large.`

- **Cause** : Azure Cosmos DB limite la taille d’une seule requête à 2 Mo. La formule est la suivante : *taille de la requête = taille de document unique \* taille du lot d’écriture*. Si la taille de votre document est importante, le comportement par défaut entraîne une trop grande taille de la requête. Vous pouvez ajuster la taille du lot d’écriture.

- **Résolution** : Dans le récepteur de l’activité de copie, réduisez la valeur de la *taille du lot d’écriture* (la valeur par défaut est 10 000).

### <a name="error-message-unique-index-constraint-violation"></a>Message d’erreur : Violation de contrainte d’index unique

- **Symptômes** : Lorsque vous copiez des données dans Azure Cosmos DB, vous recevez l’erreur suivante :

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Cause** : Il existe deux causes possibles :

    - Cause 1 : Si vous utilisez **Insérer** comme comportement d’écriture, cette erreur signifie que vos données sources ont des lignes ou des objets avec le même ID.
    - Cause 2 : Si vous utilisez **Upsert** comme comportement d’écriture et que vous définissez une autre clé unique sur le conteneur, cette erreur signifie que vos données sources ont des lignes ou des objets avec des ID différents, mais une même valeur pour la clé unique définie.

- **Résolution** : 

    - Pour la cause 1, définissez **Upsert** comme comportement d’écriture.
    - Pour la cause 2, assurez-vous que chaque document a une valeur différente pour la clé unique définie.

### <a name="error-message-request-rate-is-large"></a>Message d’erreur : Le taux de demandes est élevé

- **Symptômes** : Lorsque vous copiez des données dans Azure Cosmos DB, vous recevez l’erreur suivante :

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Cause** : Le nombre d’unités de requête (RU) utilisées est supérieur au nombre de RU disponibles configuré dans Azure Cosmos DB. Pour savoir comment Azure Cosmos DB calcule les unités de requête, consultez [Unités de requête dans Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Résolution** : Essayez l’une des deux solutions suivantes :

    - Définissez le nombre de *RU dans le conteneur* sur une valeur supérieure dans Azure Cosmos DB. Cette solution permet d’améliorer les performances de l’activité de copie, mais elle engendre des frais supplémentaires dans Azure Cosmos DB. 
    - Diminuez la valeur *writeBatchSize*, par exemple sur 1 000, et diminuez *parallelCopies*, par exemple sur 1. Cette solution réduit les performances d’exécution de la copie, mais elle n’engendre pas de frais supplémentaires dans Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Colonnes manquantes dans le mappage de colonnes

- **Symptômes** : Lorsque vous importez un schéma pour Azure Cosmos DB pour le mappage de colonnes, certaines colonnes sont manquantes. 

- **Cause** : Data Factory déduit le schéma à partir des 10 premiers documents Azure Cosmos DB. Si certaines colonnes ou propriétés du document ne contiennent pas de valeurs, le schéma n’est pas détecté par Data Factory et, par conséquent, n’est pas affiché.

- **Résolution** : Vous pouvez paramétrer la requête comme indiqué dans le code suivant pour forcer l’affichage des valeurs de colonnes dans le jeu de résultats avec des valeurs vides. Supposons que la colonne *impossible* est absente dans les 10 premiers documents. Vous pouvez également ajouter manuellement la colonne pour le mappage.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Message d’erreur : Le GuidRepresentation pour le lecteur est CSharpLegacy

- **Symptômes** : Lorsque vous copiez des données à partir de MongoAPI ou MongoDB Azure Cosmos DB avec le champ d’identificateur unique universel (UUID), vous recevez l’erreur suivante :

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Cause** : Il existe deux façons de représenter l’UUID en JSON binaire (BSON) : UuidStandard et UuidLegacy. Par défaut, UuidLegacy est utilisé pour lire les données. Vous recevez une erreur si vos données UUID dans MongoDB sont de type UuidStandard.

- **Résolution** : Dans la chaîne de connexion MongoDB, ajoutez l’option *uuidRepresentation=standard*. Pour plus d’informations, consultez [Chaîne de connexion MongoDB](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (API SQL)

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>Code d’erreur :  CosmosDbSqlApiOperationFailed

- **Message** : `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Cause** : Problème lié à l’opération CosmosDbSqlApi.

- **Recommandation** :  Pour voir les détails de l’erreur, consultez [Document d’aide Azure Cosmos DB](../cosmos-db/troubleshoot-dot-net-sdk.md). Pour une aide supplémentaire, contactez l’équipe Azure Cosmos DB.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Message d’erreur : Le serveur a clos la connexion sous-jacente : Impossible d’établir une relation de confiance pour le canal sécurisé SSL/TLS.

- **Symptômes** : L’activité de copie échoue avec l’erreur suivante : 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Cause** : La validation du certificat a échoué lors de l’établissement d’une liaison TLS.

- **Résolution** : Pour contourner ce problème, utilisez la copie intermédiaire pour ignorer la validation TLS (Transport Layer Security) pour Azure Data Lake Storage Gen1. Vous devez reproduire ce problème et collecter la trace du moniteur réseau (netmon), puis demander à votre équipe réseau de vérifier la configuration du réseau local.

    ![Diagramme des connexions Azure Data Lake Storage Gen1 pour la résolution des problèmes.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Message d’erreur : Le serveur distant a retourné une erreur : (403) Interdit

- **Symptômes** : L’activité de copie échoue avec l’erreur suivante : 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Cause** : L’une des causes possibles est que le principal du service ou l’identité managée que vous utilisez n’a pas l’autorisation d’accéder à certains dossiers ou fichiers.

- **Résolution** : Accordez les autorisations appropriées sur tous les dossiers et sous-dossiers que vous devez copier. Pour en savoir plus, consultez [Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l’aide d’Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Message d’erreur : Échec d’obtention du jeton d’accès à l’aide du principal du service. Erreur ADAL : service_unavailable

- **Symptômes** : L’activité de copie échoue avec l’erreur suivante :

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Cause** : Lorsque le serveur de jeton de service (STS) détenu par Azure Active Directory n’est pas disponible, c.-à-d. qu’il est trop occupé pour gérer les requêtes, il renvoie une erreur HTTP 503. 

- **Résolution** : Réexécutez l’activité de copie au bout de quelques minutes.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Code d’erreur : AdlsGen2OperationFailed

- **Message** : `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Cause** : Si Azure Data Lake Storage Gen2 lève cette erreur, l’opération a échoué.

- **Recommandation** :  Consultez le message d’erreur détaillé généré par Azure Data Lake Storage Gen2. Si l’erreur est un échec temporaire, recommencez l’opération. Si vous avez besoin d’aide supplémentaire, contactez le support Stockage Azure et fournissez l’ID de la requête indiqué dans le message d’erreur.

- **Cause** : Si le message d’erreur contient la chaîne « Forbidden », le principal du service ou l’identité managée que vous utilisez ne dispose peut-être pas des autorisations suffisantes pour accéder à Azure Data Lake Storage Gen2.

- **Recommandation** :  Pour résoudre cette erreur, consultez [Copier et transformer des données dans Azure Data Lake Storage Gen2 avec Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication).

- **Cause** : Si le message d’erreur contient la chaîne « InternalServerError », l’erreur est retournée par Azure Data Lake Storage Gen2.

- **Recommandation** :  L’erreur peut être due à un échec temporaire. Si c’est le cas, recommencez l’opération. Si le problème persiste, contactez le support Stockage Azure et fournissez l’ID de la requête indiqué dans le message d’erreur.

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>La requête au compte Azure Data Lake Storage Gen2 a provoqué une erreur de dépassement de délai

- **Message** : 
  * Code d’erreur = `UserErrorFailedBlobFSOperation`
  * Message d’erreur = `BlobFS operation failed for: A task was canceled.`

- **Cause** : Le problème est dû à une erreur de dépassement de délai du récepteur Azure Data Lake Storage Gen2, qui se produit généralement sur l’ordinateur du runtime d'intégration auto-hébergé.

- **Recommandation** : 

    - Placez votre machine du runtime d’intégration auto-hébergé et le compte Azure Data Lake Storage Gen2 cible dans la même région, si possible. Cela peut éviter une erreur de dépassement de délai aléatoire et améliorer les performances.

    - Vérifiez s’il existe un paramètre de réseau spécial, comme ExpressRoute, et assurez-vous que le réseau dispose d’une bande passante suffisante. Nous vous suggérons de diminuer le paramètre des tâches simultanées du runtime d’intégration auto-hébergé lorsque la bande passante globale est faible. Cela permet d’éviter que plusieurs tâches simultanées ne se disputent les ressources réseau.

    - Utilisez une taille de bloc plus petite pour la copie non binaire afin d’atténuer les effets d’une telle erreur de dépassement de délai si la taille de fichier est modérée ou petite. Pour plus d’informations, consultez [Bloc Put de stockage Blob](/rest/api/storageservices/put-block).

       Pour spécifier une taille de bloc personnalisée, modifiez la propriété dans l’éditeur de fichier JSON comme indiqué ici :
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Stockage Azure Files

### <a name="error-code--azurefileoperationfailed"></a>Code d’erreur :  AzureFileOperationFailed

- **Message** : `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Cause** : Problème lié au fonctionnement du stockage Azure Files.

- **Recommandation** :  Pour voir les détails de l’erreur, consultez [Aide Azure Files](https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes). Pour une aide supplémentaire, contactez l’équipe Azure Files.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database et SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Code d’erreur :  SqlFailedToConnect

- **Message** : `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Cause** : Pour Azure SQL, si le message d’erreur contient la chaîne « SqlErrorNumber=47073 », cela signifie que l’accès au réseau public est refusé dans le paramètre de connectivité.

- **Recommandation** : Dans le pare-feu Azure SQL, définissez l’option **Refuser l’accès au réseau public** sur *Non*. Pour plus d’informations, consultez les [paramètres de connectivité Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access).

- **Cause** : Pour Azure SQL, si le message d’erreur contient un code d’erreur SQL, par exemple « SqlErrorNumber=[errorcode] », consultez le guide de résolution des problèmes Azure SQL.

- **Recommandation** : Pour obtenir une recommandation, consultez [Résolution des problèmes de connectivité et autres erreurs avec Azure SQL Database et Azure SQL Managed Instance](https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues).

- **Cause** : Vérifiez si le port 1433 figure sur la liste verte du pare-feu.

- **Recommandation** : Pour plus d’informations, consultez [Ports utilisés par SQL Server](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-).

- **Cause** : Si le message d’erreur contient la chaîne « SqlException »,SQL Database génère l’erreur indiquant qu’une opération spécifique a échoué.

- **Recommandation** :  Pour plus d’informations, effectuez une recherche par code d’erreur SQL dans [Erreurs du moteur de base de données](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors). Si vous avez besoin d’aide supplémentaire, contactez le support Azure SQL.

- **Cause** : S’il s’agit d’un problème temporaire (par exemple, une connexion réseau instable), ajoutez une nouvelle tentative dans la stratégie d’activité à atténuer.

- **Recommandation** :  Pour plus d’informations, consultez [Pipelines et activités dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy).

- **Cause** : Si le message d’erreur contient la chaîne « Le client avec l’adresse IP "…" n’est pas autorisé à accéder au serveur » et que vous essayez de vous connecter à Azure SQL Database, ce problème est généralement causé par un problème de pare-feu Azure SQL Database.

- **Recommandation** :  Dans Configuration du pare-feu Azure SQL Server, activez l’option **Autoriser les services et les ressources Azure à accéder à ce serveur**. Pour plus d’informations, consultez [Règles de pare-feu IP Azure SQL Database et Azure Synapse](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).


### <a name="error-code--sqloperationfailed"></a>Code d’erreur :  SqlOperationFailed

- **Message** : `A database operation failed. Please search error to get more details.`

- **Cause** : Si le message d’erreur contient la chaîne « SqlException », SQL Database génère l’erreur indiquant qu’une opération spécifique a échoué.

- **Recommandation** :  Si l’erreur SQL n’est pas claire, essayez de régler la base de données sur le niveau de compatibilité « 150 » le plus récent. Cela peut lever des erreurs SQL de dernière version. Pour plus d’informations, consultez la [documentation](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Pour plus d’informations sur la résolution des problèmes SQL, effectuez une recherche par code d’erreur SQL dans [Erreurs du moteur de base de données](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors). Si vous avez besoin d’aide supplémentaire, contactez le support Azure SQL.

- **Cause** : Si le message d’erreur contient la chaîne « PdwManagedToNativeInteropException », cela est généralement dû à une incompatibilité entre les tailles de colonne source et récepteur.

- **Recommandation** :  Vérifiez la taille des colonnes source et récepteur. Si vous avez besoin d’aide supplémentaire, contactez le support Azure SQL.

- **Cause** : Si le message d’erreur contient la chaîne « InvalidOperationException », cela est généralement dû à des données d’entrée non valides.

- **Recommandation** :  Pour identifier la ligne qui a rencontré le problème, activez la fonctionnalité de tolérance de panne sur l’activité de copie, qui peut rediriger les lignes problématiques vers le stockage pour une investigation plus poussée. Pour plus d’informations, consultez [Tolérance de panne de l’activité de copie dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance).


### <a name="error-code--sqlunauthorizedaccess"></a>Code d’erreur :  SqlUnauthorizedAccess

- **Message** : `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Cause** : Les informations d’identification sont incorrectes ou le compte de connexion ne peut pas accéder à la base de données SQL.

- **Recommandation** :  Vérifiez que le compte de connexion dispose des autorisations suffisantes pour accéder à la base de données SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Code d’erreur :  SqlOpenConnectionTimeout

- **Message** : `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Cause** : Il peut s’agir d’un échec temporaire de la base de données SQL.

- **Recommandation** :  Réessayez de mettre à jour la chaîne de connexion de service lié avec une valeur de délai d’attente de connexion plus importante.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Code d’erreur :  SqlAutoCreateTableTypeMapFailed

- **Message** : `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Cause** : La table de création automatique ne peut pas répondre aux exigences de la source.

- **Recommandation** :  Mettez à jour le type de colonne dans *mappages* ou créez manuellement la table du récepteur dans le serveur cible.


### <a name="error-code--sqldatatypenotsupported"></a>Code d’erreur :  SqlDataTypeNotSupported

- **Message** : `A database operation failed. Check the SQL errors.`

- **Cause** : Si le problème se produit sur la source SQL et que l’erreur est liée au dépassement de SqlDateTime, la valeur des données est supérieure à la plage du type logique (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Recommandation** :  Castez le type en chaîne dans la requête SQL source ou, dans le mappage de colonne d’activité de copie, modifiez le type de colonne en *Chaîne*.

- **Cause** : Si le problème se produit sur un récepteur SQL et que l’erreur est liée au dépassement de SqlDateTime, la valeur des données est supérieure à la plage autorisée dans la table du récepteur.

- **Recommandation** :  Mettez à jour le type de colonne correspondant en type *datetime2* dans la table du récepteur.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Code d’erreur :  SqlInvalidDbStoredProcedure

- **Message** : `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Cause** : La procédure stockée spécifiée n’est pas valide. Cela peut être dû au fait que la procédure stockée ne retourne pas de données.

- **Recommandation** :  Validez la procédure stockée à l’aide des outils SQL. Assurez-vous que la procédure stockée peut retourner des données.


### <a name="error-code--sqlinvaliddbquerystring"></a>Code d’erreur :  SqlInvalidDbQueryString

- **Message** : `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Cause** : La requête SQL spécifiée n’est pas valide. Cela peut être dû au fait que la requête ne retourne aucune donnée.

- **Recommandation** :  Validez la requête SQL à l’aide des outils SQL. Assurez-vous que la requête peut retourner des données.


### <a name="error-code--sqlinvalidcolumnname"></a>Code d’erreur :  SqlInvalidColumnName

- **Message** : `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Cause** : La colonne est introuvable, car la configuration est peut-être incorrecte.

- **Recommandation** :  Vérifiez la colonne dans la requête, *structure* dans le jeu de données et *mappages* dans l’activité.


### <a name="error-code--sqlbatchwritetimeout"></a>Code d’erreur :  SqlBatchWriteTimeout

- **Message** : `Timeouts in SQL write operation.`

- **Cause** : Le problème peut être dû à un échec temporaire de la base de données SQL.

- **Recommandation** :  Retentez l’opération. Si le problème persiste, contactez le support Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Code d’erreur :  SqlBatchWriteTransactionFailed

- **Message** : `SQL transaction commits failed.`

- **Cause** : Si les détails de l’exception indiquent constamment une expiration du délai de la transaction, la latence du réseau entre le runtime d’intégration et la base de données est supérieure au seuil par défaut, qui est de 30 secondes.

- **Recommandation** :  Mettez à jour la chaîne de connexion du service lié à SQL avec une valeur *délai de connexion* égale ou supérieure à 120, puis réexécutez l’activité.

- **Cause** : Si les détails de l’exception indiquent par intermittence que la connexion SQL est interrompue, il peut s’agir d’un échec temporaire du réseau ou d’un problème du côté de la base de données SQL.

- **Recommandation** :  Réessayez l’activité et passez en revue les métriques du côté de la base de données SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Code d’erreur :  SqlBulkCopyInvalidColumnLength

- **Message** : `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Cause** : La copie en bloc SQL a échoué à cause d’une longueur de colonne non valide reçue du client de l’utilitaire de copie en bloc (bcp).

- **Recommandation** :  Pour identifier la ligne qui a rencontré le problème, activez la fonctionnalité de tolérance de panne sur l’activité de copie. Cela peut rediriger les lignes problématiques vers le stockage pour une investigation plus poussée. Pour plus d’informations, consultez [Tolérance de panne de l’activité de copie dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance).


### <a name="error-code--sqlconnectionisclosed"></a>Code d’erreur :  SqlConnectionIsClosed

- **Message** : `The connection is closed by SQL Database.`

- **Cause** : La connexion SQL est fermée par la base de données SQL lorsque le nombre d’exécutions simultanées est élevé et que le serveur met fin à la connexion.

- **Recommandation** :  Relancez la connexion. Si le problème persiste, contactez le support Azure SQL.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Message d’erreur : Échec lors de la conversion d’une chaîne de caractères en valeur de type uniqueidentifier

- **Symptômes** : Lorsque vous copiez des données d’une source de données tabulaire (telle que SQL Server) vers Azure Synapse Analytics à l’aide de la copie intermédiaire et de PolyBase, vous recevez l’erreur suivante :

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Cause** : Azure Synapse Analytics PolyBase ne peut pas convertir une chaîne vide en GUID.

- **Résolution** : Dans le récepteur de l’activité de copie, sous les paramètres de PolyBase, définissez l’option **utiliser le type par défaut** sur *false*.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Message d’erreur : Type de données attendu : DECIMAL(x,x), valeur incriminée

- **Symptômes** : Lorsque vous copiez des données d’une source de données tabulaire (telle que SQL Server) vers Azure Synapse Analytics à l’aide de la copie intermédiaire et de PolyBase, vous recevez l’erreur suivante :

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Cause** : Azure Synapse Analytics PolyBase ne peut pas insérer une chaîne vide (valeur Null) dans une colonne décimale.

- **Résolution** : Dans le récepteur de l’activité de copie, sous les paramètres de PolyBase, définissez l’option **utiliser le type par défaut** sur false.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Message d’erreur : Message d’exception Java : HdfsBridge::CreateRecordReader

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


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Message d’erreur : La condition spécifiée avec un ou plusieurs en-têtes conditionnels HTTP n’est pas remplie

- **Symptômes** : Vous utilisez la requête SQL pour extraire des données d’Azure Synapse Analytics et vous recevez l’erreur suivante :

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Cause** : Azure Synapse Analytics a rencontré un problème en interrogeant la table externe dans Stockage Azure.

- **Résolution** : Exécutez la même requête dans SQL Server Management Studio (SSMS) et vérifiez si vous obtenez le même résultat. Si c’est le cas, ouvrez un ticket de support pour Azure Synapse Analytics et indiquez le nom de votre base de données et de votre serveur Azure Synapse Analytics.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Le niveau de performance est faible et entraîne un échec de la copie

- **Symptômes** : Vous copiez des données dans Azure SQL Database et vous recevez l’erreur suivante : `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Cause** : Azure SQL Database S1 a atteint les limites d’entrée/sortie (E/S).

- **Résolution** : Mettez à niveau le niveau de performance d’Azure SQL Database pour corriger le problème. 


### <a name="sql-table-cant-be-found"></a>Table SQL introuvable 

- **Symptômes** : Vous copiez des données d’une table hybride vers une table SQL Server locale et vous recevez l’erreur suivante : `Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Cause** : Le compte SQL actuel ne dispose pas d’autorisations suffisantes pour exécuter les requêtes émises par .NET SqlBulkCopy.WriteToServer.

- **Résolution** : Passez à un compte SQL dotée de privilèges supérieurs.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Message d’erreur : Les données de type chaîne ou binaire sont tronquées

- **Symptômes** : Une erreur se produit lorsque vous copiez des données dans une table Azure SQL Server locale. 

- **Cause** : La définition du schéma de table SQL Cx présente une ou plusieurs colonnes dont la longueur est inférieure à celle attendue.

- **Résolution** : Pour résoudre ce problème, essayez d’effectuer les étapes suivantes :

    1. Pour corriger les lignes problématiques, appliquez la [tolérance de panne](./copy-activity-fault-tolerance.md) du récepteur SQL, en particulier « redirectIncompatibleRowSettings ».

        > [!NOTE]
        > La tolérance de panne peut nécessiter une durée d’exécution supplémentaire, pouvant entraîner des coûts plus élevés.

    2. Examinez les données redirigées par rapport à la longueur de colonne du schéma de table SQL pour identifier les colonnes qui doivent être mises à jour.

    3. Mettez à jour le schéma de table en conséquence.


## <a name="azure-table-storage"></a>Stockage de table Azure

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>Code d’erreur :  AzureTableDuplicateColumnsFromSource

- **Message** : `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Cause** : Des colonnes sources dupliquées peuvent se produire pour l’une des raisons suivantes :
   * Vous utilisez la base de données en tant que source et vous avez appliqué des jointures de table.
   * Vous avez des fichiers CSV non structurés avec des noms de colonnes dupliqués dans la ligne d’en-tête.

- **Recommandation** :  Vérifiez et corrigez les colonnes sources, si nécessaire.


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>Code d’erreur :  DB2DriverRunFailed

- **Message** : `Error thrown from driver. Sql code: '%code;'`

- **Cause** : Si le message d’erreur contient la chaîne « SQLSTATE=51002 SQLCODE=-805 », suivez le « conseil » sous [Copier des données à partir de DB2 à l’aide d’Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties).

- **Recommandation** :  Essayez de définir « NULLID » dans la propriété `packageCollection`.


## <a name="delimited-text-format"></a>Format de texte délimité

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Code d’erreur :  DelimitedTextColumnNameNotAllowNull

- **Message** : `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Cause** : Lorsque 'firstRowAsHeader' est défini dans l’activité, la première ligne est utilisée comme nom de colonne. Cette erreur signifie que la première ligne contient une valeur vide (par exemple, « ColonneA, ColonneB »).

- **Recommandation** :  Vérifiez la première ligne et corrigez la valeur si elle est vide.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Code d’erreur :  DelimitedTextMoreColumnsThanDefined

- **Message** : `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Cause** : Le nombre de colonnes de la ligne problématique est plus important que le nombre de colonnes de la première ligne. Cela peut être dû à un problème de données ou à des paramètres de délimiteur de colonne ou de guillemet incorrects.

- **Recommandation** :  Récupérez le nombre de lignes dans le message d’erreur, vérifiez la colonne de la ligne et corrigez les données.

- **Cause** : Si le nombre de colonnes attendu est « 1 » dans le message d’erreur, vous avez peut-être spécifié des paramètres de compression ou de mise en forme incorrects, ce qui a provoqué l’analyse incorrecte de vos fichiers par Data Factory.

- **Recommandation** :  Vérifiez les paramètres de mise en forme pour vous assurer qu’ils correspondent à vos fichiers sources.

- **Cause** : Si votre source est un dossier, il est possible que les fichiers du dossier spécifié aient un schéma différent.

- **Recommandation** :  Assurez-vous que les fichiers dans le dossier spécifié ont un schéma identique.


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service et Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Code d’erreur :  DynamicsCreateServiceClientError

- **Message** : `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Cause** : Il s’agit d’un problème temporaire du côté du serveur Dynamics.

- **Recommandation** :  Exécutez à nouveau le pipeline. Si l’échec persiste, essayez de réduire le parallélisme. Si le problème persiste, contactez le support Dynamics.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Colonnes manquantes lorsque vous importez un schéma ou prévisualisez des données

- **Symptômes** : Certaines colonnes sont manquantes lorsque vous importez un schéma ou prévisualisez des données. Message d’erreur : `The valid structure information (column name and type) are required for Dynamics source.`

- **Cause** : Ce problème est lié à la conception, car Data Factory ne peut pas afficher les colonnes qui ne contiennent pas de valeurs dans les 10 premiers enregistrements. Assurez-vous que les colonnes que vous avez ajoutées sont au format correct. 

- **Recommandation** : Ajoutez manuellement les colonnes sous l’onglet de mappage.


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>Code d’erreur :  DynamicsMissingTargetForMultiTargetLookupField

- **Message** : `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Cause** : La colonne cible n’existe pas dans la source ou dans le mappage de colonnes.

- **Recommandation** :  
  1. Assurez-vous que la source contient la colonne cible. 
  2. Ajoutez la colonne cible dans le mappage de colonnes. Assurez-vous que la colonne du récepteur est au format *{fieldName}@EntityReference* .


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>Code d’erreur :  DynamicsInvalidTargetForMultiTargetLookupField

- **Message** : `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Cause** : Un nom d’entité incorrect est fourni en tant qu’entité cible d’un champ de recherche à plusieurs cibles.

- **Recommandation** :  Fournissez un nom d’entité valide pour le champ de recherche à plusieurs cibles.


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>Code d’erreur :  DynamicsInvalidTypeForMultiTargetLookupField

- **Message** : `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Cause** : La valeur dans la colonne cible n’est pas une chaîne.

- **Recommandation** :  Fournissez une chaîne valide dans la colonne cible de recherche à plusieurs cibles.


### <a name="error-code--dynamicsfailedtorequetserver"></a>Code d’erreur :  DynamicsFailedToRequetServer

- **Message** : `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Cause** : Le serveur Dynamics est instable ou inaccessible ou le réseau rencontre des problèmes.

- **Recommandation** :  Pour plus d’informations, vérifiez la connectivité réseau ou consultez le journal du serveur Dynamics. Pour obtenir de l’aide supplémentaire, contactez le support Dynamics.
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>Code d’erreur :  FtpFailedToConnectToFtpServer

- **Message** : `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Cause** : Un type de service lié incorrect est peut-être utilisé pour le serveur FTP, comme l’utilisation du service lié SFTP pour la connexion à un serveur FTP.

- **Recommandation** :  Vérifiez le port du serveur cible. FTP utilise le port 21.


## <a name="http"></a>HTTP

### <a name="error-code--httpfilefailedtoread"></a>Code d’erreur :  HttpFileFailedToRead

- **Message** : `Failed to read data from http server. Check the error from http server：%message;`

- **Cause** : Cette erreur se produit quand Azure Data Factory communique avec le serveur HTTP, mais que l’opération de requête HTTP échoue.

- **Recommandation** :  Vérifiez le code d’état HTTP dans le message d’erreur, puis corrigez le problème du serveur distant.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Code d’erreur : ArgumentOutOfRangeException

- **Message** : `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Cause** : Dans Data Factory, les valeurs DateTime sont prises en charge dans la plage comprise entre 0001-01-01 00:00:00 et 9999-12-31 23:59:59. Cependant, Oracle prend en charge une plage plus étendue de valeurs DateTime (comme les siècles av. J-C ou min/s>59), ce qui entraîne une défaillance dans Data Factory.

- **Recommandation** : 

    Pour voir si la valeur dans Oracle est comprise dans la plage de Data Factory, exécutez `select dump(<column name>)`. 

    Pour connaître la séquence d’octets dans le résultat, consultez [Comment les dates sont-elles stockées dans Oracle ?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).


## <a name="orc-format"></a>Format ORC

### <a name="error-code--orcjavainvocationexception"></a>Code d’erreur :  OrcJavaInvocationException

- **Message** : `An error occurred when invoking Java, message: %javaException;.`

- **Cause** : Lorsque le message d’erreur contient les chaînes « java.lang.OutOfMemory », « Java heap space » et « doubleCapacity », il s’agit généralement d’un problème de gestion de la mémoire dans une ancienne version du runtime d’intégration.

- **Recommandation** :  Si vous utilisez des runtime d’intégration auto-hébergés, nous vous recommandons une mise à niveau vers la version la plus récente.

- **Cause** : Lorsque le message d’erreur contient la chaîne « java.lang.OutOfMemory », le runtime d’intégration ne dispose pas de suffisamment de ressources pour traiter les fichiers.

- **Recommandation** :  Limitez les exécutions simultanées sur le runtime d’intégration. Pour les runtimes d’intégration auto-hébergés, effectuez un scale-up vers un ordinateur puissant doté d’une mémoire égale ou supérieure à 8 Go.

- **Cause** : Lorsque le message d’erreur contient la chaîne « NullPointerReference », cela peut être dû à une erreur temporaire.

- **Recommandation** :  Retentez l’opération. Si le problème persiste, contactez le support technique.

- **Cause** : Lorsque le message d’erreur contient la chaîne « BufferOverflowException », cela peut être dû à une erreur temporaire.

- **Recommandation** :  Retentez l’opération. Si le problème persiste, contactez le support technique.

- **Cause** : Lorsque le message d’erreur contient la chaîne « java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable can't be cast to org.apache.hadoop.io.Text », il peut s’agir d’un problème de conversion de type au sein du runtime Java. En règle générale, cela signifie que les données sources ne peuvent pas être gérées correctement dans le runtime Java.

- **Recommandation** :  Il s’agit d’un problème de données. Essayez d’utiliser une chaîne au lieu de char ou de varchar dans des données au format ORC.

### <a name="error-code--orcdatetimeexceedlimit"></a>Code d’erreur :  OrcDateTimeExceedLimit

- **Message** : `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Cause** : Si la valeur datetime est « 0001-01-01 00:00:00 », cela peut être dû à la différence entre le [calendrier julien et le calendrier grégorien](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Recommandation** :  Vérifiez la valeur des graduations et évitez d’utiliser la valeur DateTime « 0001-01-01 00:00:00 ».


## <a name="parquet-format"></a>Format Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Code d’erreur :  ParquetJavaInvocationException

- **Message** : `An error occurred when invoking java, message: %javaException;.`

- **Cause** : Lorsque le message d’erreur contient les chaînes « java.lang.OutOfMemory », « Java heap space » et « doubleCapacity », il s’agit généralement d’un problème de gestion de la mémoire dans une ancienne version du runtime d’intégration.

- **Recommandation** :  Si vous utilisez des runtimes d’intégration auto-hébergés et que la version est antérieure à 3.20.7159.1, nous vous recommandons une mise à niveau vers la version la plus récente.

- **Cause** : Lorsque le message d’erreur contient la chaîne « java.lang.OutOfMemory », le runtime d’intégration ne dispose pas de suffisamment de ressources pour traiter les fichiers.

- **Recommandation** :  Limitez les exécutions simultanées sur le runtime d’intégration. Pour les runtimes d’intégration auto-hébergés, effectuez un scale-up vers un ordinateur puissant doté d’une mémoire égale ou supérieure à 8 Go.

- **Cause** : Lorsque le message d’erreur contient la chaîne « NullPointerReference », cela peut être dû à une erreur temporaire.

- **Recommandation** :  Retentez l’opération. Si le problème persiste, contactez le support technique.


### <a name="error-code--parquetinvalidfile"></a>Code d’erreur :  ParquetInvalidFile

- **Message** : `File is not a valid Parquet file.`

- **Cause** : Il s’agit d’un problème de fichier Parquet.

- **Recommandation** :  Vérifiez que l’entrée est un fichier Parquet valide.


### <a name="error-code--parquetnotsupportedtype"></a>Code d’erreur :  ParquetNotSupportedType

- **Message** : `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Cause** : Le format Parquet n’est pas pris en charge dans Azure Data Factory.

- **Recommandation** :  Vérifiez les données sources en accédant à [Formats de fichier et codecs de compression pris en charge par l’activité de copie dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs).


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Code d’erreur :  ParquetMissedDecimalPrecisionScale

- **Message** : `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Cause** : La précision et l’échelle des nombres ont été analysées, mais aucune information de ce type n’a été fournie.

- **Recommandation** :  La source ne retourne pas les informations correctes de précision et d’échelle. Vérifiez les informations dans la colonne posant problème.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Code d’erreur :  ParquetInvalidDecimalPrecisionScale

- **Message** : `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Cause** : Le schéma n’est pas valide.

- **Recommandation** :  Vérifiez la précision et l’échelle de la colonne posant problème.


### <a name="error-code--parquetcolumnnotfound"></a>Code d’erreur :  ParquetColumnNotFound

- **Message** : `Column %column; does not exist in Parquet file.`

- **Cause** : Le schéma source est incompatible avec le schéma du récepteur.

- **Recommandation** :  Vérifiez les mappages dans l’activité. Assurez-vous que la colonne source peut être mappée à la colonne du récepteur appropriée.


### <a name="error-code--parquetinvaliddataformat"></a>Code d’erreur :  ParquetInvalidDataFormat

- **Message** : `Incorrect format of %srcValue; for converting to %dstType;.`

- **Cause** : Les données ne peuvent pas être converties dans le type spécifié dans mappings.source.

- **Recommandation** :  Revérifiez les données sources ou spécifiez le type de données approprié pour cette colonne dans le mappage des colonnes de l’activité de copie. Pour plus d’informations, consultez [Formats de fichier et codecs de compression pris en charge par l’activité de copie dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs).


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Code d’erreur :  ParquetDataCountNotMatchColumnCount

- **Message** : `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Cause** :  Incompatibilité entre le nombre de colonnes sources et le nombre de colonnes du récepteur.

- **Recommandation** :  Revérifiez que le nombre de colonnes sources est identique au nombre de colonnes du récepteur dans « mappage ».


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Code d’erreur :  ParquetDataTypeNotMatchColumnType

- **Message** : `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Cause** : Les données de la source ne peuvent pas être converties dans le type défini dans le récepteur.

- **Recommandation** :  Spécifiez un type correct dans mappage.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Code d’erreur :  ParquetBridgeInvalidData

- **Message** : `%message;`

- **Cause** : La valeur des données a dépassé la limite.

- **Recommandation** :  Retentez l’opération. Si le problème persiste, contactez-nous.


### <a name="error-code--parquetunsupportedinterpretation"></a>Code d’erreur :  ParquetUnsupportedInterpretation

- **Message** : `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Cause** : Ce scénario n’est pas pris en charge.

- **Recommandation** :  « ParquetInterpretFor » ne doit pas être « sparkSql ».


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Code d’erreur :  ParquetUnsupportFileLevelCompressionOption

- **Message** : `File level compression is not supported for Parquet.`

- **Cause** : Ce scénario n’est pas pris en charge.

- **Recommandation** :  Supprimez « CompressionType » dans la charge utile.


### <a name="error-code--usererrorjniexception"></a>Code d’erreur :  UserErrorJniException

- **Message** : `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Cause** : Une machine virtuelle Java ne peut pas être créée, car des arguments (généraux) non conformes sont définis.

- **Recommandation** :  Connectez-vous à la machine qui héberge *chaque nœud* de votre IR auto-hébergé. Vérifiez que la variable système est définie correctement, comme suit : `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`. Redémarrez tous les nœuds IR, puis réexécutez le pipeline.


### <a name="arithmetic-overflow"></a>Dépassement arithmétique

- **Symptômes** : Un message d’erreur s’est produit pendant la copie de fichiers Parquet : `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Cause** : Pour l’heure, seules les décimales d’une précision <= 38 et une longueur de la partie entière <= 20 sont prises en charge lors de la copie de fichiers d’Oracle vers Parquet. 

- **Résolution** : Pour contourner le problème, vous pouvez convertir toutes les colonnes avec ce problème en VARCHAR2.


### <a name="no-enum-constant"></a>Absence de constante enum

- **Symptômes** : Un message d’erreur s’est affiché pendant la copie des données au format Paquet : `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` ou `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`.

- **Cause** : 

    Le problème peut être dû à la présence d’espaces blancs ou de caractères spéciaux non pris en charge (comme ,;{}()\n\t=) dans le nom de colonne, car Parquet ne prend pas en charge un tel format. 

    Par exemple, le nom de colonne *contoso(test)* analyse le type entre crochets dans le [code](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");`. L’erreur est levée, car il n’existe pas de type « test ».

    Pour vérifier les types pris en charge, accédez au site GitHub [apache/parquet-mr site](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Résolution** : 

    Revérifiez ce qui suit :
    - Le nom de la colonne du récepteur contient des espaces blancs.
    - La première ligne contenant des espaces blancs sert de nom de colonne.
    - Le type OriginalType est pris en charge. Essayez d’éviter d’utiliser ces caractères spéciaux : `,;{}()\n\t=`. 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>Code d’erreur :  RestSinkCallFailed

- **Message** : `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Cause** : Cette erreur se produit quand Azure Data Factory communique avec le point de terminaison REST via le protocole HTTP et que l’opération de requête échoue.

- **Recommandation** :  Vérifiez le code d’état HTTP ou le message dans le message d’erreur et corrifer le problème du serveur distant.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Réponse réseau inattendue du connecteur REST

- **Symptômes** : Le point de terminaison reçoit parfois une réponse inattendue (400, 401, 403, 500) du connecteur REST.

- **Cause** : Le connecteur source REST utilise l’URL et la méthode/l’en-tête/le corps HTTP du service/du jeu de données/de la copie source comme paramètres lors de la construction d’une requête HTTP. Le problème est probablement dû à des erreurs dans un ou plusieurs paramètres spécifiés.

- **Résolution** : 
    - Utilisez 'curl' dans une fenêtre d’invite de commandes pour vérifier si le paramètre est la cause ou non (les en-têtes **Accept** et **User-Agent** doivent toujours être inclus) :
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Si la commande retourne la même réponse inattendue, corrigez les paramètres ci-dessus en utilisant 'curl' jusqu’à obtenir la réponse attendue. 

      Vous pouvez également utiliser 'curl--help' pour exécuter des options plus avancées de la commande.

    - Si seul le connecteur REST Data Factory retourne une réponse inattendue, contactez le support technique de Microsoft pour poursuivre la résolution des problèmes.
    
    - Notez que 'curl' peut ne pas convenir pour reproduire le problème de validation du certificat SSL. Dans certains scénarios, la commande 'curl' a été exécutée avec succès sans rencontrer de problème de validation du certificat SSL. Toutefois, lorsque la même URL est exécutée dans un navigateur, aucun certificat SSL n’est réellement retourné pour permettre au client d’établir une relation de confiance avec le serveur.

      Les outils comme **Postman** et **Fiddler** sont recommandés dans le cas ci-dessus.


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>Code d’erreur :  SftpOperationFail

- **Message** : `Failed to '%operation;'. Check detailed error from SFTP.`

- **Cause** : Problème avec l’opération SFTP.

- **Recommandation** :  Consultez les détails de l’erreur dans SFTP.


### <a name="error-code--sftprenameoperationfail"></a>Code d’erreur :  SftpRenameOperationFail

- **Message** : `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Cause** : Votre serveur SFTP ne prend pas en charge le changement de nom du fichier temporaire.

- **Recommandation** :  Définissez « useTempFileRename » sur false dans le récepteur de copie pour désactiver le chargement dans le fichier temporaire.


### <a name="error-code--sftpinvalidsftpcredential"></a>Code d’erreur :  SftpInvalidSftpCredential

- **Message** : `Invalid SFTP credential provided for '%type;' authentication type.`

- **Cause** : Le contenu de la clé privée est extrait d’Azure Key Vault ou du SDK, mais il n’est pas correctement encodé.

- **Recommandation** :  

    Si le contenu de la clé privée provient de votre coffre de clés, le fichier de clé d’origine peut fonctionner si vous le chargez directement dans le service lié SFTP.

    Pour plus d’informations, consultez [Copier des données depuis et vers le serveur SFTP à l’aide d’Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication). Le contenu de la clé privée est un contenu de clé privée SSH encodé en base64.

    Encodez *l’intégralité* du contenu du fichier de clé privée d’origine avec un encodage en base64 et stockez la chaîne encodée dans votre coffre de clés. Le fichier de clé privée d’origine est celui qui peut fonctionner dans le service lié SFTP si vous sélectionnez **Charger** à partir d’un fichier.

    Voici quelques exemples que vous pouvez utiliser pour générer la chaîne :

    - Utilisation de code C# :

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Utilisation de code Python :

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Utilisez un outil de conversion en base64 tiers. Nous vous recommandons d’utiliser l’outil [Encoder au format Base64](https://www.base64encode.org/).

- **Cause** : Le format choisi pour le contenu de la clé était incorrect.

- **Recommandation** :  

    La clé privée SSH au format PKCS#8 (commençant par « -----BEGIN ENCRYPTED PRIVATE KEY----- ») n’est actuellement pas prise en charge pour l’accès au serveur SFTP dans Data Factory. 

    Exécutez les commandes ci-dessous pour convertir la clé au format de clé SSH classique (commençant par « ------BEGIN RSA PRIVATE KEY----- ») :

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Cause** : Contenu de clé privée ou informations d’identification non valides.

- **Recommandation** :  Servez-vous d’un outil comme WinSCP pour vérifier si votre fichier de clé ou votre mot de passe est correct.

### <a name="sftp-copy-activity-failed"></a>Échec de l’activité de copie SFTP

- **Symptômes** : 
  * Code d’erreur : UserErrorInvalidColumnMappingColumnNotFound 
  * Message d’erreur : `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Cause** : La source ne comporte pas de colonne nommée « AccMngr ».

- **Résolution** : Pour déterminer si la colonne « AccMngr » existe, vérifiez la configuration de votre jeu de données en mappant la colonne du jeu de données de destination.


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>Code d’erreur :  SftpFailedToConnectToSftpServer

- **Message** : `Failed to connect to SFTP server '%server;'.`

- **Cause** : Si le message d’erreur contient la chaîne « L’opération de lecture de socket a expiré après 30000 millisecondes », il est possible que le type de service lié soit incorrect pour le serveur SFTP. Par exemple, vous utilisez peut-être le service lié FTP pour vous connecter au serveur SFTP.

- **Recommandation** :  Vérifiez le port du serveur cible. Par défaut, SFTP utilise le port 22.

- **Cause** : Si le message d’erreur contient la chaîne « La réponse du serveur ne contient pas d’identification de protocole SSH », il est possible que le serveur SFTP limite la connexion. Data Factory crée plusieurs connexions pour opérer des téléchargements parallèles à partir du serveur SFTP et atteint parfois la limitation du serveur SFTP. En règle générale, les différents serveurs retournent des erreurs différentes lorsqu’ils rencontrent une limitation.

- **Recommandation** :  

    Définissez le nombre maximal de connexions simultanées du jeu de données SFTP sur 1, puis réexécutez l’activité de copie. Si elle aboutit, cela est le signe que la limitation est en cause.

    Si vous souhaitez promouvoir le faible débit, contactez votre administrateur SFTP pour augmenter le nombre limite de connexions simultanées ou vous pouvez procéder comme suit :

    * Si vous utilisez un IR auto-hébergé, ajoutez l’adresse IP de l’ordinateur IR auto-hébergé à la liste verte.
    * Lorsque vous utilisez Azure IR, consultez [Adresses IP Azure Integration Runtime](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses). Si vous ne souhaitez pas ajouter une plage d’adresses IP à la liste verte du serveur SFTP, utilisez plutôt le runtime d’intégration auto-hébergé.

## <a name="sharepoint-online-list"></a>Liste SharePoint Online

### <a name="error-code--sharepointonlineauthfailed"></a>Code d’erreur :  SharePointOnlineAuthFailed

- **Message** : `The access token generated failed, status code: %code;, error message: %message;.`

- **Cause** : L’ID du principal du service et la clé peuvent ne pas être correctement définis.

- **Recommandation** :  Vérifiez votre application inscrite (ID du principal du service) et la clé pour voir si elles sont correctement définies.


## <a name="xml-format"></a>Format XML

### <a name="error-code--xmlsinknotsupported"></a>Code d’erreur :  XmlSinkNotSupported

- **Message** : `Write data in XML format is not supported yet, choose a different format!`

- **Cause** : Un jeu de données XML a été utilisé en tant que jeu de données récepteur dans votre activité de copie.

- **Recommandation** :  Utilisez un jeu de données dans un format différent de celui du jeu de données récepteur.


### <a name="error-code--xmlattributecolumnnameconflict"></a>Code d’erreur :  XmlAttributeColumnNameConflict

- **Message** : `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Cause** : Un préfixe d’attribut a été utilisé, ce qui a provoqué le conflit.

- **Recommandation** :  Définissez une valeur différente de la propriété « attributePrefix ».


### <a name="error-code--xmlvaluecolumnnameconflict"></a>Code d’erreur :  XmlValueColumnNameConflict

- **Message** : `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Cause** : L’un des noms d’éléments enfants a été utilisé comme nom de colonne pour la valeur de l’élément.

- **Recommandation** :  Définissez une valeur différente de la propriété « valueColumn ».


### <a name="error-code--xmlinvalid"></a>Code d’erreur :  XmlInvalid

- **Message** : `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Cause** : Le fichier XML d’entrée n’est pas bien formé.

- **Recommandation** :  Corrigez le fichier XML pour qu’il soit correctement formé.


## <a name="general-copy-activity-error"></a>Erreur générale de l’activité de copie

### <a name="error-code--jrenotfound"></a>Code d’erreur :  JreNotFound

- **Message** : `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Cause** : L’IR auto-hébergé ne trouve pas le runtime Java. Le runtime Java est requis pour la lecture de sources spécifiques.

- **Recommandation** :  Vérifiez votre environnement de runtime d’intégration, consultez [Utiliser un runtime d’intégration auto-hébergé](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime).


### <a name="error-code--wildcardpathsinknotsupported"></a>Code d’erreur :  WildcardPathSinkNotSupported

- **Message** : `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Cause** : Le jeu de données du récepteur ne prend pas en charge les caractères génériques.

- **Recommandation** :  Vérifiez le jeu de données du récepteur et corrigez le chemin d’accès sans la valeur de caractère générique.


### <a name="fips-issue"></a>Problème FIPS

- **Symptômes** : L’activité de copie échoue pour une machine du runtime d'intégration auto-hébergé compatible FIPS avec le message d’erreur : `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.`. 

- **Cause** : Cette erreur peut se produire lorsque vous copiez des données avec des connecteurs tels que Blob Azure, SFTP, etc. FIPS (Federal Information Processing Standards) définit un certain ensemble d’algorithmes de chiffrement qui peuvent être utilisés. Lorsque le mode FIPS est activé sur l’ordinateur, certaines classes de chiffrement dont dépend l’activité de copie sont bloquées dans certains scénarios.

- **Résolution** : Découvrez [pourquoi nous ne recommandons plus le « mode FIPS »](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037), et déterminez si vous pouvez désactiver FIPS sur votre machine IR auto-hébergé.

    Ou, si vous souhaitez simplement laisser Azure Data Factory contourner FIPS et faire en sorte que l’exécution de l’activité aboutisse, procédez comme suit :

    1. Ouvrez le dossier dans lequel le runtime d’intégration auto-hébergé est installé. Le chemin est, en général, *C:\Program Files\Microsoft Integration Runtime \<IR version>\Shared*.

    2. Ouvrez le fichier *diawp.exe.config* et, à la fin de la section `<runtime>`, ajoutez `<enforceFIPSPolicy enabled="false"/>`, comme illustré ici :

        ![Capture d’écran d’une section du fichier diawp.exe.config montrant FIPS désactivé.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Enregistrez le fichier, puis redémarrez la machine du runtime d’intégration auto-hébergé.


## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
