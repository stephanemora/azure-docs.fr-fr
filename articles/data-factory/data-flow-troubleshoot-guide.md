---
title: Résoudre les problèmes liés aux flux de données de mappage
description: Découvrez comment résoudre les problèmes de flux de données dans Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/25/2021
ms.openlocfilehash: 72ab685b58f7d940fe4d682cacba6212fe80ced8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933081"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Résoudre les problèmes liés aux flux de données de mappage dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés aux flux de données de mappage dans Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Messages et codes d’erreur courants 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Code d’erreur : DF-Executor-SourceInvalidPayload
- **Message** : L’exécution de l’aperçu des données, du débogage et du flux de données de pipeline a échoué car le conteneur n’existe pas
- **Cause** : Un jeu de données comprend un conteneur qui n’existe pas dans le stockage.
- **Recommandation** : Vérifiez que le conteneur référencé dans votre jeu de données existe et est accessible.

### <a name="error-code-df-executor-systeminvalidjson"></a>Code d’erreur : DF-Executor-SystemInvalidJson

- **Message** : Erreur d’analyse JSON, encodage ou multiligne non pris en charge.
- **Cause** : Problèmes possibles avec le fichier JSON (encodage non pris en charge, octets endommagés ou utilisation de la source JSON comme document unique sur de nombreuses lignes imbriquées).
- **Recommandation** : Vérifiez que l’encodage du fichier JSON est pris en charge. Sur la transformation source qui utilise un jeu de données JSON, développez **Paramètres JSON** et activez **Un seul document**.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Code d’erreur : DF-Executor-BroadcastTimeout

- **Message** : Erreur liée à l’expiration de la diffusion de la jointure. Vérifiez que la diffusion en streaming produit des données en moins de 60 s dans les exécutions de débogage et en moins de 300 s dans les exécutions de travaux.
- **Cause** : Le délai d’expiration par défaut de la diffusion est de 60 secondes pour les exécutions de débogage et de 300 secondes pour les exécutions de travaux. Le flux choisi pour la diffusion est trop volumineux pour produire des données respectant cette limite.
- **Recommandation** : Sous l’onglet **Optimiser**, vérifiez les transformations de jointure, d’existence et de recherche de votre flux de données. L’option par défaut pour la diffusion est **Auto**. Si l’option **Auto** est définie ou que vous indiquez manuellement la diffusion du côté gauche ou droit sous **Fixe**, vous pouvez soit définir une configuration de runtime d’intégration Azure plus importante, soit désactiver la diffusion. Pour obtenir des performances optimales dans les flux de données, nous vous recommandons d’autoriser Spark à diffuser à l’aide de l’option **Auto** et d’utiliser un runtime d’intégration Azure à mémoire optimisée. 
 
  Si vous exécutez le flux de données dans une exécution de test de débogage à partir d’une exécution de pipeline de débogage, vous pouvez rencontrer cette situation plus fréquemment. En effet, Azure Data Factory limite le délai d’expiration de diffusion à 60 secondes afin de maintenir une expérience de débogage plus rapide. Vous pouvez étendre ce délai d’expiration au délai de 300 secondes d’une exécution déclenchée. Pour ce faire, vous pouvez utiliser l’option **Déboguer** > **Utiliser le runtime d’activité** afin d’utiliser le runtime d’intégration Azure défini dans votre activité de pipeline Exécuter un flux de données.

- **Message** : Erreur de délai d’attente de la diffusion de la jonction. Pour éviter ce problème, vous pouvez choisir l’option de diffusion « Désactivé » dans la transformation de recherche/existence/jonction. Si vous envisagez de diffuser l’option de jonction pour améliorer les performances, assurez-vous que le flux de diffusion peut produire des données dans un délai de 60 secondes dans des exécutions de débogage et un délai de 300 secondes dans les exécutions de travaux.
- **Cause** : Le délai d’expiration par défaut de la diffusion est de 60 secondes dans les exécutions de débogage et de 300 secondes dans les exécutions de travaux. Dans la jointure de diffusion, le flux choisi pour la diffusion est trop volumineux pour produire des données respectant cette limite. Si une jointure de diffusion n’est pas utilisée, la diffusion par défaut effectuée par un flux de données peut atteindre la même limite.
- **Recommandation** : Désactivez l’option de diffusion ou évitez de diffuser des flux de données volumineux pour lesquels le traitement peut prendre plus de 60 secondes. Choisissez un flux plus petit à diffuser. Les fichiers sources et les tables Azure SQL Data Warehouse volumineux ne sont généralement pas de bons choix. En l’absence d’une jointure de diffusion, utilisez un cluster plus grand si cette erreur se produit.

### <a name="error-code-df-executor-conversion"></a>Code d’erreur : DF-Executor-Conversion

- **Message** : Échec de conversion en date ou heure en raison d’un caractère non valide
- **Cause** : Les données ne sont pas au format attendu.
- **Recommandation** : Utilisez le type de données correct.

### <a name="error-code-df-executor-invalidcolumn"></a>Code d’erreur : DF-Executor-InvalidColumn
- **Message** : Le nom de colonne doit être spécifié dans la requête, définissez un alias si vous utilisez une fonction SQL
- **Cause** : Aucun nom de colonne n’est spécifié.
- **Recommandation** : Définissez un alias si vous utilisez une fonction SQL telle que min() ou max().

### <a name="error-code-df-executor-drivererror"></a>Code d’erreur : DF-Executor-DriverError
- **Message** : INT96 est un type d’horodatage hérité qui n’est pas pris en charge par le flux de données ADF. Envisagez de mettre à niveau le type de colonne vers les types les plus récents.
- **Cause** : Erreur de pilote.
- **Recommandation** : INT96 est un type d’horodatage hérité qui n’est pas pris en charge par le flux de données Azure Data Factory. Envisagez de mettre à niveau le type de colonne vers le type le plus récent.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Code d’erreur : DF-Executor-BlockCountExceedsLimitError
- **Message** : Le nombre de blocs non validés ne peut pas dépasser la limite maximale de 100 000 blocs. Vérifiez la configuration de l’objet blob.
- **Cause** : Le nombre maximal de blocs non validés dans un objet blob est de 100 000.
- **Recommandation** : Pour plus d’informations sur ce problème, contactez l’équipe produit Microsoft.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Code d’erreur : DF-Executor-PartitionDirectoryError
- **Message** : Le chemin source spécifié comporte plusieurs répertoires partitionnés (par exemple, <Source Path>/<Répertoire racine de partition 1>/a=10/b=20, <Source Path>/<Répertoire racine de partition 2>/c=10/d=30) ou un répertoire partitionné avec un autre fichier ou un répertoire non partitionné (par exemple, <Source Path>/<Répertoire racine de partition 1>/a=10/b=20, <Source Path>/Répertoire 2/fichier1). Supprimez le répertoire racine de la partition du chemin source et lisez-le par le biais d’une transformation source distincte.
- **Cause** : Le chemin source comporte plusieurs répertoires partitionnés ou un répertoire partitionné avec un autre fichier ou un répertoire non partitionné.
- **Recommandation** : Supprimez le répertoire racine partitionné du chemin source et lisez-le par le biais d’une transformation source distincte.

### <a name="error-code-df-executor-invalidtype"></a>Code d’erreur : DF-Executor-InvalidType
- **Message** : Vérifiez que le type de paramètre correspond au type de valeur transmis. Le passage de paramètres flottants à partir de pipelines n’est pas pris en charge actuellement.
- **Cause** : Le type de données pour le type déclaré n’est pas compatible avec la valeur de paramètre réelle.
- **Recommandation** : Vérifiez que les valeurs de paramètre passées dans le flux de données correspondent au type déclaré.

### <a name="error-code-df-executor-parseerror"></a>Code d’erreur : DF-Executor-ParseError
- **Message** : Impossible d’analyser l’expression.
- **Cause** : Une expression a généré des erreurs d’analyse en raison d’une mise en forme incorrecte.
- **Recommandation** : Vérifiez la mise en forme dans l’expression.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Code d’erreur : DF-Executor-SystemImplicitCartesian
- **Message** : Le produit cartésien implicite pour la jointure INNER n’est pas pris en charge. Utilisez plutôt CROSS JOIN. Les colonnes utilisées dans la jointure doivent créer une clé unique pour les lignes.
- **Cause** : Les produits cartésiens implicites pour les jointures INNER entre les plans logiques ne sont pas pris en charge. Si vous utilisez des colonnes dans la jointure, créez une clé unique.
- **Recommandation** : Pour les jointures non basées sur l’égalité, utilisez CROSS JOIN.

### <a name="error-code-getcommand-outputasync-failed"></a>Code d’erreur : Échec de GetCommand OutputAsync
- **Message** : Lors du débogage du flux de données et de l’aperçu des données : Échec de GetCommand OutputAsync avec...
- **Cause** : Cette erreur est une erreur de service back-end. 
- **Recommandation** : Retentez l’opération et redémarrez votre session de débogage. Si la nouvelle tentative et le redémarrage ne résolvent pas le problème, contactez le support technique. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Code d’erreur : DF-Executor-OutOfMemoryError
 
- **Message** : Le cluster a rencontré un problème de mémoire insuffisante pendant l’exécution, réessayez en utilisant un runtime d’intégration avec un nombre de cœurs plus élevé et/ou un type de calcul à mémoire optimisée.
- **Cause** : Le cluster manque de mémoire.
- **Recommandation** : Les clusters de débogage sont destinés au développement. Utilisez l’échantillonnage des données ainsi que le type et la taille de calcul appropriés pour exécuter la charge utile. Pour obtenir des conseils sur les performances, consultez le [guide des performances de flux de données de mappage](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Code d’erreur : DF-Executor-illegalArgument

- **Message** : Vérifiez que la clé d’accès de votre service lié est correcte.
- **Cause** : Le nom du compte ou la clé d’accès est incorrect.
- **Recommandation** : Vérifiez que le nom du compte ou la clé d’accès spécifiés dans votre service lié sont corrects. 

### <a name="error-code-df-executor-columnunavailable"></a>Code d’erreur : DF-Executor-ColumnUnavailable
- **Message** : Le nom de colonne utilisé dans l’expression n’est pas disponible ou n’est pas valide.
- **Cause** : Un nom de colonne non valide ou non disponible est utilisé dans une expression.
- **Recommandation** : Vérifiez les noms de colonne utilisés dans les expressions.

 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Code d’erreur : DF-Executor-OutOfDiskSpaceError
- **Message** : Erreur interne du serveur
- **Cause** : Le cluster manque d’espace disque.
- **Recommandation** : Relancez le pipeline. Si cela ne résout pas le problème, contactez le support technique.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Code d’erreur : DF-Executor-StoreIsNotDefined
- **Message** : La configuration de magasin n’est pas définie. Cette erreur est peut-être provoquée par une attribution incorrecte de paramètre dans le pipeline.
- **Cause** : Non déterminée.
- **Recommandation** : Vérifiez l’attribution de la valeur du paramètre dans le pipeline. Une expression de paramètre peut contenir des caractères non valides.


### <a name="error-code-4502"></a>Code d’erreur : 4502
- **Message** : Des exécutions de MappingDataflow simultanées importantes entraînent des échecs en raison de la limitation sous le runtime d’intégration.
- **Cause** : Un grand nombre d’exécutions d’activités de flux de données se produisent simultanément sur le runtime d’intégration. Pour plus d’informations, consultez [Limites d’Azure Data Factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Recommandation** : Si vous souhaitez exécuter davantage d’activités de flux de données en parallèle, répartissez-les sur plusieurs runtimes d’intégration.


### <a name="error-code-invalidtemplate"></a>Code d’erreur : InvalidTemplate
- **Message** : L’expression de pipeline ne peut pas être évaluée.
- **Cause** : L’expression de pipeline passée dans l’activité de flux de données n’est pas traitée correctement en raison d’une erreur de syntaxe.
- **Recommandation** : Dans la supervision des activités, vérifiez l’expression liée à votre activité.

### <a name="error-code-2011"></a>Code d’erreur : 2011
- **Message** : L’activité était exécutée sur Azure Integration Runtime et n’a pas pu déchiffrer les informations d’identification du magasin de données ou du calcul connecté via un runtime d’intégration auto-hébergé. Vérifiez la configuration des services liés associés à cette activité et veillez à utiliser le type de runtime d’intégration approprié.
- **Cause** : Le flux de données ne prend pas en charge les services liés sur les runtimes d’intégration auto-hébergés.
- **Recommandation** : Configurez le flux de données pour qu’il s’exécute sur un runtime d’intégration de réseau virtuel géré.

### <a name="error-code-df-xml-invalidvalidationmode"></a>Code d’erreur : DF-XML-InvalidValidationMode
- **Message** : le mode de validation XML fourni n’est pas valide.
- **Recommandation** : vérifiez la valeur du paramètre et indiquez le mode de validation approprié.

### <a name="error-code-df-xml-invaliddatafield"></a>Code d’erreur : DF-XML-InvalidDataField
- **Message** : le champ pour les enregistrements endommagés doit être de type chaîne et pouvant accepter la valeur Null.
- **Recommandation** : assurez-vous que la colonne `\"_corrupt_record\"` du projet source a un type de données de chaîne.

### <a name="error-code-df-xml-malformedfile"></a>Code d’erreur : DF-XML-MalformedFile
- **Message** : code XML incorrect dans ’FailFastMode'.
- **Recommandation** : mettez à jour le contenu du fichier XML au format approprié.

### <a name="error-code-df-xml-invaliddatatype"></a>Code d’erreur : DF-Xml-InvalidDataType
- **Message** : l’élément XML a des sous-éléments ou des attributs et ne peut pas être converti.

### <a name="error-code-df-xml-invalidreferenceresource"></a>Code d’erreur : DF-XML-InvalidReferenceResource
- **Message** : la ressource de référence dans le fichier de données XML ne peut pas être résolue.
- **Recommandation** : vous devez vérifier la ressource de référence dans le fichier de données XML.

### <a name="error-code-df-xml-invalidschema"></a>Code d’erreur : DF-Xml-InvalidSchema
- **Message :** La validation du schéma a échoué.

### <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>Code d’erreur : DF-XML-UnsupportedExternalReferenceResource
- **Message** : la ressource de référence externe dans le fichier de données XML n’est pas prise en charge.
- **Recommandation** : mettez à jour le contenu du fichier XML lorsque la ressource de référence externe n’est pas prise en charge.

### <a name="error-code-df-gen2-invalidaccountconfiguration"></a>Code d’erreur : DF-GEN2-InvalidAccountConfiguration
- **Message** : vous devez spécifier l’une des clés de compte ou client/SpnId/SpnCredential/SpnCredentialType ou MiServiceUri/miServiceToken.
- **Recommandation** : configurez le compte approprié dans le service lié GEN2 associé.

### <a name="error-code-df-gen2-invalidauthconfiguration"></a>Code d’erreur : DF-GEN2-InvalidAuthConfiguration
- **Message** : seule une des trois méthodes d’authentification (Clé, ServicePrincipal et MI) peut être utilisée. 
- **Recommandation** : choisissez le type d’authentification approprié dans le service lié GEN2 associé.

### <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>Code d’erreur : DF-GEN2-InvalidServicePrincipalCredentialType
- **Message** : ServicePrincipalCredentialType n’est pas valide.

### <a name="error-code-df-gen2-invaliddatatype"></a>Code d’erreur : DF-GEN2-InvalidDataType
- **Message** : le type de Cloud n’est pas valide.

### <a name="error-code-df-blob-invalidaccountconfiguration"></a>Code d’erreur : DF-Blob-InvalidAccountConfiguration
- **Message** : vous devez spécifier une clé de compte ou sas_token.

### <a name="error-code-df-blob-invalidauthconfiguration"></a>Code d’erreur : DF-Blob-InvalidAuthConfiguration
- **Message** : vous pouvez spécifier une seule des deux méthodes d’authentification (Clé, SAS).

### <a name="error-code-df-blob-invaliddatatype"></a>Code d’erreur : DF-BLOB-InvalidDataType
- **Message** : le type de Cloud n’est pas valide.

### <a name="error-code-df-cosmos-partitionkeymissed"></a>Code d’erreur : DF-Cosmos-PartitionKeyMissed
- **Message** : le chemin d’accès de la clé de partition doit être indiqué pour les opérations de mise à jour et de suppression.
- **Recommandation** : utilisez la clé de partition fournissant les paramètres du récepteur Cosmos.

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Code d’erreur : DF-Cosmos-InvalidPartitionKey
- **Message** : le chemin de la clé de partition ne peut pas être vide pour les opérations de mise à jour et de suppression.
- **Recommandation** : utilisez la clé de partition fournissant les paramètres du récepteur Cosmos.

### <a name="error-code-df-cosmos-idpropertymissed"></a>Code d’erreur : DF-Cosmos-IdPropertyMissed
- **Message** : la propriété « ID » doit être mappée pour les opérations de suppression et de mise à jour.
- **Recommandation** : Assurez-vous que les données d’entrée ont une colonne `id` dans les paramètres du récepteur Cosmos. Si ce n’est pas le cas, utilisez **sélectionner ou déduire la transformation** pour générer cette colonne avant le récepteur.

### <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>Code d’erreur : DF-Cosmos-InvalidPartitionKeyContent
- **Message** : la clé de partition doit commencer par /.
- **Recommandation** : faites commencer la clé de partition avec `/` dans les paramètres du récepteur Cosmos, par exemple : `/movieId`.

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Code d’erreur : DF-Cosmos-InvalidPartitionKey
- **Message** : partitionKey n’est pas mappé dans le récepteur pour les opérations de suppression et de mise à jour.
- **Recommandation** : dans les paramètres de récepteur Cosmos, utilisez la clé de partition qui est identique à la clé de partition de votre conteneur.

### <a name="error-code-df-cosmos-invalidconnectionmode"></a>Code d’erreur : DF-Cosmos-InvalidConnectionMode
- **Message** : connectionMode non valide.
- **Recommandation** : Vérifiez que le mode pris en charge est **Gateway** et **DirectHttps** dans les paramètres Cosmos.

### <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>Code d’erreur : DF-Cosmos-InvalidAccountConfiguration
- **Message** : Les éléments AccountName ou accountEndpoint doivent être spécifiés.

### <a name="error-code-df-github-writenotsupported"></a>Code d’erreur : DF-GitHub-WriteNotSupported
- **Message** : le stockage GitHub n’autorise pas les écritures.

### <a name="error-code-df-pgsql-invalidcredential"></a>Code d’erreur : DF-PGSQL-InvalidCredential
- **Message** : L’utilisateur et le mot de passe doivent être spécifiés.
- **Recommandation** : assurez-vous de disposer des paramètres d’informations d’identification appropriés dans le service lié PostgreSQL associé.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Code d’erreur : DF-Snowflake-InvalidStageConfiguration
- **Message** : seul le type de stockage BLOB peut être utilisé en tant qu’étape de lecture/écriture en flocon.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Code d’erreur : DF-Snowflake-InvalidStageConfiguration
- **Message** : les propriétés de l’index en flocon doivent être spécifiées avec l’authentification BLOB Azure + SAS.

### <a name="error-code-df-snowflake-invaliddatatype"></a>Code d’erreur : DF-Snowflake-InvalidDataType
- **Message** : le type Spark n’est pas pris en charge dans le flocon.
- **Recommandation** : utilisez **déduire la transformation** pour modifier la colonne de données d’entrée associée dans le type de chaîne avant le récepteur en flocon. 

### <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>Code d’erreur : DF-Hive-InvalidBlobStagingConfiguration
- **Message** : les propriétés de mise en lots du stockage d’objets BLOB doivent être spécifiées.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Code d’erreur : DF-Hive-InvalidGen2StagingConfiguration
- **Message** : la mise en lots du stockage ADLS Gen2 ne prend en charge que les informations d’identification de la clé principale du service.
- **Recommandation** : confirmez que vous appliquez les informations d’identification de la clé de principal du service dans le service lié ADLS Gen2 utilisé comme mise en lots.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Code d’erreur : DF-Hive-InvalidGen2StagingConfiguration
- **Message** : les propriétés de mise en lots du stockage d’objets ADLS Gen2 doivent être spécifiées. L’une des clés tenant/spnId/spnKey ou miServiceUri/miServiceToken est requise.
- **Recommandation** : appliquez les informations d’identification appropriées utilisées comme mise en lots dans la ruche dans le service lié ADLS Gen2 associé. 

### <a name="error-code-df-hive-invaliddatatype"></a>Code d’erreur : DF-Hive-InvalidDataType
- **Message** : colonne(s) non prise(s) en charge.
- **Recommandation** : mettez à jour la colonne des données d’entrée pour qu’elles correspondent au type de données pris en charge par la ruche.

### <a name="error-code-df-hive-invalidstoragetype"></a>Code d’erreur : DF-Hive-InvalidStorageType
- **Message** : le type de stockage peut être un objet BLOB ou un Gen2.

### <a name="error-code-df-delimited-invalidconfiguration"></a>Code d’erreur : DF-Delimited-InvalidConfiguration
- **Message** : vous devez spécifier l’une des lignes vides ou un en-tête personnalisé.
- **Recommandation** : indiquez des lignes vides ou des en-têtes personnalisés dans les paramètres CSV.

### <a name="error-code-df-delimited-columndelimitermissed"></a>Code d’erreur : DF-Delimited-ColumnDelimiterMissed
- **Message** : le délimiteur de colonne est requis pour l’analyse.
- **Recommandation** : vérifiez que vous disposez du délimiteur de colonne dans vos paramètres CSV.

### <a name="error-code-df-mssql-invalidcredential"></a>Code d’erreur : DF-MSSQL-InvalidCredential
- **Message**: l’un des uuser/pwd ou tenant/SpnId/SpnKey ou MiServiceUri/miServiceToken doit être spécifié.
- **Recommandation** : appliquez les informations d’identification appropriées dans le service lié MSSQL associé.

### <a name="error-code-df-mssql-invaliddatatype"></a>Code d’erreur : DF-MSSQL-InvalidDataType
- **Message** : champ(s) non pris en charge.
- **Recommandation** : modifiez la colonne de données d’entrée pour qu’elle corresponde au type de données pris en charge par MSSQL.

### <a name="error-code-df-mssql-invalidauthconfiguration"></a>Code d’erreur : DF-MSSQL-InvalidAuthConfiguration
- **Message** : seule une des trois méthodes d’authentification (Clé, ServicePrincipal et MI) peut être utilisée.
- **Recommandation** : vous ne pouvez indiquer qu’une des trois méthodes d’authentification (Key, ServicePrincipal et MI) dans le service lié MSSQL associé.

### <a name="error-code-df-mssql-invalidcloudtype"></a>Code d’erreur : DF-MSSQL-InvalidCloudType
- **Message** : le type de Cloud n’est pas valide.
- **Recommandation** : Vérifiez votre type de Cloud dans le service lié MSSQL associé.

### <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>Code d’erreur : DF-SQLDW-InvalidBlobStagingConfiguration
- **Message** : les propriétés de mise en lots du stockage d’objets BLOB doivent être spécifiées.

### <a name="error-code-df-sqldw-invalidstoragetype"></a>Code d’erreur : DF-SQLDW-InvalidStorageType
- **Message** : le type de stockage peut être un objet BLOB ou un Gen2.

### <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>Code d’erreur : DF-SQLDW-InvalidGen2StagingConfiguration
- **Message** : la mise en lots du stockage ADLS Gen2 ne prend en charge que les informations d’identification de la clé principale du service.

### <a name="error-code-df-sqldw-invalidconfiguration"></a>Code d’erreur : DF-SQLDW-InvalidConfiguration
- **Message** : les propriétés de mise en lots du stockage d’objets ADLS Gen2 doivent être spécifiées. L’une des clés ou tenant/spnId/spnCredential/spnCredentialType ou miServiceUri/miServiceToken est requise.

### <a name="error-code-df-delta-invalidconfiguration"></a>Code d’erreur : DF-DELTA-InvalidConfiguration
- **Message** : le timestamp et la version ne peuvent pas être définis en même temps.

### <a name="error-code-df-delta-keycolumnmissed"></a>Code d’erreur : DF-DELTA-KeyColumnMissed
- **Message** : la ou les colonnes clés doivent être spécifiées pour les opérations qui ne peuvent pas être insérées.

### <a name="error-code-df-delta-invalidtableoperationsettings"></a>Code d’erreur : DF-DELTA-InvalidTableOperationSettings
- **Message** : les options Recréer et Tronquer ne peuvent pas être indiquées en même temps.

### <a name="error-code-df-excel-worksheetconfigmissed"></a>Code d’erreur : DF-Excel-WorksheetConfigMissed
- **Message** : Le nom ou l’index de la feuille de calcul Excel est requis.
- **Recommandation** : vérifiez la valeur du paramètre et spécifiez le nom ou l’index de la feuille pour lire les données Excel.

### <a name="error-code-df-excel-invalidworksheetconfiguration"></a>Code d’erreur : DF-Excel-InvalidWorksheetConfiguration
- **Message** : Le nom et l’index d’une feuille Excel ne peuvent pas exister en même temps.
- **Recommandation** : vérifiez la valeur du paramètre et spécifiez le nom ou l’index de la feuille pour lire les données Excel.

### <a name="error-code-df-excel-invalidrange"></a>Code d’erreur : DF-Excel-InvalidRange
- **Message** : Une plage non valide est fournie.
- **Recommandation** : vérifiez la valeur du paramètre et indiquez la plage valide à l’aide de la référence suivante : [format Excel dans les propriétés d’Azure Data Factory-Dataset](./format-excel.md#dataset-properties).

### <a name="error-code-df-excel-worksheetnotexist"></a>Code d’erreur : DF-Excel-WorksheetNotExist
- **Message** : La feuille de calcul Excel n’existe pas.
- **Recommandation** : vérifiez la valeur du paramètre et spécifiez le nom ou l’index valide de la feuille pour lire les données Excel.

### <a name="error-code-df-excel-differentschemanotsupport"></a>Code d’erreur : DF-Excel-DifferentSchemaNotSupport
- **Message** : la lecture des fichiers Excel avec un schéma différent n’est pas prise en charge pour le moment.

### <a name="error-code-df-excel-invaliddatatype"></a>Code d’erreur : DF-Excel-InvalidDataType
- **Message** : Type de données non pris en charge.

### <a name="error-code-df-excel-invalidfile"></a>Code d’erreur : DF-Excel-InvalidFile
- **Message** : un fichier Excel non valide est fourni alors que seuls les fichiers .xlsx et .xls sont pris en charge.


## <a name="miscellaneous-troubleshooting-tips"></a>Conseils pour la résolution de problèmes divers
- **Problème** : Une exception inattendue s’est produite et l’exécution a échoué.
    - **Message** : Lors de l’exécution de l’activité de flux de données : Exception inattendue et échec de l’exécution.
    - **Cause** : Cette erreur est une erreur de service back-end. Retentez l’opération et redémarrez votre session de débogage.
    - **Recommandation** : Si la nouvelle tentative et le redémarrage ne résolvent pas le problème, contactez le support technique.

-  **Problème** : Aucune donnée de sortie lors de la jointure pendant l’aperçu des données de débogage.
    - **Message** : Il existe un nombre élevé de valeurs Null ou manquantes qui peuvent être dues à l’échantillonnage d’un trop petit nombre de lignes. Essayez de mettre à jour la limite de lignes de débogage et d’actualiser les données.
    - **Cause** : La condition de jointure ne correspond à aucune ligne ou a généré un nombre élevé de valeurs Null au cours de l’aperçu des données.
    - **Recommandation** : Dans **Paramètres de débogage**, augmentez le nombre de lignes dans la limite de lignes sources. Veillez à sélectionner un runtime d’intégration Azure disposant d’un cluster de flux de données suffisamment grand pour gérer davantage de données.
    
- **Problème** : Erreur de validation à la source avec des fichiers CSV multilignes. 
    - **Message** : Vous pourriez voir l’un des messages d’erreur suivants :
        - La dernière colonne est null ou manquante.
        - Échec de la validation de schéma à la source.
        - L’importation de schéma ne s’affiche pas correctement dans l’interface utilisateur et la dernière colonne contient un caractère de nouvelle ligne dans le nom.
    - **Cause** : Dans le flux de données de mappage, les fichiers sources CSV multilignes ne fonctionnent pas quand \r\n est utilisé comme séparateur de lignes. Parfois, des lignes supplémentaires au niveau des retours chariot peuvent provoquer des erreurs. 
    - **Recommandation** : Générez le fichier à la source avec \n comme séparateur de lignes plutôt que \r\n. Vous pouvez aussi utiliser l’activité de copie pour convertir le fichier CSV afin d’utiliser \n comme séparateur de lignes.

## <a name="general-troubleshooting-guidance"></a>Instructions générales pour la résolution des problèmes
1. Vérifiez l’état de vos connexions de jeu de données. Dans chaque transformation de la source et du récepteur, accédez au service lié pour chaque jeu de données que vous utilisez et testez les connexions.
2. Vérifiez l’état de vos connexions aux fichiers et aux tables dans le concepteur de flux de données. En mode de débogage, sélectionnez **Aperçu des données** dans vos transformations sources pour vérifier que vous pouvez accéder à vos données.
3. Si tout semble correct dans l’aperçu des données, accédez au concepteur de pipeline et mettez votre flux de données dans une activité de pipeline. Déboguez le pipeline pour un test de bout en bout.

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>Amélioration du format CSV/CDM dans le Data Flow 

Si vous utilisez le **format Texte délimité ou CDM pour le flux de données de mappage dans Azure Data Factory v2**, vous pourrez être confronté à des changements de comportement de vos pipelines existants en raison de l’amélioration du format Texte délimité/CDM dans le flux de données à partir du **1er mai 2021**. 

Vous pouvez rencontrer les problèmes suivants avant l’amélioration, mais après l’amélioration, les problèmes seront corrigés. Lisez le contenu suivant pour déterminer si cette amélioration vous concerne. 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>Scénario 1 : Vous rencontrez un problème de délimiteur de ligne inattendu

 Vous êtes concerné dans les conditions suivantes :
 - Vous utilisez le format Texte délimité avec le paramètre Multiline défini sur True ou CDM comme source.
 - La première ligne contient plus de 128 caractères. 
 - Le délimiteur de ligne dans les fichiers de données n’est pas `\n`.

 Avant l’amélioration, le délimiteur de ligne par défaut `\n` peut être utilisé de manière inattendue pour analyser les fichiers texte délimité, car, lorsque le paramètre Multiline est défini sur True, il invalide le paramètre de délimiteur de ligne et le délimiteur de ligne est automatiquement détecté en fonction des 128 premiers caractères. Si vous ne parvenez pas à détecter le délimiteur de ligne réel, le système revient à `\n`.  

 Après l’amélioration, l’un des trois délimiteurs de ligne, `\r`, `\n` ou `\r\n`, devrait fonctionner.
 
 L’exemple suivant montre un changement de comportement du pipeline après l’amélioration :

 **Exemple** :<br/>
   Pour la colonne suivante :<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   Avant l’amélioration, `\r` est conservé dans la valeur de la colonne. Le résultat de la colonne analysée est le suivant :<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   Après l’amélioration, le résultat de la colonne analysée doit être :<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>Scénario 2 : Vous rencontrez un problème de lecture incorrecte des valeurs de colonne contenant « \r\n »

 Vous êtes concerné dans les conditions suivantes :
 - Vous utilisez le format Texte délimité avec le paramètre Multiline défini sur True ou CDM comme source. 
 - Le délimiteur de ligne est `\r\n`.

 Avant l’amélioration, lors de la lecture de la valeur de colonne, le `\r\n` de celle-ci peut être incorrectement remplacé par `\n`. 

 Après l’amélioration, `\r\n` dans la valeur de colonne ne sera pas remplacé par `\n`.

 L’exemple suivant montre un changement de comportement du pipeline après l’amélioration :
 
 **Exemple** :<br/>
  
 Pour la colonne suivante :<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 Avant l’amélioration, le résultat de la colonne analysée est le suivant :<br/>
  **`A\n`**` B C`<br/>

 Après l’amélioration, le résultat de la colonne analysée doit être :<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>Scénario 3 : Vous rencontrez un problème d’écriture incorrecte des valeurs de colonne contenant « \r\n »

 Vous êtes concerné dans les conditions suivantes :
 - Vous utilisez le format Texte délimité comme récepteur.
 - La valeur de colonne contient `\n`.
 - L’option Séparateur de lignes est définie sur `\r\n`.
 
 Avant l’amélioration, lors de l’écriture de la valeur de colonne, le `\n` de celle-ci peut être incorrectement remplacé par `\r\n`. 

 Après l’amélioration, `\n` dans la valeur de colonne ne sera pas remplacé par `\r\n`.
 
 L’exemple suivant montre un changement de comportement du pipeline après l’amélioration :

 **Exemple** :<br/>

 Pour la colonne suivante :<br/>
 **`A\n`**` B C`<br/>

 Avant l’amélioration, le récepteur CSV est :<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 Après l’amélioration, le récepteur CSV doit être :<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>Scénario 4 : Vous rencontrez un problème de lecture incorrecte d’une chaîne vide comme NULL
 
 Vous êtes concerné dans les conditions suivantes :
 - Vous utilisez le format Texte délimité comme source. 
 - La valeur NULL est définie comme une valeur non vide. 
 - La valeur de colonne est une chaîne vide et est sans guillemets. 
 
 Avant l’amélioration, la valeur de colonne de la chaîne vide sans guillemets est lue comme NULL. 

 Après l’amélioration, la chaîne vide ne sera pas interprétée comme une valeur NULL. 
 
 L’exemple suivant montre un changement de comportement du pipeline après l’amélioration :

 **Exemple** :<br/>

 Pour la colonne suivante :<br/>
  `A, ,B, `<br/>

 Avant l’amélioration, le résultat de la colonne analysée est le suivant :<br/>
  `A null B null`<br/>

 Après l’amélioration, le résultat de la colonne analysée doit être :<br/>
  `A "" (empty string) B "" (empty string)`<br/>


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une aide supplémentaire sur la résolution des problèmes, consultez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
