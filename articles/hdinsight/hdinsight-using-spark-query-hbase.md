---
title: Utiliser Spark pour lire et écrire des données HBase - Azure HDInsight
description: Utilisez le connecteur HBase Spark pour lire et écrire des données d’un cluster Spark sur un cluster HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: e5d9d4f215752d95ee1d676e8a5b126b6d0d3ab2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190620"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Utiliser Apache Spark pour lire et écrire des données Apache HBase

Apache HBase est généralement interrogé soit avec son API de bas niveau (analyses, obtentions et insertions), soit avec une syntaxe SQL en utilisant Apache Phoenix. Apache fournit également le connecteur Apache Spark HBase. Il constitue une alternative pratique et performante pour interroger et modifier des données stockées par HBase.

## <a name="prerequisites"></a>Prérequis

* Deux clusters HDInsight distincts déployés dans le même [réseau virtuel](./hdinsight-plan-virtual-network-deployment.md). Un cluster HBase et un cluster Spark avec Spark 2.1 (HDInsight 3.6) minimum installé. Pour plus d’informations, consultez [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Le schéma d'URI de votre principal espace de stockage de clusters. Ce schéma serait wasb:// pour le Stockage Blob Azure, `abfs://` pour Azure Data Lake Storage Gen2 ou adl:// pour Azure Data Lake Storage Gen1. Si le transfert sécurisé est activé pour le stockage Blob, l’URI sera `wasbs://`.  Voir aussi [transfert sécurisé](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Procédure générale

La procédure à suivre pour permettre à votre cluster Spark d’interroger votre cluster HDInsight est la suivante :

1. Préparer des exemples de données dans HBase.
2. Obtenir le fichier hbase-site.xml à partir de votre dossier de configuration de cluster HBase (/etc/hbase/conf).
3. Placer une copie de hbase-site.xml dans votre dossier de configuration Spark 2 (/etc/spark2/conf).
4. Exécuter `spark-shell` en référençant le connecteur HBase Spark par ses coordonnées Maven dans l’option `packages`.
5. Définir un catalogue qui mappe le schéma de Spark vers HBase.
6. Interagir avec les données HBase à l’aide des API RDD ou DataFrame.

## <a name="prepare-sample-data-in-apache-hbase"></a>Préparer des exemples de données dans Apache HBase

Dans cette étape, vous créez et vous remplissez un tableau dans Apache HBase, que vous pouvez ensuite interroger avec Spark.

1. Utilisez la commande `ssh` pour vous connecter à votre cluster HBase. Modifiez la commande ci-dessous en remplaçant `HBASECLUSTER` par le nom de votre cluster HBase, puis entrez la commande :

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Utilisez la commande `hbase shell` pour démarrer l’interpréteur de commandes interactif HBase. Entrez la commande suivante dans votre connexion SSH :

    ```bash
    hbase shell
    ```

3. Utilisez la commande `create` pour créer une table HBase avec deux familles de colonnes. Entrez la commande suivante :

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Utilisez la commande `put` pour insérer des valeurs dans une colonne et sur une ligne spécifiées d’une table particulière. Entrez la commande suivante :

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. Utilisez la commande `exit` pour arrêter l’interpréteur de commandes interactif HBase. Entrez la commande suivante :

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Copier hbase-site.xml sur le cluster Spark

Copiez le fichier hbase-site.xml du stockage local vers la racine du stockage par défaut de votre cluster Spark.  Modifiez la commande ci-dessous pour l’adapter à votre configuration.  Ensuite, entrez la commande suivante depuis votre session SSH ouverte dans le cluster HBase :

| Valeur de la syntaxe | Nouvelle valeur|
|---|---|
|[Schéma d’URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modifiez-la pour l’adapter à votre stockage.  La syntaxe ci-dessous est pour le stockage d’objets blob doté du transfert sécurisé.|
|`SPARK_STORAGE_CONTAINER`|Remplacez par le nom du conteneur de stockage par défaut utilisé pour le cluster Spark.|
|`SPARK_STORAGE_ACCOUNT`|Remplacez par le nom du compte de stockage par défaut utilisé pour le cluster Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Mettez ensuite fin à votre connexion SSH à votre cluster HBase.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Placer hbase-site.xml sur votre cluster Spark

1. Connectez-vous au nœud principal de votre cluster Spark à l’aide de SSH. Modifiez la commande ci-dessous en remplaçant `SPARKCLUSTER` par le nom de votre cluster Spark, puis entrez la commande :

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Entrez la commande ci-dessous pour copier `hbase-site.xml` du stockage par défaut de votre cluster Spark vers le dossier de configuration Spark 2 sur le stockage local du cluster :

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Exécuter l’interpréteur de commandes Spark en référençant le connecteur HBase Spark

1. Depuis votre session SSH ouverte sur le cluster Spark, entrez la commande ci-dessous pour démarrer un interpréteur de commandes Spark :

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Laissez cette instance de l’interpréteur de commandes Spark ouverte et passez à l’étape suivante.

## <a name="define-a-catalog-and-query"></a>Définir un catalogue et interroger

Dans cette étape, vous définissez un objet de catalogue qui mappe le schéma depuis Apache Spark vers Apache HBase.  

1. Dans votre interpréteur de commandes Spark ouvert, entrez les instructions `import` suivantes :

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Entrez la commande ci-dessous pour définir un catalogue pour la table Contacts que vous avez créée dans HBase :

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

    Le code effectue les actions suivantes :  

     a. Définissez un schéma de catalogue pour la table HBase nommée `Contacts`.  
     b. Identifiez `key` comme RowKey et mappez les noms de colonnes utilisés dans Spark à la famille de colonne, au nom de colonne et au type de colonne utilisés dans HBase.  
     c. RowKey doit également être défini en détail en tant que colonne nommée (`rowkey`), qui a une famille de colonne spécifique `cf` de `rowkey`.  

1. Entrez la commande ci-dessous pour définir une méthode qui fournit un DataFrame autour de votre table `Contacts` dans HBase :

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Créez une instance du DataFrame :

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Interrogez le DataFrame :

    ```scala
    df.show()
    ```

    Vous devez voir deux lignes de données :

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Inscrivez une table temporaire afin de pouvoir interroger la table HBase à l’aide de Spark SQL :

    ```scala
    df.createTempView("contacts")
    ```

1. Émettez une requête SQL par rapport à la table `contacts` :

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Les résultats doivent ressembler à ceci :

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

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

1. Créez une instance de `ContactRecord` et placez-la dans un tableau :

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Enregistrez le tableau de nouvelles données dans HBase :

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Examinez les résultats :

    ```scala  
    df.show()
    ```

    Un résultat similaire à ceci s’affiche normalement :

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Fermez l’interpréteur de commandes Spark en entrant la commande suivante :

    ```scala
    :q
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Connecteur Apache Spark HBase](https://github.com/hortonworks-spark/shc)
