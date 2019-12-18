---
title: Résoudre les problèmes rencontrés dans Azure Data Factory | Microsoft Docs
description: Découvrez comment résoudre les problèmes liés aux activités de contrôle externes dans Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 2ae0f3033b88b3229d3dbef35c8bc9a32510c00e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972334"
---
# <a name="troubleshoot-azure-data-factory"></a>Résoudre les problèmes dans Azure Data Factory

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés aux activités de contrôle externes dans Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Activité de copie et connecteur

Pour des problèmes de connecteur, tels qu’une erreur lors de l’utilisation d’une activité de copie, consultez [Résoudre les problèmes liés aux connecteurs dans Azure Data Factory](connector-troubleshoot-guide.md).
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Code d’erreur :  3200

- **Message** : Erreur 403.

- **Cause** : `The Databricks access token has expired.`

- **Recommandation** : Par défaut, le jeton d’accès Azure Databricks est valide pendant 90 jours. Créez un autre jeton et mettez à jour le service lié.


### <a name="error-code--3201"></a>Code d’erreur :  3201

- **Message** : `Missing required field: settings.task.notebook_task.notebook_path.`

- **Cause** : `Bad authoring: Notebook path not specified correctly.`

- **Recommandation** : Spécifiez le chemin du notebook dans l’activité Databricks.

<br/>  

- **Message** : `Cluster... does not exist.`

- **Cause** : `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Recommandation** : Vérifiez que le cluster Databricks existe.

<br/>  

- **Message** : `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Cause** : `Bad authoring.`

- **Recommandation** : Spécifiez les chemins absolus des schémas d’adressage d’espace de travail, ou `dbfs:/folder/subfolder/foo.py` pour les fichiers stockés dans Databricks File System.

<br/>  

- **Message** : `{0} LinkedService should have domain and accessToken as required properties.`

- **Cause** : `Bad authoring.`

- **Recommandation** : Vérifiez la [définition du service lié](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Message** : `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Cause** :  `Bad authoring.`

- **Recommandation** : Vérifiez la [définition du service lié](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Message** : `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Cause** : `Bad authoring.`

- **Recommandation** : Voir le message d’erreur.

<br/>

### <a name="error-code--3202"></a>Code d’erreur :  3202

- **Message** : `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Cause** : `Too many Databricks runs in an hour.`

- **Recommandation** : Vérifiez le taux de création de travaux dans tous les pipelines qui utilisent cet espace de travail Databricks.  Si les pipelines ont lancé trop de travaux Databricks dans un agrégat, migrez des pipelines dans un nouvel espace de travail.

<br/>  

- **Message** : `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Cause** : `Authoring error: No value provided for the parameter.`

- **Recommandation** : Inspectez le code JSON de pipeline et vérifiez que tous les paramètres du notebook baseParameters spécifient une valeur non vide.

<br/>  

- **Message** : `User: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is not   authorized to access cluster.`

- **Cause** : L’utilisateur qui a généré le jeton d’accès n’est pas autorisé à accéder au cluster Databricks spécifié dans le service lié.

- **Recommandation** : Vérifiez que l’utilisateur dispose des autorisations nécessaires dans l’espace de travail.


### <a name="error-code--3203"></a>Code d’erreur :  3203

- **Message** : `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Cause** : Le cluster a été arrêté. Pour les clusters interactifs, il peut s’agir d’une condition de concurrence.

- **Recommandation** : La meilleure façon d’éviter ce problème consiste à utiliser des clusters de travaux.


### <a name="error-code--3204"></a>Code d’erreur :  3204

- **Message** : `Job execution failed.`

- **Cause** :  Les messages d’erreur indiquent différents problèmes, tels qu’un état de cluster inattendu ou une activité inattendue. Le plus souvent, aucun message d’erreur ne s’affiche.

- **Recommandation** : N/A
            

## <a name="azure-data-lake-analytics"></a>Service Analytique Azure Data Lake

Le tableau suivant s’applique à U-SQL.
      
### <a name="error-code--2709"></a>Code d’erreur :  2709

- **Message** : `The access token is from the wrong tenant.`

- **Cause** :  Mauvais locataire Azure Active Directory (Azure AD).

- **Recommandation** : Mauvais locataire Azure Active Directory (Azure AD).

<br/>

- **Message** : `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Cause** :  Cette erreur est provoquée par une limitation dans Data Lake Analytics.

- **Recommandation** : Réduisez le nombre de travaux envoyés à Data Lake Analytics en modifiant les déclencheurs Data Factory et les paramètres de concurrence des activités. Vous pouvez également augmenter les limites dans Data Lake Analytics.

<br/>  

- **Message** : `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Cause** :  Cette erreur est provoquée par une limitation dans Data Lake Analytics.

- **Recommandation** : Réduisez le nombre de travaux envoyés à Data Lake Analytics en modifiant les déclencheurs Data Factory et les paramètres de concurrence des activités. Vous pouvez également augmenter les limites dans Data Lake Analytics.


### <a name="error-code--2705"></a>Code d’erreur :  2705

- **Message** : `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Cause** :  Le principal de service ou le certificat fourni n’a pas accès au fichier situé dans le stockage.

- **Recommandation** : Vérifiez que le principal de service ou le certificat fourni par l’utilisateur pour les travaux Data Lake Analytics a accès au compte Data Lake Analytics et à l’instance Data Lake Storage par défaut à partir du dossier racine.


### <a name="error-code--2711"></a>Code d’erreur :  2711

- **Message** : `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Cause** :  Le principal de service ou le certificat fourni n’a pas accès au fichier situé dans le stockage.

- **Recommandation** : Vérifiez que le principal de service ou le certificat fourni par l’utilisateur pour les travaux Data Lake Analytics a accès au compte Data Lake Analytics et à l’instance Data Lake Storage par défaut à partir du dossier racine.

<br/>  

- **Message** : `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Cause** :  Le chemin du fichier U-SQL est incorrect ou les informations d’identification du service lié n’y ont pas accès.

- **Recommandation** : Vérifiez le chemin et les informations d’identification fournies dans le service lié.


### <a name="error-code--2704"></a>Code d’erreur :  2704

- **Message** : `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Cause** :  Le principal de service ou le certificat fourni n’a pas accès au fichier situé dans le stockage.

- **Recommandation** : Vérifiez que le principal de service ou le certificat fourni par l’utilisateur pour les travaux Data Lake Analytics a accès au compte Data Lake Analytics et à l’instance Data Lake Storage par défaut à partir du dossier racine.


### <a name="error-code--2707"></a>Code d’erreur :  2707

- **Message** : `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Cause** :  Le compte Data Lake Analytics du service lié est incorrect.

- **Recommandation** : Vérifiez que le bon compte a été fourni.


### <a name="error-code--2703"></a>Code d’erreur :  2703

- **Message** : `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Cause** :  L’erreur provient de Data Lake Analytics.

- **Recommandation** : Une erreur comme celle de l’exemple signifie que le travail a été envoyé à Data Lake Analytics et que le script a échoué. Examinez le problème dans Data Lake Analytics. Dans le portail, accédez au compte Data Lake Analytics et recherchez le travail à l’aide de l’ID d’exécution de l’activité Data Factory (et non de l’ID d’exécution du pipeline). Le travail fournit davantage d’informations sur l’erreur qui vous permettront de la résoudre. Si vous n’êtes pas sûr d’avoir résolu votre problème, contactez l’équipe du support technique Data Lake Analytics pour lui donner l’URL du travail, qui comprend le nom de votre compte et l’ID du travail.
          

## <a name="azure-functions"></a>Fonctions Azure

### <a name="error-code--3602"></a>Code d’erreur :  3602

- **Message** : `Invalid HttpMethod: '%method;'.`

- **Cause** : La méthode HTTP spécifiée dans la charge utile d’activité n’est pas prise en charge par l’activité de fonction Azure.

- **Recommandation** :  Les méthodes HTTP prises en charge sont PUT, POST, GET, DELETE, OPTIONS, HEAD et TRACE.


### <a name="error-code--3603"></a>Code d’erreur :  3603

- **Message** : `Response Content is not a valid JObject.`

- **Cause** : La fonction Azure qui a été appelée n’a pas retourné de charge utile JSON dans la réponse. L’activité de fonction ADF Azure prend uniquement en charge le contenu de la réponse JSON.

- **Recommandation** :  Mettez à jour de la fonction Azure pour retourner une charge utile JSON valide ; par exemple, une fonction C# peut retourner (ActionResult)new OkObjectResult("{\"Id\":\"123\"}");


### <a name="error-code--3606"></a>Code d’erreur :  3606

- **Message** : Clé de fonction manquante dans l’activité de fonction Azure.

- **Cause** : La définition de l’activité de fonction Azure n’est pas terminée.

- **Recommandation** :  Vérifiez que la définition JSON de l’activité AzureFunction dispose d'une propriété nommée « functionKey ».


### <a name="error-code--3607"></a>Code d’erreur :  3607

- **Message** : `Azure function activity missing function name.`

- **Cause** : La définition de l’activité de fonction Azure n’est pas terminée.

- **Recommandation** :  Vérifiez que la définition JSON de l’activité AzureFunction dispose d'une propriété nommée « functionName ».


### <a name="error-code--3608"></a>Code d’erreur :  3608

- **Message** : `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Cause** : Les détails de la fonction Azure dans la définition de l’activité sont peut-être incorrects.

- **Recommandation** :  Corrigez les détails de la fonction Azure, puis réessayez.


### <a name="error-code--3609"></a>Code d’erreur :  3609

- **Message** : `Azure function activity missing functionAppUrl.`

- **Cause** : La définition de l’activité de fonction Azure n’est pas terminée.

- **Recommandation** :  Vérifiez que la définition JSON de l’activité AzureFunction dispose d'une propriété nommée « functionAppUrl ».


### <a name="error-code--3610"></a>Code d’erreur :  3610

- **Message** : `There was an error while calling endpoint.`

- **Cause** : L’URL de la fonction est peut-être incorrecte.

- **Recommandation** :  Vérifiez que la valeur de « functionAppUrl » dans le JSON de l’activité est correcte, puis réessayez.


### <a name="error-code--3611"></a>Code d’erreur :  3611

- **Message** : `Azure function activity missing Method in JSON.`

- **Cause** : La définition de l’activité de fonction Azure n’est pas terminée.

- **Recommandation** :  Vérifiez que la définition JSON de l’activité AzureFunction d’entrée dispose d’une propriété nommée « method ».


### <a name="error-code--3612"></a>Code d’erreur :  3612

- **Message** : `Azure function activity missing LinkedService definition in JSON.`

- **Cause** : La définition de l’activité de fonction Azure n’est pas terminée.

- **Recommandation** :  Vérifiez que la définition JSON de l’activité AzureFunction dispose des détails sur le service lié.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Code d’erreur :  4101

- **Message** : `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Cause** : Format incorrect ou définition manquante de la propriété « %propertyName; ».

- **Recommandation** :  Vérifiez si la propriété « %propertyName; » est définie avec les données correctes pour l’activité « %activityName; ».


### <a name="error-code--4110"></a>Code d’erreur :  4110

- **Message** : `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Cause** : La définition de l’activité AzureMLExecutePipeline n’est pas terminée.

- **Recommandation** :  Vérifiez si la définition JSON de l’activité AzureMLExecutePipeline dispose des détails sur le service lié.


### <a name="error-code--4111"></a>Code d’erreur :  4111

- **Message** : `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Cause** : Définition d'activité incorrecte.

- **Recommandation** :  Vérifiez si la définition JSON de l’activité AzureMLExecutePipeline dispose des détails corrects sur le service lié.


### <a name="error-code--4112"></a>Code d’erreur :  4112

- **Message** : `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Cause** : Format incorrect ou définition manquante de la propriété « %propertyName; ».

- **Recommandation** :  Vérifiez si la propriété « %propertyName; » est définie avec les données correctes pour le service lié.


### <a name="error-code--4121"></a>Code d’erreur :  4121

- **Message** : `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Cause** : Les informations d’identification utilisées pour accéder à Azure Machine Learning ont expiré.

- **Recommandation** :  Vérifiez si les informations d’identification sont valides et réessayez.


### <a name="error-code--4122"></a>Code d’erreur :  4122

- **Message** : `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Cause** : Les informations d’identification fournies dans le service lié du service Azure Machine Learning ne sont pas valides ni autorisées pour l’opération.

- **Recommandation** :  Vérifiez si les informations d’identification du service lié sont valides et autorisées pour accéder au service Azure Machine Learning.


### <a name="error-code--4123"></a>Code d’erreur :  4123

- **Message** : `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Cause** : Les propriétés de l’activité, comme pipelineParameters, ne sont pas valides pour le pipeline Azure ML.

- **Recommandation** :  Vérifiez si la valeur des propriétés de l’activité correspond à la charge utile attendue du pipeline Azure ML publié spécifié dans le service lié.


### <a name="error-code--4124"></a>Code d’erreur :  4124

- **Message** : `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Cause** : Le point de terminaison de pipeline Azure ML publié n’existe pas.

- **Recommandation** :  Vérifiez que le point de terminaison du pipeline Azure Machine Learning publié et spécifié dans le service lié existe dans Azure Machine Learning.


### <a name="error-code--4125"></a>Code d’erreur :  4125

- **Message** : `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Cause** : Erreur de serveur sur Azure Machine Learning.

- **Recommandation** :  Veuillez réessayer ultérieurement. Pour obtenir de l’aide, contactez l’équipe Azure Machine Learning si le problème persiste.


### <a name="error-code--4126"></a>Code d’erreur :  4126

- **Message** : `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Cause** : L’exécution du pipeline Azure ML a échoué.

- **Recommandation** :  Pour obtenir des journaux d’erreurs supplémentaires et corriger le pipeline ML, consultez Azure Machine Learning.



## <a name="common"></a>Courant

### <a name="error-code--2103"></a>Code d’erreur :  2103

- **Message** : `Please provide value for the required property '%propertyName;'.`

- **Cause** : La valeur de la propriété n’a pas été spécifiée. Elle est cependant requise dans ce scénario.

- **Recommandation** :  Spécifiez la valeur demandée dans le message, puis réessayez.


### <a name="error-code--2104"></a>Code d’erreur :  2104

- **Message** : `The type of the property '%propertyName;' is incorrect.`

- **Cause** : Le type de la propriété spécifiée ne correspond pas au type attendu.

- **Recommandation** :  Corrigez le type de la propriété et réessayez.


### <a name="error-code--2105"></a>Code d’erreur :  2105

- **Message** : `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Cause** : La valeur de la propriété n’est pas valide ou n’est pas au format attendu.

- **Recommandation** :  Consultez la documentation relative à la propriété et assurez-vous que la valeur spécifiée présente le format et le type attendus.


### <a name="error-code--2106"></a>Code d’erreur :  2106

- **Message** : `The storage connection string is invalid. %errorMessage;`

- **Cause** : La chaîne de connexion pour le stockage n’est pas valide ou son format est incorrect.

- **Recommandation** :  Accédez au portail Azure, recherchez votre stockage, copiez la chaîne de connexion, collez-la dans votre service lié, puis réessayez.


### <a name="error-code--2108"></a>Code d’erreur :  2108

- **Message** : `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Cause** : La requête a échoué en raison d’un problème sous-jacent, par exemple un problème de connectivité réseau, un échec de DNS, un problème de validation de certificat de serveur ou l’expiration d’un délai.

- **Recommandation** :  Utilisez Fiddler/Postman pour valider la requête.


### <a name="error-code--2110"></a>Code d’erreur :  2110

- **Message** : `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Cause** : Le service lié spécifié dans l’activité est incorrect.

- **Recommandation** :  Assurez-vous que le type de service lié fait partie des types pris en charge pour l’activité. Par exemple, pour les activités HDI, le type de service lié peut être HDInsight ou HDInsightOnDemand.


### <a name="error-code--2111"></a>Code d’erreur :  2111

- **Message** : `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Cause** : Le type de la propriété spécifiée ne correspond pas au type attendu.

- **Recommandation** :  Corrigez le type de la propriété et réessayez.


### <a name="error-code--2112"></a>Code d’erreur :  2 112

- **Message** : `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Cause** : Le type de cloud n’est pas pris en charge ou n’a pas pu être déterminé pour le stockage à partir du EndpointSuffix.

- **Recommandation** :  Utilisez un stockage dans un autre cloud et réessayez.


### <a name="error-code--2128"></a>Code d’erreur :  2128

- **Message** : `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Cause** : Connectivité réseau, échec de DNS, validation de certificat de serveur ou délai d’expiration.

- **Recommandation** :  Vérifiez que le point de terminaison que vous essayez d’atteindre répond aux requêtes. Vous pouvez utiliser des outils tels que Fiddler/Postman.



## <a name="custom"></a>Personnalisée

Le tableau suivant s’applique à Azure Batch.
      
### <a name="error-code--2500"></a>Code d’erreur :  2 500

- **Message** : `Hit unexpected exception and execution failed.`

- **Cause** : `Can't launch command, or the program returned an error code.`

- **Recommandation** :  Vérifiez que le fichier exécutable existe. Si le programme a démarré, vérifiez que les fichiers *stdout.txt* et *stderr.txt* ont été chargés dans le compte de stockage. Il est conseillé de publier des journaux volumineux dans votre code à des fins de débogage.


### <a name="error-code--2501"></a>Code d’erreur :  2501

- **Message** : `Cannot access user batch account; please check batch account settings.`

- **Cause** : Le nom de pool ou la clé d’accès Batch fournis ne sont pas corrects.

- **Recommandation** : Vérifiez le nom du pool et la clé d’accès Batch dans le service lié.


### <a name="error-code--2502"></a>Code d’erreur :  2502

- **Message** : `Cannot access user storage account; please check storage account settings.`

- **Cause** : Le nom du compte de stockage ou la clé d’accès fournis ne sont pas corrects.

- **Recommandation** : Vérifiez le nom du compte de stockage et la clé d’accès dans le service lié.


### <a name="error-code--2504"></a>Code d’erreur :  2504

- **Message** : `Operation returned an invalid status code 'BadRequest'.`

- **Cause** : Trop de fichiers dans le folderPath de l’activité personnalisée. La taille totale de resourceFiles ne peut pas être supérieure à 32 768 caractères.

- **Recommandation** : Supprimez les fichiers inutiles. Vous pouvez également les placer dans un fichier zip et ajouter une commande de décompression pour les extraire. Par exemple, utilisez `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`.


### <a name="error-code--2505"></a>Code d’erreur :  2505

- **Message** : `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Cause** : Les activités personnalisées prennent uniquement en charge les comptes de stockage qui utilisent une clé d’accès.

- **Recommandation** : Voir la description de l’erreur.


### <a name="error-code--2507"></a>Code d’erreur :  2507

- **Message** : `The folder path does not exist or is empty: ...`

- **Cause** : Aucun fichier ne se trouve dans le compte de stockage situé au chemin spécifié.

- **Recommandation** : Le chemin du dossier doit contenir les fichiers exécutables que vous souhaitez exécuter.


### <a name="error-code--2508"></a>Code d’erreur :  2508

- **Message** : `There are duplicate files in the resource folder.`

- **Cause** : Il existe plusieurs fichiers du même nom dans différents sous-dossiers de folderPath.

- **Recommandation** : Les activités personnalisées aplatissent la structure de dossiers sous folderPath.  Si vous devez conserver la structure de dossiers, compressez les fichiers dans un fichier zip, puis extrayez-les dans Azure Batch à l’aide d’une commande de décompression. Par exemple, utilisez `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`.


### <a name="error-code--2509"></a>Code d’erreur :  2509

- **Message** : `Batch   url ... is invalid; it must be in Uri format.`

- **Cause** : L’URL batch doit être semblable à `https://mybatchaccount.eastus.batch.azure.com`.

- **Recommandation** : Voir la description de l’erreur.


### <a name="error-code--2510"></a>Code d’erreur :  2510

- **Message** : `An   error occurred while sending the request.`

- **Cause** : L’URL Batch n’est pas valide.

- **Recommandation** : Vérifiez l’URL Batch.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Code d’erreur :  200

- **Message** : `Unexpected error happened: '%error;'.`

- **Cause** : Un problème de service interne s’est produit.

- **Recommandation** :  Contactez le support ADF pour obtenir une aide supplémentaire.


### <a name="error-code--201"></a>Code d’erreur :  201

- **Message** : `JobType %jobType; is not found.`

- **Cause** : Un nouveau type de travail n’est pas pris en charge par ADF.

- **Recommandation** :  Contactez l’équipe du support technique ADF pour obtenir une aide supplémentaire.


### <a name="error-code--202"></a>Code d’erreur :  202

- **Message** : `Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Cause** : Le message d’erreur doit fournir des informations détaillées sur la cause du problème.

- **Recommandation** :  Le message d’erreur doit vous aider à résoudre le problème. Si les informations fournies sont insuffisantes, contactez le support ADF pour obtenir une aide supplémentaire.


### <a name="error-code--203"></a>Code d’erreur :  203

- **Message** : `Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Cause** : Le message d’erreur doit fournir des informations détaillées sur la cause du problème.

- **Recommandation** :  Le message d’erreur doit vous aider à résoudre le problème. Si les informations fournies sont insuffisantes, contactez le support ADF pour obtenir une aide supplémentaire.


### <a name="error-code--204"></a>Code d’erreur :  204

- **Message** : `The resumption token is missing for runId '%runId;'.`

- **Cause** : Un problème de service interne s’est produit.

- **Recommandation** :  Contactez le support ADF pour obtenir une aide supplémentaire.


### <a name="error-code--205"></a>Code d’erreur :  205

- **Message** : `Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Cause** : Une erreur s’est produite lors de la création du cluster HDI à la demande.

- **Recommandation** :  Contactez le support ADF pour obtenir une aide supplémentaire.


### <a name="error-code--206"></a>Code d’erreur :  206

- **Message** : `The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Cause** : Un problème interne s’est produit au niveau du service.

- **Recommandation** :  Il peut s’agir d’un problème temporaire. Réessayez le travail. Si le problème persiste, contactez le support ADF pour obtenir une aide supplémentaire.


### <a name="error-code--207"></a>Code d’erreur :  207

- **Message** : `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Cause** : Une erreur interne s’est produite lors de la tentative de détermination de la région à partir du compte de stockage principal.

- **Recommandation** :  Essayez un autre stockage. Si cette solution n’est pas acceptable, contactez l’équipe du support technique ADF pour obtenir une aide supplémentaire.


### <a name="error-code--208"></a>Code d’erreur :  208

- **Message** : `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Cause** : Une erreur interne s’est produite lors de la tentative de lecture du principal de service ou de l’instanciation de l’authentification MSI.

- **Recommandation** :  Spécifiez un principal de service disposant des autorisations nécessaires pour créer un cluster HDInsight dans l’abonnement indiqué, puis réessayez. Si cette solution n’est pas acceptable, contactez l’équipe du support technique ADF pour obtenir une aide supplémentaire.


### <a name="error-code--2300"></a>Code d’erreur :  2 300

- **Message** : `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Cause** : Quand le message d’erreur contient un passage de type « Le nom distant n’a pas pu être résolu. », cela peut indiquer que l’URI de cluster spécifié n’est pas valide.


- **Recommandation** : Vérifiez que le cluster n’a pas été supprimé et que l’URI fourni est correct. Quand vous ouvrez l’URI dans un navigateur, vous devez voir l’interface utilisateur d’Ambari. Si le cluster se trouve dans un réseau virtuel, l’URI doit être l’URI privé. Pour l’ouvrir, utilisez une machine virtuelle qui fait partie du même réseau virtuel. Pour plus d’informations, consultez [cette page](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
                  

<br>

- **Cause** : Quand le message d’erreur contient un passage de type « Une tâche a été annulée. », cela indique que l’envoi du travail a expiré.

- **Recommandation** :  Il peut s’agir d’un problème de connectivité générale HDInsight ou d’un problème de connectivité réseau. Tout d’abord, vérifiez que l’interface utilisateur Ambari HDInsight est accessible à partir de n’importe quel navigateur. Vérifiez que vos informations d’identification sont toujours valides. Si vous utilisez le runtime d’intégration (IR) auto-hébergé, veillez à utiliser la machine virtuelle ou l’ordinateur où celui-ci est installé. Ensuite, renvoyez le travail à partir de Data Factory. Si le problème persiste, contactez l’équipe Data Factory pour obtenir de l’aide.

<br>

- **Cause** : Quand le message d’erreur contient un passage de type « L’administrateur d’utilisateurs est verrouillé dans Ambari » ou « Non autorisé : Le nom d’utilisateur ou le mot de passe Ambari est incorrect », cela indique que les informations d’identification pour HDInsight sont incorrectes ou ont expiré.

- **Recommandation** :  Corrigez les informations d’identification et redéployez le service lié. Vérifiez tout d’abord que les informations d’identification fonctionnent sur HDInsight en ouvrant l’URI de cluster sur n’importe quel navigateur et en essayant de vous connecter. Si elles ne fonctionnent pas, vous pouvez les réinitialiser à partir du portail Azure.

<br>

- **Cause** : Quand le message d’erreur contient un passage de type « 502 - Le serveur web a reçu une réponse erronée lors de son utilisation en tant que passerelle ou serveur proxy », cette erreur est retournée par le service HDInsight.


- **Recommandation** : Consultez la documentation de résolution des problèmes d’Azure HDInsight, par exemple https://hdinsight.github.io/ambari/ambari-ui-502-error.html , https://hdinsight.github.io/spark/spark-thriftserver-errors.html ou https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502.
                  

<br>

- **Cause** : Quand le message d’erreur contient un passage de type « impossible de traiter la demande d’envoi de travail, car le service Templeton est occupé par des demandes d’envoi de travail trop nombreuses » ou « La file d’attente root.joblauncher contient déjà 500 applications. Impossible d’accepter l’envoi de l’application. », cela indique que le nombre de travaux envoyés simultanément à HDInsight est trop élevé.

- **Recommandation** :  Envisagez de limiter le nombre de travaux simultanés envoyés à HDInsight. Renseignez-vous sur la simultanéité des activités Data Factory si les travaux sont envoyés par la même activité. Modifiez les déclencheurs pour que les exécutions de pipeline simultanées soient réparties au fil du temps. Consultez également la documentation HDInsight pour ajuster templeton.parallellism.job.submit, comme le suggère le message d’erreur.


### <a name="error-code--2301"></a>Code d’erreur :  2301

- **Message** : `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Cause** : Le service ou le cluster HDInsight présente des problèmes.


- **Recommandation** : Cette erreur se produit quand ADF tente d’accéder à l’état du travail en cours d’exécution et n’obtient pas de réponse du cluster HDInsight. Ceci peut entraîner des problèmes sur le cluster lui-même. Il se peut également que le service HDInsight soit indisponible. Pour obtenir une aide supplémentaire, reportez-vous à la documentation de résolution des problèmes HDInsight à l’adresse https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide ou contactez le support HDInsight.
                


### <a name="error-code--2302"></a>Code d’erreur :  2302

- **Message** : `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Cause** : Le travail a été envoyé au cluster HDI et a échoué à ce stade.

- **Recommandation** :  Suivez le lien vers les journaux Yarn dans la sortie de l’exécution de l’activité et recherchez les erreurs dans la sortie HDI. Contactez l’équipe HDInsight pour obtenir de l’aide si nécessaire.


### <a name="error-code--2303"></a>Code d’erreur :  2303

- **Message** : `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Cause** : Le travail a été envoyé au cluster HDI et a échoué à ce stade.

- **Recommandation** :  Suivez le lien vers les journaux Yarn dans la sortie de l’exécution de l’activité et recherchez les erreurs dans la sortie HDI. Réessayez ou contactez l’équipe HDInsight pour obtenir de l’aide si nécessaire.


### <a name="error-code--2304"></a>Code d’erreur :  2304

- **Message** : `MSI authentication is not supported on storages for HDI activities.`

- **Cause** : Le ou les services liés de stockage utilisés dans le service lié HDI ou l’activité HDI sont configurés avec l’authentification MSI, ce qui n’est pas pris en charge.

- **Recommandation** :  Spécifiez des chaînes de connexion complètes pour les comptes de stockage utilisés dans le service lié HDI ou l’activité HDI.


### <a name="error-code--2305"></a>Code d’erreur :  2305

- **Message** : `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Cause** : Les informations de connexion pour le cluster HDI sont incorrectes. L’utilisateur spécifié n’est pas autorisé à effectuer l’action requise, ou le service HDInsight a rencontré des problèmes en répondant aux requêtes d’ADF.

- **Recommandation** :  Assurez-vous que les informations utilisateur sont correctes. Vérifiez également que l’interface utilisateur Ambari pour le cluster HDI peut être ouverte dans un navigateur à partir de la machine virtuelle sur laquelle le runtime d’intégration (IR) est installé (s’il s’agit d’un IR auto-hébergé) ou qu’elle peut être ouverte à partir de n’importe quel ordinateur (s’il s’agit d’un IR Azure).


### <a name="error-code--2306"></a>Code d’erreur :  2306

- **Message** : `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Cause** : Le JSON fourni pour l’action de script n’est pas valide.


- **Recommandation** : Le message d’erreur doit vous aider à identifier le problème. Corrigez la configuration JSON, puis réessayez. Pour plus d’informations, consultez https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service.
                


### <a name="error-code--2310"></a>Code d’erreur :  2310

- **Message** : `Failed to submit Spark job. Error: '%message;'`

- **Cause** : ADF a tenté de créer un lot sur un cluster Spark à l’aide de l’API Livy (livy/batch), mais a reçu une erreur.

- **Recommandation** :  Utilisez les informations contenues dans le message d’erreur pour résoudre le problème. Si vous ne disposez pas d’informations suffisantes pour résoudre le problème, contactez l’équipe HDI et indiquez-lui les ID de lot et de travail qui se trouvent dans la sortie de l’exécution de l’activité, dans la page de supervision d’ADF.


### <a name="error-code--2312"></a>Code d’erreur :  2312

- **Message** : `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Cause** : Le travail a échoué sur le cluster HDInsight Spark.

- **Recommandation** :  Suivez les liens figurant dans la sortie de l’exécution de l’activité (dans la page de supervision d’ADF) pour résoudre le problème d’exécution sur le cluster HDInsight Spark. Contactez l’équipe du support technique HDInsight pour obtenir une aide supplémentaire.


### <a name="error-code--2313"></a>Code d’erreur :  2313

- **Message** : `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Cause** : Le lot a été supprimé du cluster HDInsight Spark.

- **Recommandation** :  Résolvez les problèmes de lots sur le cluster HDInsight Spark. Contactez le support HDInsight pour obtenir une aide supplémentaire. 


### <a name="error-code--2328"></a>Code d’erreur :  2328

- **Message** : `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Cause** : `The error message should show the details of what went wrong.`

- **Recommandation** :  Le message d’erreur doit vous aider à résoudre le problème.


### <a name="error-code--2329"></a>Code d’erreur :  2329

- **Message** : `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Cause** : Le message d’erreur doit fournir des informations détaillées sur la cause du problème.

- **Recommandation** :  Le message d’erreur doit vous aider à résoudre le problème.


### <a name="error-code--2331"></a>Code d’erreur :  2331

- **Message** : `The file path should not be null or empty.`

- **Cause** : Le chemin de fichier spécifié est vide.

- **Recommandation** :  Spécifiez un chemin de fichier existant.


### <a name="error-code--2340"></a>Code d’erreur :  2340

- **Message** : `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Cause** : Le service lié HDInsightOnDemand ne prend pas en charge l’exécution par le biais d’un runtime d’intégration auto-hébergé.

- **Recommandation** :  Veuillez sélectionner un runtime d’intégration Azure et réessayez.


### <a name="error-code--2341"></a>Code d’erreur :  2341

- **Message** : `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Cause** : L’URL spécifiée n’est pas au bon format.

- **Recommandation** :  Corrigez l’URL de cluster et réessayez.


### <a name="error-code--2342"></a>Code d’erreur :  2342

- **Message** : `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Cause** : Les informations d’identification spécifiées sont incorrectes pour le cluster, un problème de connexion ou de configuration réseau s’est produit, ou le runtime d’intégration rencontre des problèmes de connexion au cluster.

- **Recommandation** :  
      1. Vérifiez que les informations d’identification sont correctes en ouvrant l’interface utilisateur Ambari du cluster HDInsight dans un navigateur.
      2. Si le cluster se trouve dans un réseau virtuel et que le runtime d’intégration auto-hébergé est en cours d’utilisation, l’URL HDI doit être l’URL privée dans les réseaux virtuels, ce qui signifie que le nom du cluster doit être suivi de « -int ». Par exemple, « https://mycluster.azurehdinsight.net/  » doit être remplacé par « https://mycluster-int.azurehdinsight.net/  ».
      2. Si le cluster se trouve dans un réseau virtuel, que le runtime d’intégration auto-hébergé est en cours d’utilisation, que l’URL privée a été utilisée et que la connexion a encore échoué, cela signifie que la machine virtuelle sur laquelle l’IR est installé a rencontré des problèmes de connexion au cluster HDI. Connectez-vous à la machine virtuelle sur laquelle l’IR est installé et ouvrez l’interface utilisateur Ambari dans un navigateur. Utilisez l’URL privée pour le cluster. Cette connexion doit fonctionner à partir du navigateur. Dans le cas contraire, contactez l’équipe du support technique HDInsight pour obtenir une aide supplémentaire.
      3. Si le runtime d’intégration auto-hébergé n’est pas en cours d’utilisation, le cluster HDI doit être accessible publiquement. Ouvrez l’interface utilisateur Ambari dans un navigateur et vérifiez que l’ouverture s’effectue correctement. En cas de problème avec le cluster ou les services correspondants, contactez l’équipe du support technique HDInsight pour obtenir de l’aide.
      Ainsi, en général, l’URL de cluster HDI utilisée dans le service lié ADF doit être accessible à l’IR ADF (auto-hébergé ou Azure) pour que le test de connexion réussisse et que les exécutions fonctionnent. Vous pouvez le vérifier facilement en ouvrant cette URL à partir d’un navigateur sur une machine virtuelle ou tout ordinateur public.
    


### <a name="error-code--2343"></a>Code d’erreur :  2343

- **Message** : `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Cause** : Le nom d’utilisateur ou le mot de passe est vide.

- **Recommandation** :  Spécifiez des informations d’identification correctes pour la connexion à HDI, puis réessayez.


### <a name="error-code--2345"></a>Code d’erreur :  2345

- **Message** : `Failed to read the content of the hive script. Error: '%message;'`

- **Cause** : Le fichier de script n’existe pas, ou ADF n’a pas pu se connecter à l’emplacement du script.

- **Recommandation** :  Vérifiez que le script existe et que le service lié associé dispose des informations d’identification appropriées pour la connexion.


### <a name="error-code--2346"></a>Code d’erreur :  2346

- **Message** : `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Cause** : ADF a tenté d’établir une connexion ODBC au cluster HDI. La connexion a échoué avec une erreur.

- **Recommandation** :  Le message et le code d’erreur doivent vous aider à résoudre les problèmes de connexion ODBC. S’ils ne vous permettent pas de résoudre le problème, contactez l’équipe Azure HDInsight pour obtenir de l’aide.


### <a name="error-code--2347"></a>Code d’erreur :  2347

- **Message** : `Hive execution through ODBC failed with error message '%message;'.`

- **Cause** : ADF a envoyé le script Hive pour exécution au cluster HDI par le biais d’une connexion ODBC et le script a échoué sur HDI.

- **Recommandation** :  L’exécution du script Hive a échoué sur le cluster HDI. Le message et le code d’erreur devraient vous aider à résoudre le problème. S’ils ne vous permettent pas de résoudre le problème, contactez l’équipe Azure HDInsight pour obtenir de l’aide.


### <a name="error-code--2348"></a>Code d’erreur :  2348

- **Message** : `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Cause** : Les propriétés du service lié de stockage ne sont pas définies correctement.

- **Recommandation** :  Seules les chaînes de connexion complètes sont prises en charge dans le service lié de stockage principal pour les activités HDI. Vérifiez que vous n’utilisez pas l’authentification ou des applications MSI.


### <a name="error-code--2350"></a>Code d’erreur :  2350

- **Message** : `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Cause** : Les informations d’identification spécifiées pour la connexion au stockage qui doit héberger les fichiers sont incorrectes, ou les fichiers n’existent pas à cet emplacement.

- **Recommandation** :  Cette erreur se produit quand ADF effectue les étapes de préparation des activités HDI. Il tente de copier les fichiers vers le stockage principal avant d’envoyer le travail à HDI. Assurez-vous que les fichiers existent à l’emplacement spécifié et que la connexion au stockage est correcte. Les activités HDI d’ADF ne prennent pas en charge l’authentification MSI sur les comptes de stockage associés aux activités HDI. Assurez-vous que ces services liés disposent de clés complètes ou utilisent Azure Key Vault.


### <a name="error-code--2351"></a>Code d’erreur :  2351

- **Message** : `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Cause** : Le fichier n’existe pas au chemin spécifié.

- **Recommandation** :  Vérifiez que le fichier existe réellement et que le service lié dont les informations de connexion pointent vers ce fichier dispose des informations d’identification correctes.


### <a name="error-code--2352"></a>Code d’erreur :  2352

- **Message** : `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Cause** : Les propriétés du service lié de stockage de fichiers ne sont pas définies correctement.

- **Recommandation** :  Vérifiez que les propriétés du service lié de stockage de fichiers sont correctement configurées.


### <a name="error-code--2353"></a>Code d’erreur :  2353

- **Message** : `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Cause** : Les propriétés du service lié de stockage de scripts ne sont pas définies correctement.

- **Recommandation** :  Vérifiez que les propriétés du service lié de stockage de scripts sont correctement configurées.


### <a name="error-code--2354"></a>Code d’erreur :  2354

- **Message** : `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Cause** : Le type de service lié de stockage n’est pas pris en charge par l’activité.

- **Recommandation** :  Assurez-vous que le type de service lié sélectionné fait partie des types pris en charge pour l’activité. Les activités HDI prennent en charge les services liés AzureBlobStorage et AzureBlobFSStorage.


### <a name="error-code--2355"></a>Code d’erreur :  2355

- **Message** : `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Cause** : La valeur spécifiée pour commandEnvironment est incorrecte.

- **Recommandation** :  
      Assurez-vous que la valeur spécifiée est de type \"commandEnvironment\": [ \"NomVariable=ValeurVariable\" ] et que chaque variable apparaît une seule fois dans la liste.
    


### <a name="error-code--2356"></a>Code d’erreur :  2356

- **Message** : `The commandEnvironment already contains a variable named '%variableName;'.`

- **Cause** : La variable a été spécifiée deux fois dans commandEnvironment.

- **Recommandation** :  
      Assurez-vous que la valeur spécifiée est de type \"commandEnvironment\": [ \"NomVariable=ValeurVariable\" ] et que chaque variable apparaît une seule fois dans la liste.
    


### <a name="error-code--2357"></a>Code d’erreur :  2357

- **Message** : `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Cause** : Les informations d’identification spécifiées sont incorrectes.

- **Recommandation** :  Vérifiez les informations de connexion dans le service lié ADLS Gen1 et assurez-vous que le test de connexion réussit.


### <a name="error-code--2358"></a>Code d’erreur :  2358

- **Message** : `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Cause** : Le format de la valeur spécifiée pour la propriété requise « TimeToLive » n’est pas valide. 

- **Recommandation** :  Modifiez la valeur pour qu’elle soit incluse dans la plage suggérée, puis réessayez.


### <a name="error-code--2359"></a>Code d’erreur :  2359

- **Message** : `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Cause** : La valeur spécifiée pour la propriété « roles » n’est pas valide.

- **Recommandation** :  Modifiez la valeur pour qu’elle corresponde à l’une des valeurs suggérées, puis réessayez.


### <a name="error-code--2360"></a>Code d’erreur :  2360

- **Message** : `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Cause** : La chaîne de connexion spécifiée pour HCatalogLinkedService n’est pas valide.

- **Recommandation** :  Modifiez la valeur pour qu’elle corresponde à une chaîne de connexion Azure SQL correcte, puis réessayez.


### <a name="error-code--2361"></a>Code d’erreur :  2361

- **Message** : `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Cause** : La création du cluster a échoué, et ADF n’a pas reçu d’erreur du service HDInsight.

- **Recommandation** :  Ouvrez le portail Azure, recherchez la ressource HDI avec le nom spécifié et vérifiez l’état du provisionnement. Contactez l’équipe du support technique HDInsight pour obtenir une aide supplémentaire.


### <a name="error-code--2362"></a>Code d’erreur :  2362

- **Message** : `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Cause** : Le stockage supplémentaire spécifié n’était pas un stockage d’objets blob Azure.

- **Recommandation** :  Spécifiez un compte de stockage d’objets blob Azure en tant que stockage supplémentaire pour le service lié HDInsight à la demande.



## <a name="web-activity"></a>Activité web

### <a name="error-code--2128"></a>Code d’erreur :  2128

- **Message** : `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Cause** : Connectivité réseau, échec de DNS, validation de certificat de serveur ou délai d’expiration.

- **Recommandation** :  Vérifiez que le point de terminaison que vous essayez d’atteindre répond aux requêtes. Vous pouvez utiliser des outils tels que Fiddler/Postman.


### <a name="error-code--2108"></a>Code d’erreur :  2108

- **Message** : `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Cause** : La requête a échoué en raison d’un problème sous-jacent, par exemple un problème de connectivité réseau, un échec de DNS, un problème de validation de certificat de serveur ou l’expiration d’un délai.

- **Recommandation** :  Utilisez Fiddler/Postman pour valider la requête.
<br>


#### <a name="more-details"></a>Détails supplémentaires
Pour utiliser Fiddler en vue de créer une session HTTP de l’application web supervisée :

1. Téléchargez, installez puis ouvrez [Fiddler](https://www.telerik.com/download/fiddler).

1. Si votre application web utilise le protocole HTTPS, accédez à **Tools** > **Fiddler Options** > **HTTPS** (Outils > Options Fiddler > HTTPS). Sélectionnez **Capture HTTPS CONNECTs** (Capturer les HTTPS CONNECT) et **Decrypt HTTPS traffic** (Déchiffrer le trafic HTTPS).

   ![Options Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Si votre application utilise des certificats SSL, ajoutez le certificat Fiddler à votre appareil. Accédez à **Tools** > **Fiddler Options** > **HTTPS** > **Actions** > **Export Root Certificate to Desktop** (Outils > Options Fiddler > HTTPS > Actions > Exporter le certificat racine vers le Bureau).

1. Désactivez la capture en accédant à **File** > **Capture Traffic** (Fichier > Capturer le trafic). Vous pouvez aussi appuyer sur **F12**.

1. Videz le cache de votre navigateur afin que tous les éléments présents soient supprimés et doivent être téléchargés à nouveau.

1. Créez une requête :

   1. Sélectionnez l’onglet **Composer** (Éditeur).

   1. Définissez la méthode HTTP et l’URL.
   
   1. Ajoutez des en-têtes et un corps de requête si nécessaire.

   1. Sélectionnez **Exécuter**.

1. Réactivez la capture du trafic, puis terminez la transaction problématique dans votre page.

1. Accédez à **File** > **Save** > **All Sessions** (Fichier > Enregistrer > Toutes les sessions).

Pour plus d’informations, consultez [Bien démarrer avec Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)


            
