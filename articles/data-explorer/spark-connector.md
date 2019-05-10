---
title: Utiliser le connecteur de l’Explorateur de données Azure pour Apache Spark pour déplacer des données entre les clusters Spark et de l’Explorateur de données Azure.
description: Cette rubrique vous montre comment déplacer des données entre l’Explorateur de données Azure et d’Apache Spark clusters.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 854e29b67b6e24c583a98b5851bf17551cfcbf61
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441351"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Connecteur de l’Explorateur de données Azure pour Apache Spark (version préliminaire)

[Apache Spark](https://spark.apache.org/) est un moteur analytique unifiée pour le traitement des données à grande échelle. Explorateur de données Azure est un service d’analytique de données entièrement géré pour l’analyse en temps réel sur de grands volumes de données. 

Connecteur de l’Explorateur de données Azure pour Spark implémente la source de données et de récepteur de données pour déplacer des données entre l’Explorateur de données Azure et Spark clusters à utiliser à la fois de leurs fonctionnalités. À l'aide d'Azure Data Explorer et d'Apache Spark, vous pouvez rapidement créer des applications évolutives ciblant des scénarios axés sur les données, comme le Machine Learning (ML), l'extraction, la transformation et le chargement (ETL) et Log Analytics. L’écriture dans l’Explorateur de données Azure est possible dans le lot et le mode de diffusion en continu.
Lecture à partir de l’Explorateur de données Azure prend en charge le nettoyage de colonne et des prédicats, ce qui réduit le volume de données transférées en filtrant les données dans l’Explorateur de données Azure.

Connecteur Spark de l’Explorateur de données Azure est un [projet open source](https://github.com/Azure/azure-kusto-spark) qui peuvent s’exécuter sur n’importe quel cluster Spark.

> [!NOTE]
> Bien que certains des exemples ci-dessous font référence à un [Azure Databricks](https://docs.azuredatabricks.net/) cluster Spark, Data Explorer connecteur Spark-Azure ne prend pas de dépendances directes sur Databricks ou toute autre distribution Spark.

## <a name="prerequisites"></a>Conditions préalables

* [Créer un cluster et une base de données Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) 
* Créer un cluster Spark
* Installer la bibliothèque des connecteurs Explorateur de données Azure et les bibliothèques répertoriées dans [dépendances](https://github.com/Azure/azure-kusto-spark#dependencies) notamment [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) bibliothèques :
    * [Kusto données Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Client d’ingestion Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Prédéfinies pour les bibliothèques [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Comment créer le connecteur Spark

Connecteur Spark peut être généré à partir de [sources](https://github.com/Azure/azure-kusto-spark) comme détaillé ci-dessous.

> [!NOTE]
> Cette étape est facultative. Si vous utilisez des bibliothèques prédéfinis accédez à [le programme d’installation de Spark cluster](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Créer des conditions préalables

* Java 1.8 SDK est installé
* [Maven 3.x](https://maven.apache.org/download.cgi) installé
* Version d’Apache Spark 2.4.0 ou une version ultérieure

> [!TIP]
> 2.3.x versions sont également prises en charge, mais peuvent nécessiter certaines modifications dans les dépendances de fichier pom.xml.

Pour les applications Scala/Java à l’aide de définitions de projet Maven, liez votre application avec l’artefact suivant (version la plus récente peut varier) :

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

Pour générer le fichier jar, exécuter tous les tests et installez le fichier jar dans votre référentiel Maven local :

```
mvn clean install
```

Pour plus d’informations, consultez [l’utilisation du connecteur](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configuration de cluster Spark

> [!NOTE]
> Il est recommandé d’utiliser la dernière version de connecteur Spark de l’Explorateur de données Azure lorsque vous effectuez les étapes suivantes :

1. Définir des paramètres de cluster, en fonction de cluster Azure Databricks à l’aide de Spark 2.4 et Scala 2.11 le Spark suivant : 

    ![Paramètres de cluster Databricks](media/spark-connector/databricks-cluster.png)

1. Importer la bibliothèque de connecteur de l’Explorateur de données Azure :

    ![Bibliothèque d’importation Explorateur de données Azure](media/spark-connector/db-create-library.png)

1. Ajouter des dépendances supplémentaires :

    ![Ajout de dépendances](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > La version java correct pour chaque version de Spark se trouve [ici](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Vérifiez que toutes les bibliothèques sont installées :

    ![Vérifier les bibliothèques installées](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Connecteur Spark de l’Explorateur de données Azure vous permet de s’authentifier avec Azure Active Directory (Azure AD) à l’aide un [application Azure AD](#azure-ad-application-authentication), [jeton d’accès Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [l’authentification des appareils ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (pour les scénarios hors production), ou [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). L’utilisateur doit installer le package de coffre de clés azure et fournir des informations d’identification de l’application pour accéder à la ressource Key Vault.

### <a name="azure-ad-application-authentication"></a>Authentification d’application Azure AD

La plupart des méthode d’authentification simple et courante. Cette méthode est recommandée pour l’utilisation du connecteur Spark de l’Explorateur de données Azure.

|properties  |Description   |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Identificateur d’application (client) Active Directory Azure.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autorité d’authentification Azure AD. ID de l’annuaire AD (locataire) Azure.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Clé d’application Azure AD pour le client.     |

### <a name="azure-data-explorer-privileges"></a>Privilèges de l’Explorateur de données Azure

Sur un Cluster de l’Explorateur de données Azure doivent disposer des privilèges suivants :

* Pour la lecture (source de données), l’application Azure AD doit avoir *visionneuse* des privilèges sur la base de données cible, ou *administrateur* des privilèges sur la table cible.
* Pour l’écriture (récepteur de données), l’application Azure AD doit avoir *évolutif* des privilèges sur la base de données cible. Il doit également avoir *utilisateur* des privilèges sur la base de données cible pour créer des tables. Si la table cible existe déjà, *administrateur* privilèges sur la table cible peuvent être configurés.
 
Pour plus d’informations sur les rôles de principal de l’Explorateur de données Azure, consultez [autorisation basée sur le rôle](/azure/kusto/management/access-control/role-based-authorization). Pour la gestion des rôles de sécurité, consultez [gestion des rôles de sécurité](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Récepteur de Spark : Écriture dans l’Explorateur de données Azure

1. Définir les paramètres de récepteur :

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Écrire la trame de données Spark au cluster de l’Explorateur de données Azure en tant que traitement par lots :

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. Écriture des données en continu :

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Source Spark : Lecture à partir de l’Explorateur de données Azure

1. Lors de la lecture de petites quantités de données, définissez la requête de données :

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. Lors de la lecture de grandes quantités de données, le stockage d’objets blob temporaires doit être fourni. Fournir le nom du conteneur de clé de SAP de conteneur de stockage, ou nom de compte de stockage et clé de compte. Cette étape est uniquement requis pour la version préliminaire actuelle du connecteur Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Dans l’exemple ci-dessus, nous n’accéder au coffre de clé à l’aide de l’interface de connecteur. Ou bien, nous utilisons une méthode plus simple d’utilisation les secrets de Databricks.

1. Lire à partir de l’Explorateur de données Azure :

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
