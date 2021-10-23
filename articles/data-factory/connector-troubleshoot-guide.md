---
title: Résoudre les problèmes des connecteurs
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés aux connecteurs dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 9d27c292d044b39f841f86906deaf386357df7c7
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062753"
---
# <a name="troubleshoot-azure-data-factory-and-azure-synapse-analytics-connectors"></a>Résoudre les problèmes des connecteurs Azure Data Factory et Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment résoudre les problèmes liés aux connecteurs dans Azure Data Factory et Azure Synapse Analytics.  

## <a name="connector-specific-problems"></a>Problèmes spécifiques au connecteur

Vous pouvez consulter les pages de résolution des problèmes pour chaque connecteur afin de voir les problèmes qui lui sont spécifiques avec les explications de leurs causes et des recommandations pour les résoudre.

- [Stockage Blob Azure](connector-troubleshoot-azure-blob-storage.md)
- [Azure Cosmos DB (y compris le connecteur de l’API SQL)](connector-troubleshoot-azure-cosmos-db.md)
- [Azure Data Lake (Gen1 et Gen2)](connector-troubleshoot-azure-data-lake.md)
- [Azure Database pour PostgreSQL](connector-troubleshoot-postgresql.md)
- [Stockage Azure Files](connector-troubleshoot-azure-files.md)
- [Azure Synapse Analytics, Azure SQL Database et SQL Server](connector-troubleshoot-synapse-sql.md)
- [DB2](connector-troubleshoot-db2.md)
- [Format de texte délimité](connector-troubleshoot-delimited-text.md)
- [Dynamics 365, Dataverse (Common Data Service) et Dynamics CRM](connector-troubleshoot-dynamics-dataverse.md)
- [FTP, SFTP et HTTP](connector-troubleshoot-ftp-sftp-http.md)
- [Hive](connector-troubleshoot-hive.md)
- [Oracle](connector-troubleshoot-oracle.md)
- [Format ORC](connector-troubleshoot-orc.md)
- [Format Parquet](connector-troubleshoot-parquet.md)
- [REST](connector-troubleshoot-rest.md)
- [Liste SharePoint Online](connector-troubleshoot-sharepoint-online-list.md)
- [Format XML](connector-troubleshoot-xml.md)

## <a name="general-copy-activity-errors"></a>Erreurs générales de l’activité Copy

Les erreurs ci-dessous sont générales à l’activité Copy et peuvent se produire avec n’importe quel connecteur.

### <a name="error-code-jrenotfound"></a>Code d’erreur : JreNotFound

- **Message** : `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Cause** : L’IR auto-hébergé ne trouve pas le runtime Java. Le runtime Java est requis pour la lecture de sources spécifiques.

- **Recommandation** :  Vérifiez votre environnement de runtime d’intégration, consultez [Utiliser un runtime d’intégration auto-hébergé](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Code d’erreur : WildcardPathSinkNotSupported

- **Message** : `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Cause** : Le jeu de données du récepteur ne prend pas en charge les caractères génériques.

- **Recommandation** :  Vérifiez le jeu de données du récepteur et corrigez le chemin d’accès sans la valeur de caractère générique.


### <a name="fips-issue"></a>Problème FIPS

- **Symptômes** : L’activité de copie échoue pour une machine du runtime d'intégration auto-hébergé compatible FIPS avec le message d’erreur : `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.`. 

- **Cause** : Cette erreur peut se produire lorsque vous copiez des données avec des connecteurs tels que Blob Azure, SFTP, etc. FIPS (Federal Information Processing Standards) définit un certain ensemble d’algorithmes de chiffrement qui peuvent être utilisés. Lorsque le mode FIPS est activé sur l’ordinateur, certaines classes de chiffrement dont dépend l’activité de copie sont bloquées dans certains scénarios.

- **Résolution** : Découvrez [pourquoi nous ne recommandons plus le « mode FIPS »](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037), et déterminez si vous pouvez désactiver FIPS sur votre machine IR auto-hébergé.

    Par ailleurs, si vous souhaitez simplement contourner FIPS et faire en sorte que l’exécution de l’activité aboutisse, procédez comme suit :

    1. Ouvrez le dossier dans lequel le runtime d’intégration auto-hébergé est installé. Le chemin est, en général, *C:\Program Files\Microsoft Integration Runtime \<IR version>\Shared*.

    2. Ouvrez le fichier *diawp.exe.config* et, à la fin de la section `<runtime>`, ajoutez `<enforceFIPSPolicy enabled="false"/>`, comme illustré ici :

        :::image type="content" source="./media/connector-troubleshoot-guide/disable-fips-policy.png" alt-text="Capture d’écran d’une section du fichier diawp.exe.config montrant FIPS désactivé.":::

    3. Enregistrez le fichier, puis redémarrez la machine du runtime d’intégration auto-hébergé.

### <a name="error-code-jniexception"></a>Code d’erreur : JniException

- **Message** : `An error occurred when invoking Java Native Interface.`

- **Cause** : Si le message d’erreur contient « Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.] » (« impossible de créer la JVM : code de retour JNI [-6] [échec de l’appel JNI : arguments non valides.] »), la cause possible est que la machine virtuelle Java (JVM) ne peut pas être créée, car certains arguments non conformes (globaux) sont définis.

- **Recommandation** : Connectez-vous à la machine qui héberge *chaque nœud* de votre IR auto-hébergé. Vérifiez que la variable système est définie correctement, comme suit : `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8G`. Redémarrez tous les nœuds de l’IR, puis réexécutez le pipeline.

### <a name="error-code-getoauth2accesstokenerrorresponse"></a>Code d’erreur : GetOAuth2AccessTokenErrorResponse

- **Message** : `Failed to get access token from your token endpoint. Error returned from your authorization server: %errorResponse;.`

- **Cause** : Votre ID client ou clé secrète client n’est pas valide, et l’authentification a échoué sur votre serveur d’autorisation.

- **Recommandation** : Corrigez tous les paramètres de transmission des informations d’identification du client OAuth2 de votre serveur d’autorisation.

### <a name="error-code-failedtogetoauth2accesstoken"></a>Code d’erreur : FailedToGetOAuth2AccessToken

- **Message** : `Failed to get access token from your token endpoint. Error message: %errorMessage;.`

- **Cause** : Les paramètres de transmission des informations d’identification du client OAuth2 ne sont pas valides.

- **Recommandation** : Corrigez tous les paramètres de transmission des informations d’identification du client OAuth2 de votre serveur d’autorisation.

### <a name="error-code-oauth2accesstokentypenotsupported"></a>Code d’erreur : OAuth2AccessTokenTypeNotSupported

- **Message** : `The toke type '%tokenType;' from your authorization server is not supported, supported types: '%tokenTypes;'.`

- **Cause** : Votre serveur d’autorisation n’est pas pris en charge.

- **Recommandation** : Utilisez un serveur d’autorisation qui peut retourner des jetons avec des types de jetons pris en charge.

### <a name="error-code-oauth2clientidcolonnotallowed"></a>Code d’erreur : OAuth2ClientIdColonNotAllowed

- **Message** : `The character colon(:) is not allowed in clientId for OAuth2ClientCredential authentication.`

- **Cause** : Votre ID client comprend le caractère deux-points non valide (`:`).

- **Recommandation** : Utilisez un ID de client valide.

### <a name="error-code-managedidentitycredentialobjectnotsupported"></a>Code d’erreur : ManagedIdentityCredentialObjectNotSupported

- **Message** : `Managed identity credential is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **Recommandation** : Vérifiez la version prise en charge et mettez à niveau le runtime d’intégration vers une version plus récente.

### <a name="error-code-querymissingformatsettingsindataset"></a>Code d’erreur : QueryMissingFormatSettingsInDataset

- **Message** : `The format settings are missing in dataset %dataSetName;.`

- **Cause** : Le type de jeu de données est binaire, ce qui n’est pas pris en charge.

- **Recommandation** : Utilisez à la place le jeu de données DelimitedText, JSON, Avro, ORC ou Parquet.

- **Cause** : Pour le stockage de fichiers, les paramètres de format sont absents du jeu de données.

- **Recommandation** : Désélectionnez la « copie binaire » dans le jeu de données et définissez les paramètres de mise en forme appropriés.

### <a name="error-code-queryunsupportedcommandbehavior"></a>Code d’erreur : QueryUnsupportedCommandBehavior

- **Message** : `The command behavior "%behavior;" is not supported.`

- **Recommandation** : N’ajoutez pas le comportement de la commande en tant que paramètre de l’URL de requête d’API en préversion ou GetSchema.

### <a name="error-code-dataconsistencyfailedtogetsourcefilemetadata"></a>Code d’erreur : DataConsistencyFailedToGetSourceFileMetadata

- **Message** : `Failed to retrieve source file ('%name;') metadata to validate data consistency.`

- **Cause** : Il existe un problème temporaire dans le magasin de données récepteur ou bien la récupération des métadonnées à partir du magasin de données récepteur n’est pas autorisée.

### <a name="error-code-dataconsistencyfailedtogetsinkfilemetadata"></a>Code d’erreur : DataConsistencyFailedToGetSinkFileMetadata

- **Message** : `Failed to retrieve sink file ('%name;') metadata to validate data consistency.`

- **Cause** : Il existe un problème temporaire dans le magasin de données récepteur ou bien la récupération des métadonnées à partir du magasin de données récepteur n’est pas autorisée.

### <a name="error-code-dataconsistencyvalidationnotsupportedfornondirectbinarycopy"></a>Code d’erreur : DataConsistencyValidationNotSupportedForNonDirectBinaryCopy

- **Message** : `Data consistency validation is not supported in current copy activity settings.`

- **Cause** : La validation de la cohérence des données est uniquement prise en charge dans le scénario de copie binaire directe.

- **Recommandation** : Supprimez la propriété « validateDataConsistency » dans la charge utile de l’activité de copie.

### <a name="error-code-dataconsistencyvalidationnotsupportedforlowversionselfhostedintegrationruntime"></a>Code d’erreur : DataConsistencyValidationNotSupportedForLowVersionSelfHostedIntegrationRuntime

- **Message** : `'validateDataConsistency' is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **Recommandation** : Vérifiez la version prise en charge du runtime d’intégration et mettez-la à niveau vers une version supérieure, ou supprimez la propriété « validateDataConsistency » des activités de copie.

### <a name="error-code-skipmissingfilenotsupportedfornondirectbinarycopy"></a>Code d’erreur : SkipMissingFileNotSupportedForNonDirectBinaryCopy

- **Message** : `Skip missing file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **Recommandation** : Supprimez « fileMissing » du paramètre skipErrorFile dans la charge utile de l’activité de copie.

### <a name="error-code-skipinconsistencydatanotsupportedfornondirectbinarycopy"></a>Code d’erreur : SkipInconsistencyDataNotSupportedForNonDirectBinaryCopy

- **Message** : `Skip inconsistency is not supported in current copy activity settings, it's only supported with direct binary copy when validateDataConsistency is true.`

- **Recommandation** : supprimez « dataInconsistency » du paramètre skipErrorFile dans la charge utile de l’activité de copie.

### <a name="error-code-skipforbiddenfilenotsupportedfornondirectbinarycopy"></a>Code d’erreur : SkipForbiddenFileNotSupportedForNonDirectBinaryCopy

- **Message** : `Skip forbidden file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **Recommandation** : Supprimez « fileForbidden » du paramètre skipErrorFile dans la charge utile de l’activité de copie.

### <a name="error-code-skipforbiddenfilenotsupportedforthisconnector"></a>Code d’erreur : SkipForbiddenFileNotSupportedForThisConnector

- **Message** : `Skip forbidden file is not supported for this connector: ('%connectorName;').`

- **Recommandation** : supprimez « fileForbidden » du paramètre skipErrorFile dans la charge utile de l’activité de copie.

### <a name="error-code-skipinvalidfilenamenotsupportedfornondirectbinarycopy"></a>Code d’erreur : SkipInvalidFileNameNotSupportedForNonDirectBinaryCopy

- **Message** : `Skip invalid file name is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **Recommandation** : supprimez « invalidFileName » du paramètre skipErrorFile dans la charge utile de l’activité de copie.

### <a name="error-code-skipinvalidfilenamenotsupportedforsource"></a>Code d’erreur : SkipInvalidFileNameNotSupportedForSource

- **Message** : `Skip invalid file name is not supported for '%connectorName;' source.`

- **Recommandation** : Supprimez « invalidFileName » du paramètre skipErrorFile dans la charge utile de l’activité de copie.

### <a name="error-code-skipinvalidfilenamenotsupportedforsink"></a>Code d’erreur : SkipInvalidFileNameNotSupportedForSink

- **Message** : `Skip invalid file name is not supported for '%connectorName;' sink.`

- **Recommandation** : Supprimez « invalidFileName » du paramètre skipErrorFile dans la charge utile de l’activité de copie.

### <a name="error-code-skipallerrorfilenotsupportedfornonbinarycopy"></a>Code d’erreur : SkipAllErrorFileNotSupportedForNonBinaryCopy

- **Message** : `Skip all error file is not supported in current copy activity settings, it's only supported with binary copy with folder.`

- **Recommandation** : Supprimez « allErrorFile » du paramètre skipErrorFile dans la charge utile de l’activité de copie.

### <a name="error-code-deletefilesaftercompletionnotsupportedfornondirectbinarycopy"></a>Code d’erreur : DeleteFilesAfterCompletionNotSupportedForNonDirectBinaryCopy

- **Message** : `'deleteFilesAfterCompletion' is not support in current copy activity settings, it's only supported with direct binary copy.`

- **Recommandation** : Supprimez le paramètre « deleteFilesAfterCompletion » ou utilisez la copie binaire directe.

### <a name="error-code-deletefilesaftercompletionnotsupportedforthisconnector"></a>Code d’erreur : DeleteFilesAfterCompletionNotSupportedForThisConnector

- **Message** : `'deleteFilesAfterCompletion' is not supported for this connector: ('%connectorName;').`

- **Recommandation** : Supprimez le paramètre « deleteFilesAfterCompletion » dans la charge utile de l’activité de copie.

### <a name="error-code-failedtodownloadcustomplugins"></a>Code d’erreur : FailedToDownloadCustomPlugins

- **Message** : `Failed to download custom plugins.`

- **Cause** : Liens de téléchargement non valides ou problèmes de connectivité temporaires.

- **Recommandation** : Réessayez si le message indique qu’il s’agit d’un problème temporaire. Si le problème persiste, contactez l’équipe du support technique.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)