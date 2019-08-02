---
title: Résoudre les problèmes rencontrés dans Azure Data Factory | Microsoft Docs
description: Découvrez comment résoudre les problèmes liés aux activités de contrôle externes dans Azure Data Factory.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: c76242c176ba4f4c9ffc0d6934f6b645743d77f4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234581"
---
# <a name="troubleshoot-azure-data-factory"></a>Résoudre les problèmes dans Azure Data Factory

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés aux activités de contrôle externes dans Azure Data Factory.

## <a name="azure-databricks"></a>Azure Databricks

| Code d'erreur | Message d’erreur                                          | Description                             | Recommandation                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Erreur 403.                                                    | Le jeton d’accès Databricks a expiré.                         | Par défaut, le jeton d’accès Databricks est valide pendant 90 jours.  Créez un autre jeton et mettez à jour le service lié. |
| 3201           | Champ obligatoire manquant : settings.task.notebook_task.notebook_path | Création incorrecte : le chemin d’accès au notebook n’est pas correctement spécifié. | Spécifiez le chemin du notebook dans l’activité Databricks. |
| 3201           | Le cluster ... n’existe pas.                                 | Erreur de création : le cluster Databricks n’existe pas ou a été supprimé. | Vérifiez que le cluster Databricks existe. |
| 3201           | URI de fichier Python non valide Consultez le guide de l’utilisateur Databricks pour connaître les schémas d’URI pris en charge. | Création incorrecte.                                                | Spécifiez les chemins absolus des schémas d’adressage d’espace de travail, ou `dbfs:/folder/subfolder/foo.py` pour les fichiers stockés dans Databricks File System. |
| 3201           | {0}   Le service lié doit contenir les propriétés obligatoires domain et accessToken. | Création incorrecte.                                                | Vérifiez la [définition du service lié](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}   Le service lié doit spécifier un ID de cluster existant ou de nouvelles informations de cluster pour la création. | Création incorrecte.                                                | Vérifiez la [définition du service lié](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Le type de nœud Standard_D16S_v3 n’est pas pris en charge. Types de nœuds pris en charge :   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2. | Création incorrecte.                                                | Voir le message d’erreur.                                          |
| 3201           | notebook_path non valide : Seuls les chemins d’accès absolus sont actuellement pris en charge. Les chemins d’accès doivent commencer par « / ». | Création incorrecte.                                                | Voir le message d’erreur.                                          |
| 3202           | 1 000 travaux ont déjà été créés dans les dernières 3 600 secondes, dépassant la limite de débit :   1 000 créations de travail pour 3 600 secondes. | Trop de travaux Databricks s’exécutent en une heure.                         | Vérifiez le taux de création de travaux dans tous les pipelines qui utilisent cet espace de travail Databricks.  Si les pipelines ont lancé trop de travaux Databricks dans un agrégat, migrez des pipelines dans un nouvel espace de travail. |
| 3202           | Impossible d’analyser l’objet de la requête : « key » et « value » attendus comme définition pour le champ de mappage JSON base_parameters ; le paramètre fournir est « clé : ... » à la place. | Erreur de création : aucune valeur fournie pour le paramètre.         | Inspectez le code JSON de pipeline et vérifiez que tous les paramètres du notebook baseParameters spécifient une valeur non vide. |
| 3202           | Utilisateur : `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` n’est pas autorisé à accéder au cluster. | L’utilisateur qui a généré le jeton d’accès n’est pas autorisé à accéder au cluster Databricks spécifié dans le service lié. | Vérifiez que l’utilisateur dispose des autorisations nécessaires dans l’espace de travail.   |
| 3203           | Le cluster est dans un état Terminé et n’est pas disponible pour recevoir des travaux. Corrigez le cluster ou réessayez plus tard. | Le cluster a été arrêté.    Pour les clusters interactifs, il peut s’agir d’une condition de concurrence. | La meilleure façon d’éviter ce problème consiste à utiliser des clusters de travaux.             |
| 3204           | Échec de l’exécution du travail.  | Les messages d’erreur indiquent différents problèmes, tels qu’un état de cluster inattendu ou une activité inattendue. Le plus souvent, aucun message d’erreur ne s’affiche.                                                          | N/A                                                          |



## <a name="azure-data-lake-analytics"></a>Service Analytique Azure Data Lake

Le tableau suivant s’applique à U-SQL.

| Code d'erreur         | Message d’erreur                                                | Description                                          | Recommandation                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Le jeton d’accès provient du mauvais locataire.                    | Mauvais locataire Azure Active Directory (Azure AD).                                         | Le principal du service utilisé pour accéder à Azure Data Lake Analytics appartient à un autre locataire Azure AD. Créez un principal de service dans le locataire où se trouve le compte Data Lake Analytics. |
| 2711,   2705,   2704 | Interdit. Échec de la vérification d’ACL. La ressource n’existe pas ou l’utilisateur n’est pas autorisé à effectuer l’opération demandée.<br/><br/>L’utilisateur n’est pas en mesure d’accéder à Data Lake Store.  <br/><br/>L’utilisateur n’est pas autorisé à utiliser Data Lake Analytics. | Le principal de service ou le certificat fourni n’a pas accès au fichier situé dans le stockage. | Vérifiez que le principal de service ou le certificat fourni par l’utilisateur pour les travaux Data Lake Analytics a accès au compte Data Lake Analytics et à l’instance Data Lake Storage par défaut à partir du dossier racine. |
| 2711                 | Impossible de trouver le fichier ou le dossier « Azure Data Lake Store ».       | Le chemin du fichier U-SQL est incorrect ou les informations d’identification du service lié n’y ont pas accès. | Vérifiez le chemin et les informations d’identification fournies dans le service lié. |
| 2707                 | Impossible de résoudre le compte d’AzureDataLakeAnalytics. Vérifiez « AccountName » et « DataLakeAnalyticsUri ». | Le compte Data Lake Analytics du service lié est incorrect.                  | Vérifiez que le bon compte a été fourni.             |
| 2703                 | ID d'erreur : E_CQO_SYSTEM_INTERNAL_ERROR (ou une autre erreur qui commence par « ID d’erreur : ») | L’erreur provient de Data Lake Analytics.                                    | Une erreur comme celle de l’exemple signifie que le travail a été envoyé à Data Lake Analytics et que le script a échoué. Examinez le problème dans Data Lake Analytics. Dans le portail, accédez au compte Data Lake Analytics et recherchez le travail à l’aide de l’ID d’exécution de l’activité Data Factory (et non de l’ID d’exécution du pipeline). Le travail fournit davantage d’informations sur l’erreur qui vous permettront de la résoudre. Si vous n’êtes pas sûr d’avoir résolu votre problème, contactez l’équipe du support technique Data Lake Analytics pour lui donner l’URL du travail, qui comprend le nom de votre compte et l’ID du travail. |
| 2709                 | Nous ne pouvons pas accepter votre travail pour l’instant. Le nombre maximal de travaux en file d’attente pour votre compte est de 200. | Cette erreur est provoquée par une limitation dans Data Lake Analytics.                                           | Réduisez le nombre de travaux envoyés à Data Lake Analytics en modifiant les déclencheurs Data Factory et les paramètres de concurrence des activités. Vous pouvez également augmenter les limites dans Data Lake Analytics. |
| 2709                 | Ce travail a été rejeté, car il nécessite 24 unités Analytique. La stratégie définie par l’administrateur de ce compte empêche un même travail d’utiliser plus de 5 unités Analytique. | Cette erreur est provoquée par une limitation dans Data Lake Analytics.                                           | Réduisez le nombre de travaux envoyés à Data Lake Analytics en modifiant les déclencheurs Data Factory et les paramètres de concurrence des activités. Vous pouvez également augmenter les limites dans Data Lake Analytics. |



## <a name="azure-functions"></a>Fonctions Azure

| Code d'erreur | Message d’erreur                           | Description                                                  | Recommandation                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Le contenu de la réponse n’est pas un JObject valide. | La fonction Azure qui a été appelée n’a pas retourné de charge utile JSON dans sa réponse. Dans Data Factory, l’activité de fonction Azure prend uniquement en charge le contenu de réponse JSON. | Mettez à jour la fonction Azure pour retourner une charge utile JSON valide. Par exemple, une fonction C# peut retourner `(ActionResult)new<OkObjectResult("{`\"Id\" :\"123\"`}");`. |
| 3600         | HttpMethod non valide :               | La méthode HTTP spécifiée dans la charge utile d’activité n’est pas prise en charge par l’activité de fonction Azure. | Utilisez une méthode HTTP prise en charge, comme PUT, POST, GET, DELETE, OPTIONS, HEAD ou TRACE. |



## <a name="custom"></a>Personnalisée

Le tableau suivant s’applique à Azure Batch.

| Code d'erreur | Message d’erreur                                                | Description                                                  | Recommandation                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2 500         | Exception inattendue et échec de l’exécution.             | Impossible de lancer la commande, ou le programme a retourné un code d’erreur. | Vérifiez que le fichier exécutable existe. Si le programme a démarré, vérifiez que les fichiers *stdout.txt* et *stderr.txt* ont été chargés dans le compte de stockage. Il est conseillé de publier des journaux volumineux dans votre code à des fins de débogage. |
| 2501         | Impossible d’accéder au compte batch d’utilisateur. Vérifiez les paramètres du compte batch. | Le nom de pool ou la clé d’accès Batch fournis ne sont pas corrects.            | Vérifiez le nom du pool et la clé d’accès Batch dans le service lié. |
| 2502         | Impossible d’accéder au compte de stockage d’utilisateur. Vérifiez les paramètres du compte de stockage. | Le nom du compte de stockage ou la clé d’accès fournis ne sont pas corrects.       | Vérifiez le nom du compte de stockage et la clé d’accès dans le service lié. |
| 2504         | L’opération a retourné un code d’état non valide : « BadRequest ».     | Trop de fichiers dans le folderPath de l’activité personnalisée. La taille totale de resourceFiles ne peut pas être supérieure à 32 768 caractères. | Supprimez les fichiers inutiles. Vous pouvez également les placer dans un fichier zip et ajouter une commande de décompression pour les extraire. Par exemple, utilisez `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`. |
| 2505         | Impossible de créer la signature d’accès partagé, sauf si les informations d’identification de clé de compte sont utilisées. | Les activités personnalisées prennent uniquement en charge les comptes de stockage qui utilisent une clé d’accès. | Voir la description de l’erreur.                                            |
| 2507         | Le chemin du dossier n’existe pas ou est vide :            | Aucun fichier ne se trouve dans le compte de stockage situé au chemin spécifié.       | Le chemin du dossier doit contenir les fichiers exécutables que vous souhaitez exécuter. |
| 2508         | Il existe des fichiers en double dans le dossier de ressources.               | Il existe plusieurs fichiers du même nom dans différents sous-dossiers de folderPath. | Les activités personnalisées aplatissent la structure de dossiers sous folderPath.  Si vous devez conserver la structure de dossiers, compressez les fichiers dans un fichier zip, puis extrayez-les dans Azure Batch à l’aide d’une commande de décompression. Par exemple, utilisez `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`. |
| 2509         | L’URL Batch n’est pas valide, elle doit être au format Uri.         | L’URL batch doit être semblable à `https://mybatchaccount.eastus.batch.azure.com`. | Voir la description de l’erreur.                                            |
| 2510         | Une erreur s’est produite lors de l’envoi de la requête.               | L’URL Batch n’est pas valide.                                         | Vérifiez l’URL Batch.                                            |

## <a name="hdinsight"></a>HDInsight

Le tableau suivant s’applique à Spark, Hive, MapReduce, Pig et Hadoop Streaming.

| Code d’erreur | Message d’erreur                                                | Description                                                  | Recommandation                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | La soumission du travail Hadoop a échoué. Error: Impossible de résoudre le nom distant. <br/><br/>Le cluster est introuvable. | L’URI de cluster fourni n’est pas valide.                              | Vérifiez que le cluster n’a pas été supprimé et que l’URI fourni est correct. Lorsque vous ouvrez l’URI dans un navigateur, vous devez voir l’interface utilisateur d’Ambari. Si le cluster se trouve dans un réseau virtuel, l’URI doit être l’URI privé. Pour l’ouvrir, utilisez une machine virtuelle qui fait partie du même réseau virtuel. Pour plus d’informations, consultez [Se connecter directement aux services Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2 300         | La soumission du travail Hadoop a échoué. Travail : ..., Cluster : .../. Error: Une tâche a été annulée. | L’envoi du travail a expiré.                         | Il peut s’agir d’un problème de connectivité générale HDInsight ou d’un problème de connectivité réseau. Tout d’abord, vérifiez que l’interface utilisateur Ambari HDInsight est accessible à partir de n’importe quel navigateur. Vérifiez que vos informations d’identification sont toujours valides. Si vous utilisez le runtime d’intégration auto-hébergé (IR), veillez à utiliser la machine virtuelle ou l’ordinateur où celui-ci est installé. Ensuite, renvoyez le travail à partir de Data Factory. Si le problème persiste, contactez l’équipe Data Factory pour obtenir de l’aide. |
| 2 300         | Non autorisé :   le nom d’utilisateur ou mot de passe Ambari est incorrect.  <br/><br/>Non autorisé :   l’utilisateur administrateur est bloqué et n’a pas accès à Ambari.   <br/><br/>403 - Interdit : L’accès est refusé. | Les informations d’identification fournies pour HDInsight sont incorrectes ou ont expiré. | Corrigez les informations d’identification et redéployez le service lié. Vérifiez tout d’abord que les informations d’identification fonctionnent sur HDInsight en ouvrant l’URI de cluster sur n’importe quel navigateur et en essayant de vous connecter. Si elles ne fonctionnent pas, vous pouvez les réinitialiser à partir du portail Azure. |
| 2300,   2310 | 502 - Le serveur web a reçu une réponse erronée lors de son utilisation en tant que passerelle ou serveur proxy.       <br/>Passerelle incorrecte. | L’erreur provient de HDInsight.                               | L’erreur provient du cluster HDInsight. Pour plus d’informations, consultez [Erreur 502 : Interface utilisateur Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [Erreur 502 : Connexion au serveur Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [Erreur 502 : Connexion au serveur Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html) et [Résolution des erreurs de passerelle incorrecte dans Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502). |
| 2 300         | La soumission du travail Hadoop a échoué. Travail : ..., Cluster : ... Erreur : {\"erreur\" :\" impossible de traiter la requête d’envoi du travail, car le service templeton est occupé avec trop de requêtes d’envoi de travail. Veuillez patienter un certain temps avant de recommencer l’opération. Reportez-vous à la configuration templeton.parallellism.job.submit pour configurer des requêtes simultanées.  <br/><br/>La soumission du travail Hadoop a échoué. Travail : 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, Cluster : `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`.   Erreur : {\"erreur\" : \"java.io.IOException org.apache.hadoop.yarn.exceptions.YarnException: Échec de l’envoi d’application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: La file d’attente root.joblauncher contient déjà 500 applications et n’accepte pas l’envoi de l’application : application_1561147195099_3730\ | Trop de travaux sont envoyés à HDInsight en même temps. | Envisagez de limiter le nombre de travaux simultanés envoyés à HDInsight. Renseignez-vous sur la simultanéité des activités Data Factory si les travaux sont envoyés par la même activité. Modifiez les déclencheurs de sorte que les exécutions de pipeline simultanées soient réparties au fil du temps. Reportez-vous à la documentation HDInsight pour ajuster `templeton.parallellism.job.submit` comme le suggère l’erreur. |
| 2303,   2347 | Échec du travail Hadoop avec le code de sortie « 5 ». Consultez wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr pour plus d’informations.  <br/><br/>L’exécution de Hive a échoué avec le code d’erreur « UserErrorHiveOdbcCommandExecutionFailure ».   Pour plus d’informations, consultez wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out. | Le travail a été envoyé à HDInsight, et il a échoué sur HDInsight. | Le travail a bien été envoyé à HDInsight. Il a échoué sur le cluster. Ouvrez le travail et les journaux dans l’interface utilisateur Ambari HDInsight, ou ouvrez le fichier à partir du stockage, comme le suggère le message d’erreur. Le fichier affiche les détails de l’erreur. |
| 2328         | Une erreur interne de serveur s'est produite lors du traitement de la requête. Renvoyez la requête ou contactez le support technique. | Cette erreur se produit dans HDInsight à la demande.                              | Cette erreur provient du service HDInsight en cas d’échec du provisionnement HDInsight. Contactez l’équipe HDInsight pour lui fournir le nom du cluster à la demande. |
| 2310         | java.lang.NullPointerException                               | Cette erreur se produit lorsque le travail est envoyé à un cluster Spark.      | Cette exception provient de HDInsight. Elle cache le vrai problème. Contactez l’équipe HDInsight pour obtenir de l’aide. Indiquez le nom du cluster et la période de l’exécution d’activité. |
|              | Toutes les autres erreurs                                             |                                                              | Consultez [Résoudre les problèmes avec HDInsight](../hdinsight/hdinsight-troubleshoot-guide.md) et [FAQ HDInsight](https://hdinsight.github.io/). |



## <a name="web-activity"></a>Activité web

| Code d'erreur | Message d’erreur                                                | Description                                                  | Recommandation                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | HttpMethod non valide : '...'.                                    | L’activité web ne prend pas en charge la méthode HTTP spécifiée dans la charge utile d’activité. | Les méthodes HTTP prises en charge sont PUT, POST, GET et DELETE. |
| 2108         | Erreur 500 Serveur non valide                                     | Erreur interne sur le point de terminaison                               | Utilisez Fiddler ou Postman pour vérifier son fonctionnement avec l’URL. |
| 2108         | 401 - Non autorisé.                                             | Authentification valide manquante dans la requête.                      | Le jeton a peut-être expiré. Fournissez une méthode d’authentification valide. Utilisez Fiddler ou Postman pour vérifier son fonctionnement avec l’URL. |
| 2108         | 403 - Interdit.                                                | Autorisations manquantes.                                 | Vérifiez les autorisations des utilisateurs sur la ressource souhaitée. Utilisez Fiddler ou Postman pour vérifier son fonctionnement avec l’URL.  |
| 2108         | 400 Requête incorrecte.                                              | Requête HTTP non valide.                                         | Vérifiez l’URL, le verbe et le corps de la requête. Utilisez Fiddler ou Postman pour valider la requête.  |
| 2108         | 404 Introuvable.                                                | La ressource est introuvable.                                       | Utilisez Fiddler ou Postman pour valider la requête.  |
| 2108         | Service indisponible.                                          | Le service n’est pas disponible.                                       | Utilisez Fiddler ou Postman pour valider la requête.  |
| 2108         | Type de support non pris en charge.                                       | Le type de contenu ne correspond pas au corps de l’activité web.           | Spécifiez le type de contenu qui correspond au format de la charge utile. Utilisez Fiddler ou Postman pour valider la requête. |
| 2108         | La ressource que vous recherchez a été supprimée, son nom a été modifié ou elle est temporairement indisponible. | La ressource n’est pas disponible.                                | Utilisez Fiddler ou Postman pour vérifier le point de terminaison. |
| 2108         | La page que vous recherchez ne peut pas être affichée, car une méthode non valide (verbe HTTP) est utilisée. | Une méthode d’activité web incorrecte a été spécifiée dans la requête.   | Utilisez Fiddler ou Postman pour vérifier le point de terminaison. |
| 2108         | invalid_payload                                              | Le corps de l’activité web est incorrect.                       | Utilisez Fiddler ou Postman pour vérifier le point de terminaison. |

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



