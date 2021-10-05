---
title: Utiliser Metastore Hive externe pour le pool Azure Synapse Spark
description: En savoir plus sur la configuration de Metastore Hive externe pour le pool Azure Synapse Spark.
keywords: metastore Hive externe, partager, Synapse
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.author: yanacai
author: yanancai
ms.date: 09/08/2021
ms.openlocfilehash: 9a2d822d84d6356fbf34b45e24501e4426f1b06d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700519"
---
# <a name="use-external-hive-metastore-for-synapse-spark-pool-preview"></a>Utiliser Metastore Hive externe pour le pool Synapse Spark Pool (préversion)

Azure Synapse Analytics permet aux pools Apache Spark dans le même espace de travail de partager un metastore compatible HMS (Hive Metastore Service) managé en tant que catalogue. Lorsque les clients veulent conserver le catalogue Hive en dehors de l’espace de travail et partager des objets de catalogue avec d’autres moteurs de calcul en dehors de l’espace de travail, tels que HDInsight et Azure Databricks, ils peuvent se connecter à un Metastore Hive externe. Dans cet article, vous allez apprendre à connecter Synapse Spark à un Metastore Apache Hive. 

## <a name="supported-hive-metastore-versions"></a>Versions de metastore Hive prises en charge

La fonctionnalité fonctionne avec Spark 2.4 et Spark 3.0. La table suivante indique les versions prises en charge du service metastore Hive (HMS) pour chaque version Spark.


|Version de Spark|HMS 1.2.X|HMS 2.1.X|HMS 2.3.x|HMS 3.1.X|
|--|--|--|--|--|
|2.4|Oui|Oui|Oui|Non|
|3|Oui|Oui|Oui|Oui|

## <a name="set-up-hive-metastore-linked-service"></a>Configurer le service lié metastore Hive

> [!NOTE]
> Seul Azure SQL Database est pris en charge en tant que metastore Hive externe.

Suivez les étapes ci-dessous pour configurer un service lié au metastore Hive externe dans l’espace de travail Synapse.

1.  Ouvrez Synapse Studio, accédez à **Gérer > Services liés** à gauche, cliquez sur **Nouveau** pour créer un nouveau service lié.

    :::image type="content" source="./media/use-external-metastore/set-up-hive-metastore-linked-service.png" alt-text="Configurer un service lié metastore Hive" border="true":::

2.  Choisissez **Azure SQL Database**, cliquez sur **Continuer**.

3.  Entrez le **Nom** du service lié. Enregistrez le nom du service lié. Ces informations seront utilisées pour configurer Spark dans un instant.

4.  Vous pouvez sélectionner Azure SQL Database pour le metastore Hive externe à partir de la liste des abonnements Azure ou entrer les informations manuellement.

5.  Actuellement, nous prenons uniquement en charge l’authentification SQL comme type d’authentification. Entrez un **Nom d’utilisateur** et un **Mot de passe** pour configurer la connexion.

6.  **Testez la connexion** pour vérifier le nom d’utilisateur et le mot de passe.

7.  Cliquez sur **Créer** pour créer le service lié. 

### <a name="test-connection-and-get-the-metastore-version-in-notebook"></a>Tester la connexion et récupérer la version du metastore dans le notebook
Certains paramètres de règle de sécurité réseau peuvent bloquer l’accès du pool Spark à la base de données du metastore Hive externe. Avant de configurer le pool Spark, exécutez le code ci-dessous dans n’importe quel notebook du pool Spark pour tester la connexion à la base de données du metastore Hive externe. 

Vous pouvez également obtenir votre version de metastore Hive à partir des résultats de sortie. La version de metastore Hive sera utilisée dans la configuration Spark.

```
%%spark 
import java.sql.DriverManager 
/** this url could be copied from Azure portal > Azure SQL database > Connection strings > JDBC **/ 
val url = s"jdbc:sqlserver://{your_servername_here}.database.windows.net:1433;database={your_database_here};user={your_username_here};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;" 
try { 
    val connection = DriverManager.getConnection(url) 
    val result = connection.createStatement().executeQuery("select t.SCHEMA_VERSION from VERSION t") 
    result.next(); 
    println(s"Successful to test connection. Hive metastore version is ${result.getString(1)}") 
} catch { 
    case ex: Throwable =>println(s"Failed to establish connection:\n $ex") 
}  
```

## <a name="configure-spark-to-use-the-external-hive-metastore"></a>Configurer Spark pour utiliser le metastore Hive externe
Après avoir créé avec succès le service lié au metastore Hive externe, vous devez configurer quelques configurations dans Spark pour utiliser le metastore Hive externe. Vous pouvez configurer la configuration au niveau du pool Spark ou au niveau de la session Spark. 

Voici les configurations et les descriptions :

> [!NOTE]
> La version par défaut de metastore Hive est 2.3. Vous n’avez pas besoin de définir `spark.sql.hive.metastore.version` et `spark.sql.hive.metastore.jars` si votre version de metastore Hive est 2.3. Seul `spark.hadoop.hive.synapse.externalmetastore.linkedservice.name` est nécessaire.

|Configuration Spark|Description|
|--|--|
|`spark.sql.hive.metastore.version`|Versions prises en charge : <ul><li>`1.2`</li><li>`2.1`</li><li>`3.1`</li></ul> Veillez à utiliser les deux premières parties sans la troisième partie|
|`spark.sql.hive.metastore.jars`|<ul><li>Version 1.2 : `/opt/hive-metastore/lib-1.2/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>Version 2.1 : `/opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>Version 3.1 : `/opt/hive-metastore/lib-3.1/*:/usr/hdp/current/hadoop-client/lib/*`</li></ul>|
|`spark.hadoop.hive.synapse.externalmetastore.linkedservice.name`|Nom de votre service lié créé dans Azure SQL Database.|

### <a name="configure-spark-pool"></a>Configurer le pool Spark 
Lors de la création du pool Spark, sous l’onglet **Paramètres supplémentaires**, placez les configurations ci-dessous dans un fichier texte et chargez-les dans la section **Configuration Apache Spark**. Vous pouvez également utiliser le menu contextuel d’un pool Spark existant, choisir la configuration Apache Spark pour ajouter ces configurations.

   :::image type="content" source="./media/use-external-metastore/config-spark-pool.png" alt-text="Configurer le pool Spark":::

Mettez à jour la version du metastore et le nom du service lié, puis enregistrez les configurations ci-dessous dans un fichier texte pour la configuration du pool Spark :

```
spark.sql.hive.metastore.version <your hms version, Make sure you use the first 2 parts without the 3rd part>
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name <your linked service name to Azure SQL DB>
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*
```

Voici un exemple de la version 2.1 du metastore avec un service lié nommé HiveCatalog21 :

```
spark.sql.hive.metastore.version 2.1
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name HiveCatalog21
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*
```

### <a name="configure-a-spark-session"></a>Configurer une session Spark
Si vous ne souhaitez pas configurer votre pool Spark, vous pouvez également configurer la session Spark dans le notebook à l’aide de la commande %%configurer magic. Voici le code. La même configuration peut également être appliquée à un programme de traitement par lots Spark. 

```
%%configure -f
{
    "conf":{
        "spark.sql.hive.metastore.version":"<your linked service name to Azure SQL DB>",
        "spark.hadoop.hive.synapse.externalmetastore.linkedservice.name":"<your linked service name to Azure SQL DB>",
        "spark.sql.hive.metastore.jars":"/opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*"
    }
}
```

### <a name="run-queries-to-verify-the-connection"></a>Exécuter les requêtes pour vérifier la connexion
Après tous ces paramètres, essayez de répertorier les objets de catalogue en exécutant la requête ci-dessous dans le notebook Spark pour vérifier la connectivité au metastore Hive externe.
```python
spark.sql("show databases").show()
```

## <a name="set-up-storage-connection"></a>Configurer une connexion de stockage
Le service lié à la base de données metastore Hive fournit simplement l’accès aux métadonnées du catalogue Hive. Pour interroger les tables existantes, vous devez configurer la connexion au compte de stockage qui stocke également les données sous-jacentes de vos tables Hive.

### <a name="set-up-connection-to-adls-gen-2"></a>Configurer la connexion à ADLS Gen 2
#### <a name="workspace-primary-storage-account"></a>Compte de stockage principal de l’espace de travail
Si les données sous-jacentes de vos tables Hive sont stockées dans le compte de stockage principal de l’espace de travail, vous n’avez pas besoin de paramètres supplémentaires. Elle fonctionnera tant que vous avez suivi les instructions de configuration du stockage lors de la création de l’espace de travail.

#### <a name="other-adls-gen-2-account"></a>Autre compte ADLS Gen 2
Si les données sous-jacentes de vos catalogues Hive sont stockées dans un autre compte ADLS Gen 2, vous devez vous assurer que les utilisateurs qui exécutent des requêtes Spark disposent du rôle **Contributeur de données blob de Stockage** sur le compte de stockage ADLS Gen2. 

### <a name="set-up-connection-to-blob-storage"></a>Configurer la connexion au Stockage Blob
Si les données sous-jacentes de vos tables Hive sont stockées dans le compte de stockage Blob Azure, configurez la connexion en suivant les étapes ci-dessous :

1.  Ouvrez Synapse Studio, accédez à **Données > onglet Lié > bouton Ajouter** > **Se connecter à des données externes**.

    :::image type="content" source="./media/use-external-metastore/connect-to-storage-account.png" alt-text="Se connecter à un compte de stockage" border="true":::

2.  Sélectionnez **Stockage Blob Azure**, puis cliquez sur **Continuer**.
3.  Entrez le **Nom** du service lié. Enregistrez le nom du service lié. Ces informations seront ultérieurement utilisées dans la configuration de session Spark.
4.  Sélectionnez le compte de Stockage Blob Azure. Vérifiez que la méthode d’authentification est **Clé de compte**. Actuellement, le pool Spark peut uniquement accéder à un compte de stockage Blob via une clé de compte.
5.  **Testez la connexion** et cliquez **Créer**.
6.  Après avoir créé le service lié au compte de stockage Blob, lorsque vous exécutez des requêtes Spark, veillez à exécuter le code Spark ci-dessous dans le notebook pour accéder au compte de stockage Blob pour la session Spark. En savoir plus sur la raison pour laquelle vous devez le faire [ici](./apache-spark-secure-credentials-with-tokenlibrary.md).

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

Après avoir configuré des connexions de stockage, vous pouvez interroger les tables existantes dans le metastore Hive.

## <a name="known-limitations"></a>Limitations connues

- L’Explorateur d’objets de Synapse Studio continue à afficher des objets dans le metastore Synapse managé au lieu du HMS externe, nous améliorons l’expérience de ce processus.
- [SQL <-> synchronisation spark](../sql/develop-storage-files-spark-tables.md) ne fonctionne pas en utilisant HMS externe.  
- Seul Azure SQL Database est pris en charge en tant que base de données du metastore Hive. Seule l’autorisation SQL est pris en charge.
- Actuellement, Spark ne fonctionne que les tables Hive externes et les tables Hive managées non transactionnelles/non ACID. Il ne prend pas en charge les tables transactionnelles/ACID Hive.
- L’intégration d’Apache Ranger n’est pas prise en charge à ce stade.

## <a name="troubleshooting"></a>Résolution des problèmes
### <a name="see-below-error-when-querying-a-hive-table-with-data-stored-in-blob-storage"></a>Consultez l’erreur ci-dessous lors de l’interrogation d’une table Hive avec des données stockées dans le Stockage Blob
```
Py4JJavaError : An error occurred while calling o241.load. : org.apache.hadoop.fs.azure.AzureException: org.apache.hadoop.fs.azure.AzureException: No credentials found for account demohdicatalohdistorage.blob.core.windows.net in the configuration, and its container demohdicatalog-2021-07-15t23-42-51-077z is not accessible using anonymous credentials. Please check if the container exists first. If it is not publicly available, you have to provide account credentials.
```

Lorsque vous utilisez l’authentification par clé pour votre compte de stockage via le service lié, vous devez effectuer une étape supplémentaire pour obtenir le jeton pour la session Spark. Exécutez le code ci-dessous pour configurer votre session Spark avant d’exécuter la requête. En savoir plus sur la raison pour laquelle vous devez le faire ici.

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

### <a name="see-below-error-when-query-a-table-stored-in-adls-gen2-account"></a>Voir l’erreur ci-dessous lors de la requête d’une table stockée dans un compte ADLS Gen2
```
Py4JJavaError : An error occurred while calling o305.load. : Operation failed: "This request is not authorized to perform this operation using this permission.", 403, HEAD
```

Cela peut se produire si l’utilisateur qui exécute la requête Spark ne dispose pas de suffisamment d’accès au compte de stockage sous-jacent. Assurez-vous que les utilisateurs qui exécutent des requêtes Spark disposent du rôle de **Contributeur de données blob de stockage** sur le compte de stockage ADLS Gen2. Cette étape peut être effectuée ultérieurement après la création du service lié.

### <a name="hms-schema-related-settings"></a>Paramètres liés au schéma HMS 
Pour éviter de modifier le schéma / la version back-end HMS, les configurations Hive suivantes sont définies par le système par défaut : 
```
spark.hadoop.hive.metastore.schema.verification true 
spark.hadoop.hive.metastore.schema.verification.record.version false 
spark.hadoop.datanucleus.fixedDatastore true 
spark.hadoop.datanucleus.schema.autoCreateAll false 
```

Si votre version HMS est 1.2.1 ou 1.2.2, il y a un problème dans Hive qui requiert uniquement 1.2.0 si vous définissez spark.hadoop.hive.metastore.schema.verification sur true. Notre suggestion est soit de modifier votre version HMS en 1.2.0, soit de remplacer les deux configurations suivantes pour contourner :

```
spark.hadoop.hive.metastore.schema.verification false 
spark.hadoop.hive.synapse.externalmetastore.schema.usedefault false
```

Si vous devez migrer votre version HMS, nous vous recommandons d’utiliser l'[outil de schéma de ruche](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool). Et si le HMS a été utilisé par les clusters HDInsight, nous vous suggérons d’utiliser la [version fournie par HDI](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-migrate-workloads). 

### <a name="when-sharing-the-metastore-with-hdinsight-40-spark-clusters-i-cannot-see-the-tables"></a>Lors du partage du metastore avec des clusters HDInsight 4.0 Spark, je ne peux pas voir les tables
Si vous souhaitez partager le catalogue Hive avec un cluster Spark dans HDInsight 4.0, vérifiez que votre propriété `spark.hadoop.metastore.catalog.default` dans Synapse Spark est alignée sur la valeur de HDInsight Spark. La valeur par défaut est `Spark`.

### <a name="when-sharing-the-hive-metastore-with-hdinsight-40-hive-clusters-i-can-list-the-tables-successfully-but-only-get-empty-result-when-i-query-the-table"></a>Lors du partage du metastore Hive avec des clusters HDInsight 4.0 Hive, je peux répertorier les tables avec succès, mais je n’obtiens qu’un résultat vide lorsque j’interroge la table
Comme mentionné dans les limitations, le pool de Synapse Spark ne prend en charge que les tables Hive externes et les tables managées non transactionnelles/ACID et ne prend pas actuellement en charge les tables ACID/transactionnelles. Par défaut, dans les clusters HDInsight 4.0 Hive, toutes les tables managées sont créées en tant que tables ACID/transactionnelles par défaut, c’est pourquoi vous recevez des résultats vides lors de l’interrogation de ces tables. 