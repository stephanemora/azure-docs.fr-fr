---
title: Utiliser Spark pour lire et écrire des données HBase - Azure HDInsight
description: Utilisez le connecteur HBase Spark pour lire et écrire des données d’un cluster Spark sur un cluster HBase.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 0ba61dc266add48577c3a382465ecb2cec9d2a05
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188031"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Utiliser Apache Spark pour lire et écrire des données Apache HBase

Apache HBase est généralement interrogé soit avec son API de bas niveau (analyses, obtentions et insertions), soit avec une syntaxe SQL en utilisant Apache Phoenix. Apache fournit également le connecteur Apache HBase Spark, qui est une alternative pratique et performante pour interroger et modifier des données stockées par HBase.

## <a name="prerequisites"></a>Conditions préalables

* Deux distincts au moins des clusters HDInsight, un HBase et un Spark avec Spark 2.1 (HDInsight 3.6) est installé.
* Le cluster Spark doit communiquer directement avec le cluster HBase avec une latence minimale. La configuration recommandée consiste donc à déployer les deux clusters sur le même réseau virtuel. Pour plus d’informations, consultez [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
* Un client SSH. Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
* Le [schéma d’URI](/hdinsight-hadoop-linux-information#URI-and-scheme.md) pour votre stockage principal de clusters. Il s’agit de wasb : / / pour le stockage d’objets Blob Azure, abfs : / / pour Azure Data Lake Storage Gen2 ou adl : / / pour Azure Data Lake Storage Gen1. Si un transfert sécurisé est activé pour le stockage d’objets Blob ou Data Lake Storage Gen2, l’URI serait wasbs : / / ou abfss : / /, respectivement, voir aussi [transfert sécurisé](../storage/common/storage-require-secure-transfer.md).


## <a name="overall-process"></a>Procédure générale

La procédure à suivre pour permettre à votre cluster Spark d’interroger votre cluster HDInsight est la suivante :

1. Préparer des exemples de données dans HBase.
2. Obtenir le fichier hbase-site.xml à partir de votre dossier de configuration de cluster HBase (/etc/hbase/conf).
3. Placer une copie de hbase-site.xml dans votre dossier de configuration Spark 2 (/etc/spark2/conf).
4. Exécuter `spark-shell` en référençant le connecteur HBase Spark par ses coordonnées Maven dans l’option `packages`.
5. Définir un catalogue qui mappe le schéma de Spark vers HBase.
6. Interagir avec les données HBase à l’aide des API RDD ou DataFrame.

## <a name="prepare-sample-data-in-apache-hbase"></a>Préparer des exemples de données dans Apache HBase

Dans cette étape, vous créez et vous remplissez un tableau simple dans Apache HBase, que vous pouvez ensuite interroger avec Spark.

1. Connectez-vous au nœud principal de votre cluster HBase à l’aide de SSH. Pour plus d’informations, consultez [Se connecter à HDInsight à l’aide du protocole SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  Modifiez la commande ci-dessous en remplaçant `HBASECLUSTER` avec le nom de votre cluster HBase, `sshuser` avec le ssh utilisateur le nom du compte, puis entrez la commande.

    ```
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Entrez la commande suivante pour démarrer l’interpréteur de commandes HBase :

        hbase shell

3. Entrez la commande suivante pour créer un `Contacts` table avec les familles de colonnes `Personal` et `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Entrez les commandes ci-dessous pour charger quelques exemples de lignes de données :

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

5. Entrez la commande suivante pour quitter l’interpréteur de commandes HBase :

        exit 

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Copiez hbase-site.XML dans un cluster Spark
Copiez le fichier hbase-site.XML du stockage local à la racine de stockage par défaut de votre cluster Spark.  Modifier la commande ci-dessous afin de refléter votre configuration.  Puis, à partir de votre session SSH ouverte au cluster HBase, entrez la commande :

| Valeur de la syntaxe | Nouvelle valeur|
|---|---|
|[Schéma d’URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modifier pour refléter votre stockage.  La syntaxe ci-dessous est pour le stockage blob doté du transfert sécurisé.|
|`SPARK_STORAGE_CONTAINER`|Remplacez par le nom de conteneur de stockage par défaut utilisé pour le cluster Spark.|
|`SPARK_STORAGE_ACCOUNT`|Remplacez par le nom de compte de stockage par défaut utilisé pour le cluster Spark.|

```
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Placer hbase-site.xml sur votre cluster Spark

1. Connectez-vous au nœud principal de votre cluster Spark à l’aide de SSH.

2. Entrez la commande suivante pour copier `hbase-site.xml` depuis le stockage par défaut de votre cluster Spark dans le dossier de configuration Spark 2 sur le stockage du cluster local :

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Exécuter l’interpréteur de commandes Spark en référençant le connecteur HBase Spark

1. À partir de votre session SSH ouverte au cluster Spark, entrez la commande suivante pour démarrer un interpréteur de commandes spark :

    ```
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Laissez cette instance de l’interpréteur de commandes Spark ouverte et passez à l’étape suivante.

## <a name="define-a-catalog-and-query"></a>Définir un catalogue et interroger

Dans cette étape, vous définissez un objet de catalogue qui mappe le schéma depuis Apache Spark vers Apache HBase.  

1. Dans votre interpréteur de commandes Spark ouvert, entrez les informations suivantes `import` instructions :

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. Entrez la commande suivante pour définir un catalogue pour la table Contacts que vous avez créé dans HBase :

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    Le code effectue les opérations suivantes :  

     a. Définissez un schéma de catalogue pour la table HBase nommée `Contacts`.  
     b. Identifiez `key` comme RowKey et mappez les noms de colonnes utilisés dans Spark à la famille de colonne, au nom de colonne et au type de colonne utilisés dans HBase.  
     c. RowKey doit également être défini en détail en tant que colonne nommée (`rowkey`), qui a une famille de colonne spécifique `cf` de `rowkey`.  

3. Entrez la commande suivante pour définir une méthode qui fournit une trame de données autour de votre `Contacts` table dans HBase :

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. Créez une instance du DataFrame :

    ```scala
    val df = withCatalog(catalog)
    ```  

5. Interrogez le DataFrame :

    ```scala
    df.show()
    ```

6. Vous devez voir deux lignes de données :

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Inscrivez une table temporaire afin de pouvoir interroger la table HBase à l’aide de Spark SQL :

    ```scala
    df.createTempView("contacts")
    ```

8. Émettez une requête SQL par rapport à la table `contacts` :

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. Les résultats doivent ressembler à ceci :

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Insérer de nouvelles données

1. Pour insérer un nouvel enregistrement Contact, définissez une classe `ContactRecord` :

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. Créez une instance de `ContactRecord` et placez-la dans un tableau :

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. Enregistrez le tableau de nouvelles données dans HBase :

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. Examinez les résultats :

    ```scala  
    df.show()
    ```

5. Un résultat similaire à ceci s’affiche normalement :

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Étapes suivantes

* [Connecteur Apache Spark HBase](https://github.com/hortonworks-spark/shc)
