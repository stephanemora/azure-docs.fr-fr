---
title: Résoudre les problèmes liés aux connecteurs dans Azure Data Factory
description: Découvrez comment résoudre les problèmes liés aux connecteurs dans Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 8cabc1031f9d0be772ba087109ae1dfc881ce163
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680079"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Résoudre les problèmes liés aux connecteurs dans Azure Data Factory

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés aux connecteurs dans Azure Data Factory.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Message d’erreur : Le serveur distant a retourné une erreur : (403) Interdit

- **Symptômes** : L’activité de copie échoue avec l’erreur suivante : 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Cause** : L’une des causes possibles est que le principal du service ou l’identité managée que vous utilisez n’a pas l’autorisation d’accéder au dossier/fichier donné.

- **Résolution** : Accordez des autorisations appropriées sur tous les dossiers et sous-dossiers que vous devez copier. Consultez [ce document](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Message d’erreur : Échec d’obtention du jeton d’accès à l’aide du principal du service. Erreur ADAL : service_unavailable

- **Symptômes** : Échec de l’activité de copie avec l’erreur suivante :

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Cause** : Lorsque le service d’émission de jeton de sécurité (STS) détenu par Azure Active Directory n’est pas disponible, c.-à-d. qu’il est trop occupé pour gérer les demandes, il renvoie une erreur HTTP 503. 

- **Résolution** : Réexécutez l’activité de copie au bout de quelques minutes.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

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

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Message d’erreur : La taille de la demande est trop grande

- **Symptômes** : Vous copiez des données dans Azure Cosmos DB avec la taille du lot d’écriture par défaut et vous rencontrez l’erreur *« **La taille de la demande est trop grande** »* .

- **Cause** : Cosmos DB limite la taille d’une demande unique à 2 Mo. La formule est la suivante : Taille de la demande = Taille de document unique * Taille du lot d’écriture. Si la taille de votre document est importante, le comportement par défaut entraîne une trop grande taille de demande. Vous pouvez ajuster la taille du lot d’écriture.

- **Résolution** : Dans le récepteur de l’activité de copie, réduisez la taille du lot d’écriture « Write batch size » (la valeur par défaut est 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Message d’erreur : Violation de contrainte d’index unique

- **Symptômes** : Lorsque vous copiez des données dans Cosmos DB, vous rencontrez l’erreur suivante :

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
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

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Message d’erreur : Informations d’identification SFTP non valides fournies pour le type d’authentification « SshPublicKey »

- **Symptômes** : Vous utilisez l’authentification `SshPublicKey` et rencontrez l’erreur suivante :

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Cause** : Il existe 3 causes possibles :

    1. Si vous utilisez l’interface utilisateur de création ADF pour créer le service lié SFTP, cette erreur signifie que la clé privée que vous choisissez d’utiliser a un format incorrect. Vous pouvez utiliser un format PKCS#8 de clé privée SSH, mais notez qu’ADF prend en charge uniquement le format de clé SSH traditionnel. Plus précisément, la différence entre le format PKCS#8 et le format de clé traditionnel est que le contenu de la clé PKCS#8 commence par «  *-----BEGIN ENCRYPTED PRIVATE KEY-----*  », tandis que le format de clé traditionnel commence par «  *-----BEGIN RSA PRIVATE KEY-----*  ».
    2. Si vous utilisez Azure Key Vault pour stocker le contenu de la clé privée ou si vous utilisez la méthode par programmation pour créer le service lié SFTP, cette erreur signifie que le contenu de la clé privée est incorrect et probablement qu’il n’est pas encodé en base64.
    3. Contenu de clé privée ou informations d’identification non valides.

- **Résolution** : 

    - Pour la cause no 1, exécutez les commandes suivantes pour convertir la clé au format de clé traditionnel, puis utilisez-la dans l’interface utilisateur de création ADF.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - Pour la cause no 2, pour générer une telle chaîne, le client peut utiliser les 2 méthodes ci-dessous :
    - Utilisation de l’outil de conversion base64 tiers : collez l’ensemble du contenu de la clé privée dans des outils d’[encodage et décodage base64](https://www.base64encode.org/), encodez-le en une chaîne de format base64, puis collez cette chaîne dans le coffre de clés ou utilisez cette valeur pour créer par programmation le service lié SFTP.
    - Utilisation du code C# :

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Pour la cause no 3, vérifiez bien si le fichier de clé ou le mot de passe est correct à l’aide d’autres outils pour valider si vous pouvez l’utiliser pour accéder correctement au serveur SFTP.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)



