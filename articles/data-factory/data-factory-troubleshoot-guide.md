---
title: Résolution des problèmes d’Azure Data Factory | Microsoft Docs
description: Résolution des problèmes d’Azure Data Factory. Document commun pour toutes les activités de contrôle externes.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshoot
ms.subservice: troubleshoot
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 8d6ab565098e1ea40ede5c650f05e670a1edc7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454236"
---
# <a name="troubleshooting-azure-data-factory"></a>Résolution des problèmes d’Azure Data Factory
Cet article répertorie les questions courantes de résolution des problèmes.

- [Azure Databricks (notebook, jars, python)](#azure-databricks)
- [Azure Data Lake Analytics (U-SQL)](#azure-data-lake-analytics-u-sql)
- [Azure Functions](#azure-functions)
- [Personnalisé (Azure Batch)](#custom-azure-batch)
- [HDInsight (Spark, Hive, MapReduce, Pig, diffusion en continu Hadoop)](#hdinsight-spark-hive-mapreduce-pig-hadoop-streaming)
- [Activité Web](#web-activity)



## <a name="azure-databricks"></a>Azure Databricks
| Code d'erreur | Message d’erreur                                          | Description du problème                             | Correction possible/Action recommandée                            |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Erreur 403                                                    | Le jeton d’accès Databricks a expiré.                         | Par défaut, le jeton d'accès Databricks est valide pendant 90 jours.  Créez un autre jeton et mettez à jour le service lié. |
| 3201           | Champ obligatoire manquant : settings.task.notebook_task.notebook_path | Création incorrecte : le chemin d’accès au notebook n’est pas correctement spécifié. | Spécifiez le chemin d’accès au notebook dans l’activité Databricks. |
| 3201           | Cluster inexistant                                 | Erreur de création : le cluster Databricks n’existe pas ou a été supprimé. | Vérifiez que le cluster Databricks existe. |
| 3201           | URI de fichier python non valide : ... Consultez le guide de l’utilisateur Databricks pour connaître les schémas d’URI pris en charge. | Création incorrecte                                                | Spécifiez les chemins d’accès absolus pour les schémas d’adressage d’espace de travail, ou « dbfs:/folder/subfolder/foo.py » pour les fichiers stockés sur DBFS. |
| 3201           | {0}   Le service lié doit contenir un domaine et accessToken en tant que propriétés requises | Création incorrecte                                                | Vérifiez la [définition du service lié](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}   Le service lié doit spécifier un ID de cluster existant ou de nouvelles informations de cluster pour la création. | Création incorrecte                                                | Vérifiez la [définition du service lié](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Le type de nœud Standard_D16S_v3 n’est pas pris en charge. Types de nœuds pris en charge :   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2 | Création incorrecte                                                | Voir le message d’erreur                                          |
| 3201           | notebook_path non valide : ... Seuls les chemins d’accès absolus sont actuellement pris en charge. Les chemins d’accès doivent commencer par « / ». | Création incorrecte                                                | Voir le message d’erreur                                          |
| 3202           | 1 000 travaux ont déjà été créés dans les dernières 3 600 secondes, dépassant la limite de débit :   1 000 créations de travail pour 3 600 secondes. | Trop de travaux Databricks s’exécutent en une heure.                         | Vérifiez le taux de création de travaux dans tous les pipelines qui utilisent cet espace de travail Databricks.  Si les pipelines ont lancé trop de travaux Databricks dans un agrégat, migrez des pipelines dans un nouvel espace de travail. |
| 3202           | Impossible d’analyser l’objet de la requête : « key » et « value » attendus comme définition pour le champ de mappage JSON base_parameters ; le paramètre fournir est « clé : ... » à la place. | Erreur de création : aucune valeur fournie pour le paramètre         | Inspectez le code JSON de pipeline et vérifiez que tous les paramètres dans baseParameters pour le notebook ont une valeur non vide spécifiée. |
| 3202           | Utilisateur : SimpleUserContext{userId=..., name=user@company.com, orgId=…} n’est pas autorisé à accéder au cluster | L’utilisateur qui a généré le jeton d’accès n’est pas autorisé à accéder au cluster Databricks spécifié dans le service lié. | Vérifiez que l’utilisateur dispose des autorisations requises dans l’espace de travail.   |
| 3203           | Le cluster est dans un état Terminé et n’est pas disponible pour recevoir des travaux. Veuillez corriger le cluster ou réessayez plus tard. | Le cluster a été arrêté.    Pour un cluster interactif, il peut s’agir d’une condition de concurrence. | La meilleure façon d’éviter ce problème consiste à utiliser des clusters de travaux.             |
| 3204           | Échec de l’exécution du travail. Il peut y avoir un nombre quelconque de messages d’erreur, de l’état de cluster inattendu à un message spécifique de l’activité.  Le plus souvent, il n’y a aucun message d’erreur. | N/A                                                          | N/A                                                          |



## <a name="azure-data-lake-analytics-u-sql"></a>Azure Data Lake Analytics (U-SQL)

| Code d’erreur         | Message d’erreur                                                | Description du problème                                          | Correction possible/Action recommandée                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Le jeton d’accès provient d’un locataire incorrect                    | Locataire AAD incorrect                                         | Le principal de service utilisé pour accéder à ADLA appartient à un autre locataire AAD. Créez le principal de service dans le même locataire que le compte ADLA. |
| 2711,   2705,   2704 | Interdit. Échec de la vérification d’ACL. La ressource n’existe pas ou l’utilisateur n’est pas autorisé à effectuer l’opération demandée.<br/><br/>L’utilisateur n’est pas en mesure d’accéder au magasin de données DataLake.  <br/><br/>L’utilisateur n’est pas autorisé à accéder à Data Lake Analytics. | Le principal de service ou le certificat fourni n’a pas accès au fichier dans le stockage. | Vérifiez que le principal de service ou le certificat fourni pour les travaux ADLA a accès à la fois au compte ADLA et au stockage ADLS par défaut correspondant depuis le dossier racine. |
| 2711                 | Impossible de trouver le fichier ou le dossier « Azure Data Lake Store »       | Le chemin d’accès au fichier USQL est incorrect ou les informations d’identification du service lié n’y ont pas accès. | Vérifiez le chemin d’accès et les informations d’identification fournies dans le service lié. |
| 2707                 | Impossible de résoudre le compte d’AzureDataLakeAnalytics. Vérifiez « AccountName » et « DataLakeAnalyticsUri ». | Le compte ADLA dans le service lié est incorrect.                  | Vérifiez que le compte correct est fourni.             |
| 2703                 | ID d'erreur : E_CQO_SYSTEM_INTERNAL_ERROR ou une autre erreur qui commence par « ID d’erreur : » | L’erreur provient d’ADLA.                                    | Toute erreur qui ressemble à l’exemple signifie que le travail a été soumis à ADLA et que le script a échoué. L’enquête doit être effectuée sur ADLA. Si vous ouvrez le portail et accédez au compte ADLA, recherchez le travail à l’aide de l’ID d’exécution d’activité ADF (pas l’ID d’exécution de pipeline). Le travail contiendra plus d’informations sur l’erreur et vous aidera à résoudre les problèmes. Si la résolution n’est pas claire, veuillez contacter l’équipe de support ADLA et fournir l’URL du travail, qui inclut le nom de votre compte et l’ID de travail. |
| 2709                 | Nous ne pouvons pas accepter votre travail pour l’instant. Le nombre maximal de travaux en file d’attente pour votre compte est de 200. | Limitation sur ADLA                                           | Réduisez le nombre de travaux soumis à ADLA en modifiant les déclencheurs ADF et les paramètres de concurrence sur les activités, ou augmenter les limites sur ADLA. |
| 2709                 | Ce travail a été rejeté, car il requiert 24 unités d’allocation, et ce compte dispose d’une stratégie définie par l’administrateur qui empêche un travail d’utiliser plus de 5 unités d’allocation. | Limitation sur ADLA                                           | Réduisez le nombre de travaux soumis à ADLA en modifiant les déclencheurs ADF et les paramètres de concurrence sur les activités, ou augmenter les limites sur ADLA. |



## <a name="azure-functions"></a>Azure Functions

| Code d’erreur | Message d’erreur                           | Description                                                  | Correction possible/Action recommandée                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Le contenu de la réponse n’est pas un JObject valide. | Cela signifie que la fonction Azure qui a été appelée n’a pas renvoyé une charge utile JSON dans la réponse. L’activité de fonction ADF Azure prend uniquement en charge le contenu de la réponse JSON. | Mettez à jour de la fonction Azure pour renvoyer une charge utile JSON valide ; par exemple, une fonction C# peut renvoyer (ActionResult)new<OkObjectResult("{`\"Id\":\"123\"`}"); |
| 3600         | HttpMethod non valide : ...               | Cela signifie que le méthode HTTP spécifiée dans la charge utile d’activité n’est pas prise en charge par l’activité de fonction Azure. | Les méthodes HTTP prises en charge sont les suivantes :  <br/>PUT, POST, GET, DELETE, OPTIONS, HEAD, TRACE |



## <a name="custom-azure-batch"></a>Personnalisé (Azure Batch)
| Code d’erreur | Message d’erreur                                                | Description                                                  | Correction possible/Action recommandée                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2 500         | Exception inattendue et échec de l’exécution.             | Impossible de lancer la commande, ou le programme a renvoyé un code d’erreur. | Vérifiez que l’exécutable existe. Si le programme est lancé, vérifiez les fichiers stdout.txt et stderr.txt téléchargés vers le compte de stockage. Il est conseillé de publier des journaux volumineux dans votre code à des fins de débogage. |
| 2501         | Impossible d’accéder au compte batch d’utilisateur, vérifiez les paramètres du compte batch. | Le nom de pool ou la clé d'accès batch fournis ne sont pas corrects.            | Nécessité de vérifier le nom du pool et la clé d’accès batch dans le service lié. |
| 2502         | Impossible d’accéder au compte de stockage d’utilisateur, vérifiez les paramètres du compte de stockage. | Le nom du compte de stockage ou la clé d'accès fournis ne sont pas corrects.       | Nécessité de vérifier le nom du compte de stockage et la clé d'accès dans le service lié. |
| 2504         | L’opération a renvoyé un code d’état non valide « BadRequest ».     | Trop de fichiers dans folderPath si l’activité est personnalisée.  (La taille totale de resourceFiles ne peut pas être supérieure à 32 768 caractères.) | Supprimez les fichiers inutiles, ou compressez-les et ajoutez une commande de décompression pour les extraire, par exemple : powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe |
| 2505         | Impossible de créer la signature d’accès partagé, sauf si les informations d’identification de clé de compte sont utilisées. | Les activités personnalisées prennent uniquement en charge les comptes de stockage qui utilisent une clé d’accès. | Voir la description                                            |
| 2507         | Le chemin d’accès du dossier n’existe pas ou est vide : ...            | Aucun fichier dans le compte de stockage au chemin spécifié.       | Le paramètre folderPath doit contenir les fichiers exécutables que vous souhaitez exécuter. |
| 2508         | Il existe des fichiers en double dans le dossier de ressources.               | Il existe plusieurs fichiers du même nom dans différents sous-dossiers de folderPath. | Les activités personnalisées aplatissent la structure de dossiers sous folderPath.  Si la structure de dossiers doit être préservée, compressez les fichiers et extrayez-les sur Azure Batch avec une commande de décompression, par exemple : powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe |
| 2509         | L’URL batch n’est pas valide, elle doit être au format Uri.         | L’URL batch doit être semblable à https://mybatchaccount.eastus.batch.azure.com. | Voir la description                                            |
| 2510         | Une erreur s’est produite lors de l’envoi de la requête.               | URL batch non valide                                         | Vérifiez l’URL batch.                                            |

## <a name="hdinsight-spark-hive-mapreduce-pig-hadoop-streaming"></a>HDInsight (Spark, Hive, MapReduce, Pig, diffusion en continu Hadoop)

| Code d’erreur | Message d’erreur                                                | Description                                                  | Correction possible/Action recommandée                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | La soumission du travail Hadoop a échoué. Error: Impossible de résoudre le nom distant. <br/><br/>Le cluster est introuvable. | L’URI de cluster fourni n’est pas valide.                              | Vérifiez que le cluster n’a pas été supprimé et que l’URI fourni est correct. Vous pouvez ouvrir l’URI dans n’importe quel navigateur, et vous devez voir l’UI Ambari. Si le cluster se trouve dans un réseau virtuel, l’URI doit être l’URI privé, et vous devez essayer de l’ouvrir à partir d’une machine virtuelle qui fait partie du même réseau virtuel. Plus d’informations sur le [réseau virtuel dans HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2 300         | La soumission du travail Hadoop a échoué. Travail : ..., Cluster : .../. Error: Une tâche a été annulée. | L’envoi du travail a expiré.                         | Il peut s’agir d’un problème de connectivité générale HDInsight ou d’un problème de connectivité réseau. Vérifiez d’abord que l’interface utilisateur HDInsight Ambari est disponible par le biais de n’importe quel navigateur et que vos informations d’identification sont toujours valides. Veillez à utiliser la machine virtuelle ou l’ordinateur où le runtime d’intégration auto-hébergé est installé le cas échéant. Ensuite, soumettez à nouveau le travail à partir d’ADF. Si le problème persiste, contactez l’équipe ADF pour obtenir de l’aide. |
| 2 300         | Non autorisé :   le nom d’utilisateur ou mot de passe Ambari est incorrect.  <br/><br/>Non autorisé :   l’administrateur utilisateur est bloqué et n’a pas accès à Ambari.   <br/><br/>403    Interdit : l’accès est refusé. | Les informations d’identification fournies pour le HDInsight sont incorrectes ou ont expiré. | Veuillez les corriger et redéployer le service lié. Assurez-vous tout d’abord que les informations d’identification fonctionnent sur HDInsight en ouvrant l’URI de cluster sur n’importe quel navigateur et en essayant de vous connecter. Si elles ne fonctionnent pas, vous pouvez les réinitialiser à partir du portail Azure. |
| 2300,   2310 | 502 - Le serveur web a reçu une réponse non valide lors de son utilisation en tant que passerelle ou serveur proxy.       <br/>Passerelle incorrecte | L’erreur provient de HDInsight.                               | Cette erreur provient du cluster HDInsight. Consultez l’[utilitaire de résolution des problèmes HDInsight](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) pour connaître les erreurs courantes.    <br/>Pour les clusters Spark, cela peut être également dû à [ce facteur](https://hdinsight.github.io/spark/spark-thriftserver-errors.html). <br/><br/>[Lien supplémentaire](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502) |
| 2 300         | La soumission du travail Hadoop a échoué. Travail : ..., Cluster : ... Erreur : {\"erreur\" :\" impossible de traiter la requête d’envoi du travail, car le service templeton est occupé avec trop de requêtes d’envoi de travail. Veuillez patienter un certain temps avant de recommencer l’opération. Reportez-vous à la configuration templeton.parallellism.job.submit pour configurer des requêtes simultanées.\  <br/><br/>La soumission du travail Hadoop a échoué. Travail : 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, Cluster : https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/.   Erreur : {\"erreur\" : \"java.io.IOException org.apache.hadoop.yarn.exceptions.YarnException: Échec de l’envoi d’application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: La file d’attente root.joblauncher contient déjà 500 applications et n’accepte pas l’envoi de l’application : application_1561147195099_3730\ | Trop de travaux sont envoyés à HDInsight en même temps. | Envisagez de limiter le nombre de travaux simultanés envoyés à HDI. Reportez-vous à la simultanéité des activités ADF s’ils sont envoyés par la même activité. Modifiez les déclencheurs de sorte que les exécutions de pipeline simultanées soient réparties au fil du temps. Consultez également les documents HDInsight afin d’ajuster « templeton.parallellism.job.submit » comme l’indique l’erreur. |
| 2303,   2347 | Échec du travail Hadoop avec le code de sortie « 5 ». Consultez wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr pour plus d’informations.  <br/><br/>L’exécution de Hive a échoué avec le code d’erreur « UserErrorHiveOdbcCommandExecutionFailure ».   Consultez wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out pour plus d’informations. | Le travail a été envoyé à HDInsight, et il a échoué sur HDInsight. | Le travail a été envoyé à HDInsight. Il a échoué sur le cluster. Ouvrez le travail sur l’interface utilisateur d’Ambari HDInsight et ouvrez les journaux à cet endroit, ou ouvrez le fichier à partir du stockage comme indiqué dans le message d’erreur. Les détails de l’erreur se trouvent dans ce fichier. |
| 2328         | Une erreur interne de serveur s'est produite lors du traitement de la requête. Relancez la requête ou contactez le support technique. | Se produit sur HDInsight à la demande.                              | Cette erreur provient du service HDInsight en cas d’échec de l’approvisionnement de HDInsight. Veuillez contacter l’équipe HDInsight et leur fournir le nom du cluster à la demande. |
| 2310         | java.lang.NullPointerException                               | Un erreur s’est produite lors de l’envoi du travail au cluster Spark.      | Cette exception provient de HDInsight et masque le problème réel.   Veuillez contacter l’équipe HDInsight pour obtenir de l’aide et leur fournir le nom du cluster, ainsi que la période d’activité de l’exécution. |
|              | Toutes les autres erreurs                                             |                                                              | Reportez-vous à l’[utilitaire de résolution des problèmes HDInsight](../hdinsight/hdinsight-troubleshoot-guide.md) et à la [FAQ sur HDInsight](https://hdinsight.github.io/). |



## <a name="web-activity"></a>Activité web

| Code d’erreur | Message d’erreur                                                | Description                                                  | Correction possible/Action recommandée                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | HttpMethod non valide : ...                                    | Cela signifie que le méthode HTTP spécifiée dans la charge utile d’activité n’est pas prise en charge par l’activité web. | Les méthodes HTTP prises en charge sont les suivantes : <br/>PUT, POST, GET, DELETE |
| 2108         | Erreur 500 Serveur non valide                                     | Erreur interne sur le point de terminaison                               | Vérifiez la fonctionnalité sur l’URL (avec Fiddler/Postman) : [Comment utiliser Fiddler pour créer une session HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 401 Non autorisé                                             | Authentification valide manquante dans la requête                      | Fournissez une méthode d’authentification valide (le jeton a peut-être expiré).   <br/><br/>Vérifiez la fonctionnalité sur l’URL (avec Fiddler/Postman) : [Comment utiliser Fiddler pour créer une session HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 403 Interdit                                                | Autorisations requises manquantes                                 | Vérifiez les autorisations des utilisateurs sur la ressource souhaitée.   <br/><br/>Vérifiez la fonctionnalité sur l’URL (avec Fiddler/Postman) : [Comment utiliser Fiddler pour créer une session HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 400 Requête incorrecte                                              | Requête HTTP non valide                                         | Vérifiez l’URL, le verbe et le corps de la requête.   <br/><br/>Utilisez Fiddler/Postman pour valider la requête : [Comment utiliser Fiddler pour créer une session HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 404 Introuvable                                                | Ressource introuvable                                       | Utilisez Fiddler/Postman pour valider la requête : [Comment utiliser Fiddler pour créer une session HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Service indisponible                                          | Le service est indisponible                                       | Utilisez Fiddler/Postman pour valider la requête : [Comment utiliser Fiddler pour créer une session HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Type de support non pris en charge                                       | Content-Type incompatible avec le corps d’activité Web           | Spécifiez la valeur Content-Type appropriée qui correspond au format de charge utile. Utilisez Fiddler/Postman pour valider la requête : [Comment utiliser Fiddler pour créer une session HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | La ressource que vous recherchez a été supprimée, son nom a été modifié ou elle est temporairement indisponible. | La ressource n’est pas disponible.                                | Utilisez Fiddler/Postman pour vérifier le point de terminaison : [Comment utiliser Fiddler pour créer une session HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | La page que vous recherchez ne peut pas être affichée, car une méthode non valide (verbe HTTP) est utilisée. | Méthode d’activité web incorrecte spécifiée dans la requête   | Utilisez Fiddler/Postman pour vérifier le point de terminaison : [Comment utiliser Fiddler pour créer une session HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | invalid_payload                                              | Le corps de l’activité web est incorrect.                       | Utilisez Fiddler/Postman pour vérifier le point de terminaison : [Comment utiliser Fiddler pour créer une session HTTP](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |

#### <a name="how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application"></a>Comment utiliser Fiddler pour créer une session HTTP de l’application web surveillée

1. Téléchargez et installez [Fiddler](https://www.telerik.com/download/fiddler)

2. Si votre application web utilise HTTPS :

   1. Ouvrez Fiddler.

   2. Accédez à **Tools > Fiddler Options** (Outils > Options Fiddler) et sélectionnez les mêmes options que dans la capture d’écran ci-dessous. 

      ![fiddler-options](media/data-factory-troubleshoot-guide/fiddler-options.png)

3. Si votre application utilise des certificats SSL, vous devez également ajouter le certificat de Fiddler à votre appareil.

4. Pour ajouter le certificat de Fiddler pour votre appareil, accédez à **Tools** > **Fiddler Options** > **HTTPS** > **Actions** > **Export Root Certificate to Desktop** (Outils > Options Fiddler > Actions > Exporter le certificat racine sur le bureau) pour obtenir le certificat de Fiddler.

5. Désactivez la capture pour que le cache du navigateur puisse être vidé afin de démarrer une nouvelle trace.

6. 1. Accédez à **File** > **Capture Traffic** (Fichier > Capturer le trafic) ou appuyez sur **F12**.
   2. Videz le cache de votre navigateur afin que tous les éléments présents soient supprimées et doivent être téléchargés à nouveau.

7. Créez la requête : 

8. 1. Cliquez sur l’onglet Composer (Éditeur).
   2. Définissez la méthode HTTP et l’URL.
   3. Ajoutez des en-têtes et le corps de la requête si nécessaire.
   4. Cliquez sur Execute (Exécuter).

9. Commencez à capturer le trafic à nouveau et terminez la transaction problématique sur votre page.

10. Enfin, accédez à **File** > **Save** > **All Sessions** (Fichier > Enregistrer > Toutes les sessions).

Pour plus d’informations sur Fiddler, cliquez [ici](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une aide supplémentaire lors de la recherche d’une solution à votre problème, voici d’autres ressources auxquelles vous pouvez recourir.

*  [Blogs](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter](https://twitter.com/hashtag/DataFactory)



