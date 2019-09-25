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
ms.openlocfilehash: ed466b072a771c3aa288a96fa4a0037c31b875f9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091989"
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

- **Message** : `Cluster   ... does not exist.`

- **Cause** : `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Recommandation** : Vérifiez que le cluster Databricks existe.

<br/>

- **Message** : `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

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

- **Message** : `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Cause** :  Le principal de service ou le certificat fourni n’a pas accès au fichier situé dans le stockage.

- **Recommandation** : Vérifiez que le principal de service ou le certificat fourni par l’utilisateur pour les travaux Data Lake Analytics a accès au compte Data Lake Analytics et à l’instance Data Lake Storage par défaut à partir du dossier racine.


### <a name="error-code--2711"></a>Code d’erreur :  2711

- **Message** : `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Cause** :  Le principal de service ou le certificat fourni n’a pas accès au fichier situé dans le stockage.

- **Recommandation** : Vérifiez que le principal de service ou le certificat fourni par l’utilisateur pour les travaux Data Lake Analytics a accès au compte Data Lake Analytics et à l’instance Data Lake Storage par défaut à partir du dossier racine.

<br/>

- **Message** : `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Cause** :  Le chemin du fichier U-SQL est incorrect ou les informations d’identification du service lié n’y ont pas accès.

- **Recommandation** : Vérifiez le chemin et les informations d’identification fournies dans le service lié.


### <a name="error-code--2704"></a>Code d’erreur :  2704

- **Message** : `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

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

- **Message** : `Invalid HttpMethod: {method}.`

- **Cause** : La méthode HTTP spécifiée dans la charge utile d’activité n’est pas prise en charge par l’activité de fonction Azure. 

- **Recommandation** : Les méthodes HTTP prises en charge sont PUT, POST, GET, DELETE, OPTIONS, HEAD et TRACE.


### <a name="error-code--3603"></a>Code d’erreur :  3603

- **Message** : `Response content is not a valid JObject.`

- **Cause** : La fonction Azure qui a été appelée n’a pas retourné de charge utile JSON dans sa réponse. Dans Data Factory, l’activité de fonction Azure prend uniquement en charge le contenu de réponse JSON. 

- **Recommandation** : Mettez à jour la fonction Azure pour retourner une charge utile JSON valide. Par exemple, une fonction C# peut retourner `(ActionResult)new<OkObjectResult("{`\"Id\" :\"123\"`}");`.


### <a name="error-code--3606"></a>Code d’erreur :  3606

- **Message** : `Azure function activity missing function key.`

- **Cause** : La définition de l’activité de fonction Azure n’est pas terminée. 

- **Recommandation** : Vérifiez que la définition JSON de l’activité AzureFunction dispose d'une propriété nommée « functionKey ».


### <a name="error-code--3607"></a>Code d’erreur :  3607

- **Message** : `Azure function activity missing function name.`

- **Cause** : La définition de l’activité de fonction Azure n’est pas terminée. 

- **Recommandation** : Vérifiez que la définition JSON de l’activité AzureFunction dispose d'une propriété nommée « functionName ».


### <a name="error-code--3608"></a>Code d’erreur :  3608

- **Message** : `Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Cause** : Les détails de la fonction Azure dans la définition de l’activité sont peut-être incorrects. 

- **Recommandation** : Corrigez les détails de la fonction Azure, puis réessayez.


### <a name="error-code--3609"></a>Code d’erreur :  3609

- **Message** : `Azure function activity missing functionAppUrl.` 

- **Cause** : La définition de l’activité de fonction Azure n’est pas terminée. 

- **Recommandation** : Vérifiez que la définition JSON de l’activité AzureFunction dispose d'une propriété nommée « functionAppUrl ».


### <a name="error-code--3610"></a>Code d’erreur :  3610

- **Message** : `There was an error while calling endpoint.`

- **Cause** : L’URL de la fonction est peut-être incorrecte.

- **Recommandation** : Vérifiez que la valeur de « functionAppUrl » dans le JSON de l’activité est correcte, puis réessayez.


### <a name="error-code--3611"></a>Code d’erreur :  3611

- **Message** : `Azure function activity missing Method in JSON.` 

- **Cause** : La définition de l’activité de fonction Azure n’est pas terminée.

- **Recommandation** : Vérifiez que la définition JSON de l’activité AzureFunction dispose d'une propriété nommée « method ».


### <a name="error-code--3612"></a>Code d’erreur :  3612

- **Message** : `Azure function activity missing LinkedService definition in JSON.`

- **Cause** : La définition de l’activité de fonction Azure n’est peut-être pas terminée.

- **Recommandation** : Vérifiez que la définition JSON de l’activité AzureFunction dispose des détails sur le service lié.


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>Code d’erreur :  4101

- **Message** : `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Cause** : Format incorrect ou définition manquante d’une propriété.

- **Recommandation** :  Vérifiez si l’activité est définie avec les données qui conviennent.


### <a name="error-code--4110"></a>Code d’erreur :  4110

- **Message** : La définition LinkedService de l’activité AzureMLExecutePipeline est manquante dans JSON.

- **Cause** : La définition de l’activité AzureMLExecutePipeline n’est pas terminée.

- **Recommandation** :  Vérifiez si la définition JSON de l’activité AzureMLExecutePipeline dispose des détails sur le service lié.


### <a name="error-code--4111"></a>Code d’erreur :  4111

- **Message** : `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Cause** : Définition d'activité incorrecte.

- **Recommandation** :  Vérifiez si la définition JSON de l’activité AzureMLExecutePipeline dispose des détails corrects sur le service lié.


### <a name="error-code--4112"></a>Code d’erreur :  4112

- **Message** : `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Cause** : Format incorrect ou définition manquante d’une propriété.

- **Recommandation** :  Vérifiez si la définition du service lié contient les données correctes.


### <a name="error-code--4121"></a>Code d’erreur :  4121

- **Message** : `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Cause** : Les informations d’identification utilisées pour accéder à Azure Machine Learning ont expiré.

- **Recommandation** :  Vérifiez si les informations d’identification sont valides et réessayez.


### <a name="error-code--4122"></a>Code d’erreur :  4122

- **Message** : `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Cause** : Les informations d’identification fournies dans le service lié du service AzureML ne sont pas valides ou autorisées pour l’opération.

- **Recommandation** :  Vérifiez si les informations d’identification du service lié sont valides et autorisées pour accéder au service AzureML.


### <a name="error-code--4123"></a>Code d’erreur :  4123

- **Message** : `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Cause** : `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Recommandation** :  Vérifiez si la valeur des propriétés de l’activité correspond à la charge utile attendue du pipeline Azure ML publié spécifié dans le service lié.


### <a name="error-code--4124"></a>Code d’erreur :  4124

- **Message** : `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Cause** : Le point de terminaison de pipeline Azure ML publié n’existe pas.

- **Recommandation** :  Vérifiez si le point de terminaison du pipeline Azure ML publié spécifié dans le service lié existe dans le service Azure ML.


### <a name="error-code--4125"></a>Code d’erreur :  4125

- **Message** : `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Cause** : Erreur de serveur sur le service Azure ML.

- **Recommandation** :  Veuillez réessayer ultérieurement. Si le problème persiste, contactez l’équipe du service Azure ML pour obtenir de l'aide.


### <a name="error-code--4126"></a>Code d’erreur :  4126

- **Message** : `AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **Cause** : L'exécution du pipeline AzureML a échoué.

- **Recommandation** :  Pour plus d’informations sur les journaux d’erreurs et corriger le pipeline ML, consultez AzureMLService.


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

- **Message** : `The folder path does not exist or is empty: ....`

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

Le tableau suivant s’applique à Spark, Hive, MapReduce, Pig et Hadoop Streaming.


### <a name="error-code--2300"></a>Code d’erreur :  2 300

- **Message** : `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Cause** : L’URI de cluster fourni n’est pas valide. 

- **Recommandation** :  Vérifiez que le cluster n’a pas été supprimé et que l’URI fourni est correct. Lorsque vous ouvrez l’URI dans un navigateur, vous devez voir l’interface utilisateur d’Ambari. Si le cluster se trouve dans un réseau virtuel, l’URI doit être l’URI privé. Pour l’ouvrir, utilisez une machine virtuelle qui fait partie du même réseau virtuel. Pour plus d’informations, consultez [Se connecter directement aux services Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Message** : `Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Cause** : L’envoi du travail a expiré. 

- **Recommandation** : Il peut s’agir d’un problème de connectivité générale HDInsight ou d’un problème de connectivité réseau. Tout d’abord, vérifiez que l’interface utilisateur Ambari HDInsight est accessible à partir de n’importe quel navigateur. Vérifiez que vos informations d’identification sont toujours valides. Si vous utilisez le runtime d’intégration auto-hébergé (IR), veillez à utiliser la machine virtuelle ou l’ordinateur où celui-ci est installé. Ensuite, renvoyez le travail à partir de Data Factory. Si le problème persiste, contactez l’équipe Data Factory pour obtenir de l’aide.


- **Message** : `Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Cause** : Les informations d’identification fournies pour HDInsight sont incorrectes ou ont expiré.

- **Recommandation** : Corrigez les informations d’identification et redéployez le service lié. Vérifiez tout d’abord que les informations d’identification fonctionnent sur HDInsight en ouvrant l’URI de cluster sur n’importe quel navigateur et en essayant de vous connecter. Si elles ne fonctionnent pas, vous pouvez les réinitialiser à partir du portail Azure.

<br/>

- **Message** : `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Cause** : L’erreur provient de HDInsight.

- **Recommandation** : L’erreur provient du cluster HDInsight. Pour plus d’informations, consultez [Erreur 502 : Interface utilisateur Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [Erreur 502 : Connexion au serveur Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [Erreur 502 : Connexion au serveur Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html) et [Résolution des erreurs de passerelle incorrecte dans Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Message** : `Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Cause** : Trop de travaux sont envoyés à HDInsight en même temps.

- **Recommandation** : Envisagez de limiter le nombre de travaux simultanés envoyés à HDInsight. Renseignez-vous sur la simultanéité des activités Data Factory si les travaux sont envoyés par la même activité. Modifiez les déclencheurs de sorte que les exécutions de pipeline simultanées soient réparties au fil du temps. Reportez-vous à la documentation HDInsight pour ajuster `templeton.parallellism.job.submit` comme le suggère l’erreur.


### <a name="error-code--2303"></a>Code d’erreur :  2303

- **Message** : `Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Cause** : Le travail a été envoyé à HDInsight, et il a échoué sur HDInsight.

- **Recommandation** : Le travail a bien été envoyé à HDInsight. Il a échoué sur le cluster. Ouvrez le travail et les journaux dans l’interface utilisateur Ambari HDInsight, ou ouvrez le fichier à partir du stockage, comme le suggère le message d’erreur. Le fichier affiche les détails de l’erreur.


### <a name="error-code--2310"></a>Code d’erreur :  2310

- **Message** : `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Cause** : L’URI de cluster fourni n’est pas valide. 

- **Recommandation** :  Vérifiez que le cluster n’a pas été supprimé et que l’URI fourni est correct. Lorsque vous ouvrez l’URI dans un navigateur, vous devez voir l’interface utilisateur d’Ambari. Si le cluster se trouve dans un réseau virtuel, l’URI doit être l’URI privé. Pour l’ouvrir, utilisez une machine virtuelle qui fait partie du même réseau virtuel. Pour plus d’informations, consultez [Se connecter directement aux services Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Message** : `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Cause** : L’erreur provient de HDInsight.

- **Recommandation** : L’erreur provient du cluster HDInsight. Pour plus d’informations, consultez [Erreur 502 : Interface utilisateur Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [Erreur 502 : Connexion au serveur Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [Erreur 502 : Connexion au serveur Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html) et [Résolution des erreurs de passerelle incorrecte dans Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Message** : `java.lang.NullPointerException`

- **Cause** : Cette erreur se produit lorsque le travail est envoyé à un cluster Spark. 

- **Recommandation** : Cette exception provient de HDInsight. Elle cache le vrai problème. Contactez l’équipe HDInsight pour obtenir de l’aide. Indiquez le nom du cluster et la période de l’exécution d’activité.


### <a name="error-code--2347"></a>Code d’erreur :  2347

- **Message** : `Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Cause** : Le travail a été envoyé à HDInsight, et il a échoué sur HDInsight.

- **Recommandation** : Le travail a bien été envoyé à HDInsight. Il a échoué sur le cluster. Ouvrez le travail et les journaux dans l’interface utilisateur Ambari HDInsight, ou ouvrez le fichier à partir du stockage, comme le suggère le message d’erreur. Le fichier affiche les détails de l’erreur.


### <a name="error-code--2328"></a>Code d’erreur :  2328

- **Message** : `Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Cause** : Cette erreur se produit dans HDInsight à la demande.

- **Recommandation** : Cette erreur provient du service HDInsight en cas d’échec du provisionnement HDInsight. Contactez l’équipe HDInsight pour lui fournir le nom du cluster à la demande.



## <a name="web-activity"></a>Activité web

### <a name="error-code--2108"></a>Code d’erreur :  2108

- **Message** : `Invalid HttpMethod: '...'.`

- **Cause** : L’activité web ne prend pas en charge la méthode HTTP spécifiée dans la charge utile d’activité.

- **Recommandation** :  Les méthodes HTTP prises en charge sont PUT, POST, GET et DELETE.

<br/>

- **Message** : `Invalid Server Error 500.`

- **Cause** : Erreur interne sur le point de terminaison

- **Recommandation** :  Utilisez Fiddler ou Postman pour vérifier son fonctionnement avec l’URL.

<br/>

- **Message** : `Unauthorized 401.`

- **Cause** : Authentification valide manquante dans la requête.

- **Recommandation** :  Le jeton a peut-être expiré. Fournissez une méthode d’authentification valide. Utilisez Fiddler ou Postman pour vérifier son fonctionnement avec l’URL.

<br/>

- **Message** : `Forbidden 403.`

- **Cause** : Autorisations manquantes.

- **Recommandation** :  Vérifiez les autorisations des utilisateurs sur la ressource souhaitée. Utilisez Fiddler ou Postman pour vérifier son fonctionnement avec l’URL.

<br/>

- **Message** : `Bad Request 400.`

- **Cause** : Requête HTTP non valide.

- **Recommandation** :   Vérifiez l’URL, le verbe et le corps de la requête. Utilisez Fiddler ou Postman pour valider la requête.

<br/>

- **Message** : `Not found 404.` 

- **Cause** : La ressource est introuvable.   

- **Recommandation** :  Utilisez Fiddler ou Postman pour valider la requête.

<br/>

- **Message** : `Service unavailable.`

- **Cause** : Le service n’est pas disponible.

- **Recommandation** :  Utilisez Fiddler ou Postman pour valider la requête.

<br/>

- **Message** : `Unsupported Media Type.`

- **Cause** : Le type de contenu ne correspond pas au corps de l’activité web.

- **Recommandation** :  Spécifiez le type de contenu qui correspond au format de la charge utile. Utilisez Fiddler ou Postman pour valider la requête.

<br/>

- **Message** : `The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Cause** : La ressource n’est pas disponible. 

- **Recommandation** :  Utilisez Fiddler ou Postman pour vérifier le point de terminaison.

<br/>

- **Message** : `The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Cause** : Une méthode d’activité web incorrecte a été spécifiée dans la requête.

- **Recommandation** :  Utilisez Fiddler ou Postman pour vérifier le point de terminaison.

<br/>

- **Message** : `invalid_payload`

- **Cause** : Le corps de l’activité web est incorrect.

- **Recommandation** :  Utilisez Fiddler ou Postman pour vérifier le point de terminaison.


### <a name="error-code--2208"></a>Code d’erreur :  2208

- **Message** : `Invoking Web Activity failed with HttpStatusCode - {0}.`

- **Cause** : Le service cible a renvoyé un état d’échec.

- **Recommandation** :  Utilisez Fiddler/Postman pour valider la requête.


### <a name="error-code--2308"></a>Code d’erreur :  2308

- **Message** : `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Cause** : Plusieurs raisons peuvent expliquer cette erreur : connectivité réseau, échec DNS, validation du certificat du serveur ou délai d’expiration.

- **Recommandation** :  Utilisez Fiddler/Postman pour valider la requête.


Pour utiliser Fiddler en vue de créer une session HTTP de l’application web supervisée :

1. Téléchargez, installez puis ouvrez [Fiddler](https://www.telerik.com/download/fiddler).

1. Si votre application web utilise le protocole HTTPS, accédez à **Tools** > **Fiddler Options** > **HTTPS** (Outils > Options Fiddler > HTTPS). Sélectionnez **Capture HTTPS CONNECTs** (Capturer les HTTPS CONNECT) et **Decrypt HTTPS traffic** (Déchiffrer le trafic HTTPS). 
   
   ![Options Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Si votre application utilise des certificats SSL, ajoutez le certificat Fiddler à votre appareil. Accédez à **Tools** > **Fiddler Options** > **HTTPS** > **Actions** > **Export Root Certificate to Desktop** (Outils > Options Fiddler > HTTPS > Actions > Exporter le certificat racine vers le Bureau).

1. Désactivez la capture en accédant à **File** > **Capture Traffic** (Fichier > Capturer le trafic). Vous pouvez aussi appuyer sur **F12**.

1. Videz le cache de votre navigateur afin que tous les éléments présents soient supprimés et doivent être téléchargés à nouveau.

1. Créez une requête : 

   a. Sélectionnez l’onglet **Composer** (Éditeur).

   b. Définissez la méthode HTTP et l’URL.

   c. Ajoutez des en-têtes et un corps de requête si nécessaire.

   d. Sélectionnez **Exécuter**.

9. Réactivez la capture du trafic, puis terminez la transaction problématique dans votre page.

10. Accédez à **File** > **Save** > **All Sessions** (Fichier > Enregistrer > Toutes les sessions).

Pour plus d’informations, consultez [Bien démarrer avec Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)



