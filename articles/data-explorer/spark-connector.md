---
title: Déplacez les données entre les clusters Azure Data Explorer et Spark au moyen du connecteur Azure Data Explorer pour Apache Spark.
description: Cette rubrique vous montre comment déplacer des données entre des clusters Azure Data Explorer et Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 868e9e068244af91e218d906bee115b58906152f
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027917"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Connecteur Azure Data Explorer pour Apache Spark (préversion)

[Apache Spark](https://spark.apache.org/) est un moteur d’analytique unifié pour le traitement des données à grande échelle. Azure Data Explorer est un service d’analytique données rapide et entièrement managé dédié à l’analyse en temps réel de volumes importants de données de streaming. 

Le connecteur Azure Data Explorer pour Spark implémente la source de données et le récepteur de données pour le déplacement des données entre des clusters Azure Data Explorer et Spark, afin d’utiliser leurs deux fonctionnalités. À l'aide d'Azure Data Explorer et d'Apache Spark, vous pouvez rapidement créer des applications évolutives ciblant des scénarios axés sur les données, comme le Machine Learning (ML), l'extraction, la transformation et le chargement (ETL) et Log Analytics. Vous pouvez écrire des données par lot et en mode de diffusion en continu dans Azure Data Explorer.
La fonction de lecture depuis Azure Data Explorer prend en charge le nettoyage de colonne et le pushdown de prédicats, ce qui réduit le volume de données transférées via le filtrage de ces données dans Azure Data Explorer.

Le connecteur Spark Azure Data Explorer est un [projet open source](https://github.com/Azure/azure-kusto-spark) qui peut s’exécuter sur n’importe quel cluster Spark. Le connecteur Azure Data Explorer Spark convertit Azure Data Explorer en un magasin de données valide pour les opérations standard source et de réception telles que l'écriture, la lecture et writeStream. 

> [!NOTE]
> Bien que certains des exemples ci-dessous fassent référence à un cluster Spark [Azure Databricks](https://docs.azuredatabricks.net/), le connecteur Spark Azure Data Explorer ne dépend pas directement de ce cluster, ni d’aucune autre distribution Spark.

## <a name="prerequisites"></a>Conditions préalables requises

* [Créer un cluster et une base de données Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) 
* Créer un cluster Spark
* Installez la bibliothèque du connecteur Azure Data Explorer, ainsi que les bibliothèques répertoriées dans les [dépendances](https://github.com/Azure/azure-kusto-spark#dependencies), notamment les bibliothèques [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) suivantes :
    * [Client de données Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Client d’ingestion Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Bibliothèques prédéfinies pour [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) et le [référentiel Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)

## <a name="how-to-build-the-spark-connector"></a>Comment créer un connecteur Spark

Vous pouvez générer un connecteur Spark à partir de [sources](https://github.com/Azure/azure-kusto-spark), comme détaillé ci-dessous.

> [!NOTE]
> Cette étape est facultative. Si vous utilisez des bibliothèques prédéfinies, accédez à la section [Configuration du cluster Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Configuration requise

* Installation du Kit de développement logiciel (SDK) Java 1.8
* Installation de [Maven 3.x](https://maven.apache.org/download.cgi)
* Apache Spark version 2.4.0 ou version ultérieure

> [!TIP]
> Les versions 2.3.x sont également prises en charge, mais il peut s’avérer nécessaire de modifier certaines dépendances du fichier pom.xml.

Pour les applications Scala/Java utilisant des définitions de projet Maven, liez votre application à l’artefact suivant (la dernière version peut être différente) :

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Commandes de génération

Pour générer un fichier jar et exécuter tous les tests :

```
mvn clean package
```

Pour générer un fichier jar, exécutez tous les tests et installez le fichier jar dans votre référentiel Maven local :

```
mvn clean install
```

Pour en savoir plus, consultez la section relative à [l’utilisation des connecteurs](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configuration du cluster Spark

> [!NOTE]
> Il est recommandé d’utiliser la dernière version de connecteur Spark Azure Data Explorer lorsque vous effectuez les étapes suivantes :

1. Définissez les paramètres suivants du cluster Spark en fonction du cluster Azure Databricks, en utilisant Spark 2.4.4 et Scala 2.11 : 

    ![Paramètres de cluster Databricks](media/spark-connector/databricks-cluster.png)
    
1. Installez la dernière bibliothèque spark-kusto-connector à partir de Maven :

    ![Importation de la bibliothèque Azure Data Explorer](media/spark-connector/db-create-library.png)

1. Vérifiez que toutes les bibliothèques requises sont installées :

    ![Vérification de l’installation des bibliothèques](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Le connecteur Spark Azure Data Explorer vous permet de vous authentifier auprès de Microsoft Azure Active Directory (Azure AD) à l’aide d’une [application Azure AD](#azure-ad-application-authentication), d’un [jeton d’accès Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [de l’authentification des appareils](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (pour les scénarios ne concernant pas la production) ou [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). L’utilisateur doit installer le package azure-keyvault et fournir les informations d’identification à l’application pour accéder à la ressource Key Vault.

### <a name="azure-ad-application-authentication"></a>Authentification de l’application Azure AD

Méthode d’authentification la plus simple et la plus fréquente. Elle est recommandée pour l’utilisation d’un connecteur Spark Azure Data Explorer.

|Propriétés  |Description  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   ID client d’application (client) Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autorité d’authentification Azure AD. ID Azure AD Directory (locataire).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Clé d’application Azure AD pour le client.     |

### <a name="azure-data-explorer-privileges"></a>Privilèges Azure Data Explorer

Les privilèges suivants doivent être accordés sur un cluster Azure Data Explorer :

* Pour la lecture (source de données), l’application Azure AD doit disposer de privilèges de *viewer* sur la base de données cible, ou de privilèges *d’administrateur* sur la table cible.
* Pour l’écriture (récepteur de données), l’application Azure AD doit bénéficier de privilèges *d’ingesteur* sur la base de données cible. Elle doit également bénéficier de privilèges *d’utilisateur* sur la base de données cible pour pouvoir créer des tables. Si la table cible existe déjà, les privilèges *d’administrateur* sur cette dernière peuvent être configurés.
 
Pour en savoir plus sur les principaux rôles Azure Data Explorer, consultez la section relative à [l’autorisation basée sur les rôles](/azure/kusto/management/access-control/role-based-authorization). Pour savoir comment gérer les rôles de sécurité, consultez la section relative à la [gestion des rôles de sécurité](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Récepteur Spark : écriture de données dans Azure Data Explorer

1. Configurez les paramètres du récepteur :

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Écrivez la trame de données Spark dans le cluster Azure Data Explorer en tant que lot :

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Ou utilisez la syntaxe simplifiée :
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Écrivez les données de diffusion en continu :

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Source Spark : lecture des données depuis Azure Data Explorer

1. Lors de la lecture de petites quantités de données, définissez la requête de données :

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Lors de la lecture de grandes quantités de données, le stockage d’objets blob temporaires doit être fourni. Indiquez la clé SAS du conteneur de stockage ou le nom du compte de stockage, la clé du compte et le nom du conteneur. Cette étape est uniquement requise pour la préversion actuelle du connecteur Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Dans l’exemple ci-dessus, nous n’accédons pas à Key Vault au moyen de l’interface du connecteur. Nous avons recours à une méthode plus simple, qui repose sur l’utilisation des secrets de Databricks.

1. Lisez les données depuis Azure Data Explorer :

    ```scala
     val conf3 = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
          KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [connecteur Spark Azure Data Explorer](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Exemple de code](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)

