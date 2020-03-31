---
title: Apache Spark et Hive - Hive Warehouse Connector - Azure HDInsight
description: Découvrez comment intégrer Apache Spark et Apache Hive au connecteur d’entrepôt Hive sur Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252408"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Intégrer Apache Spark et Apache Hive au le connecteur d’entrepôt Hive

Le connecteur d’entrepôt Apache Hive Warehouse Connector (HWC) est une bibliothèque qui vous permet de travailler plus facilement avec Apache Spark et Apache Hive en prenant en charge des tâches comme le déplacement de données entre des DataFrames Spark et des tables Hive, et en dirigeant les données de diffusion en continu Spark vers des tables Hive tables. Le connecteur d’entrepôt Hive fonctionne comme un pont entre Spark et Hive. Il prend en charge le développement avec Scala, Java et Python.

Le connecteur d’entrepôt Hive vous permet de profiter des fonctionnalités uniques de Hive et de Spark afin de créer de puissantes applications Big Data. Apache Hive prend en charge les transactions de base de données ACID (Atomiques, Cohérentes, Isolées et Durables). Pour plus d'informations sur ACID et les transactions dans Hive, voir [Transactions Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive offre également des contrôles de sécurité détaillés via Apache Ranger et Low Latency Analytical Processing, des options non disponibles dans Apache Spark.

Apache Spark dispose d'une API de flux structuré qui offre des capacités de diffusion en continu non disponibles dans Apache Hive. À partir de HDInsight 4.0, Apache Spark 2.3.1 et Apache Hive 3.1.0 proposent des metastores distincts, ce qui peut nuire à l'interopérabilité. Le connecteur d'entrepôt Hive facilite l'utilisation simultanée de Spark et de Hive. La bibliothèque HWC charge les données des démons LLAP vers les exécuteurs Spark en parallèle, une solution plus efficace et plus évolutive qu’une connexion JDBC standard de Spark vers Hive.

![Architecture du connecteur d’entrepôt Hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Voici quelques-unes des opérations prises en charge par le connecteur d'entrepôt Hive :

* Description d’une table
* Création d'une table pour les données au format ORC
* Sélection de données Hive et récupération d'un DataFrame
* Écriture par lots d'un DataFrame vers Hive
* Exécution d'une instruction de mise à jour Hive
* Lecture des données d’une table à partir de Hive, transformation de ces données dans Spark, puis leur écriture dans une nouvelle table Hive
* Écriture d'un flux DataFrame ou Spark vers Hive à l'aide de HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Configuration du connecteur d’entrepôt Hive

Suivez ces étapes pour configurer le connecteur d'entrepôt Hive entre un cluster Spark et un cluster Interactive Query dans Azure HDInsight :

### <a name="create-clusters"></a>Créer des clusters

1. Créez un cluster HDInsight Spark **4.0** avec un compte de stockage et un réseau virtuel Azure personnalisé. Pour plus d'informations sur la création d'un cluster dans un réseau virtuel Azure, voir [Ajouter HDInsight à un réseau virtuel existant](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Créez un cluster HDInsight Interactive Query (LLAP) **4.0** avec le même compte de stockage et le même réseau virtuel Azure que pour le cluster Spark.

### <a name="modify-hosts-file"></a>Modifier le fichier hosts

Copiez les informations sur le nœud à partir du fichier `/etc/hosts` sur le nœud principal (headnode0) de votre cluster Interactive Query et concaténez les informations vers le fichier `/etc/hosts` du nœud principal (headnode0) de votre cluster Spark. Cette étape permettra à votre cluster Spark de résoudre les adresses IP des nœuds dans le cluster Interactive Query. Affichez le contenu du fichier mis à jour avec `cat /etc/hosts`. Le résultat final devrait ressembler à la capture d'écran ci-dessous.

![Fichier d’hôte du connecteur d’entrepôt Hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Recueillir des informations préliminaires

#### <a name="from-your-interactive-query-cluster"></a>À partir de votre cluster Interactive Query

1. Accédez à la page Hive Apache Ambari du cluster à l’aide de `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` où `LLAPCLUSTERNAME` est le nom de votre cluster Interactive Query.

1. Accédez à **Advanced** > **General** > **hive.metastore.uris** et notez la valeur. La valeur peut être similaire à : `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

1. Accédez à **Advanced** > **Advanced hive-site** > **hive.zookeeper.quorum** et notez la valeur. La valeur peut être similaire à : `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

#### <a name="from-your-apache-spark-cluster"></a>À partir de votre cluster Apache Spark

1. Accédez à la page Hive Apache Ambari du cluster à l’aide de `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` où `SPARKCLUSTERNAME` est le nom de votre cluster Apache Spark.

1. Accédez à **Advanced** > **Advanced hive-interactive-site** > **hive.llap.daemon.service.hosts** et notez la valeur. La valeur peut être similaire à : `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Configurer les paramètres du cluster Spark

À partir de l’IU web de votre Spark Ambari, accédez à **Spark2** > **CONFIGS** > **Custom spark2-defaults**.

![Apache Ambari - Configuration Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Sélectionnez **Add Property...** (Ajouter une propriété) pour ajouter/mettre à jour ce qui suit :

| Clé | Valeur |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|La valeur que vous avez obtenue précédemment à partir de **hive.llap.daemon.service.hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Définir sur la chaîne de connexion JDBC, qui se connecte à Hiveserver2 sur le cluster Interactive Query. REMPLACEZ `LLAPCLUSTERNAME` par le nom de votre cluster Interactive Query. Remplacez `PWD` par le mot de passe réel.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Définir sur un répertoire de préproduction compatible HDFS approprié. Si vous utilisez deux clusters différents, ce répertoire doit être un dossier dans le répertoire de préproduction du compte de stockage du cluster LLAP afin que HiveServer2 puisse y accéder.  Remplacez `STORAGE_ACCOUNT_NAME` par le nom du compte de stockage utilisé par le cluster et `STORAGE_CONTAINER_NAME` par le nom du conteneur de stockage.|
|`spark.datasource.hive.warehouse.metastoreUri`|La valeur que vous avez obtenue précédemment à partir de **hive.metastore.uris**.|
|`spark.security.credentials.hiveserver2.enabled`|`false` pour le mode de déploiement du client YARN.|
|`spark.hadoop.hive.zookeeper.quorum`|La valeur que vous avez obtenue précédemment à partir de **hive.zookeeper.quorum**.|

Enregistrez les modifications et redémarrez les composants si nécessaire.

## <a name="using-the-hive-warehouse-connector"></a>Utilisation du connecteur d'entrepôt Hive

### <a name="connecting-and-running-queries"></a>Connexion et exécution des requêtes

Vous pouvez choisir entre différentes méthodes pour vous connecter à votre cluster Interactive Query et exécuter des requêtes à l'aide du connecteur d’entrepôt Hive. Les méthodes prises en charge incluent les outils suivants :

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Tous les exemples fournis dans cet article seront exécutés via spark-shell.

Pour démarrer une session spark-shell, procédez comme suit :

1. Connectez-vous avec SSH au nœud principal de votre cluster Apache Spark. Pour plus d’informations sur la connexion à votre cluster via SSH, consultez [Se connecter à HDInsight (Apache Hadoop) avec SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Entrez la commande suivante pour démarrer l’interpréteur de commandes Spark :

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Vous verrez un message de bienvenue et une invite `scala>` dans laquelle vous pourrez entrer des commandes.

1. Après avoir démarré l’interpréteur de commandes de Spark, une instance du connecteur d'entrepôt Hive peut être lancée en utilisant les commandes suivantes :

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Connexion et exécution de requêtes sur des clusters du Pack Sécurité Entreprise (ESP, Enterprise Security Package)

Le Pack Sécurité Entreprise (ESP) vous fournit des fonctionnalités de qualité professionnelle, notamment l’authentification basée sur Active Directory, la prise en charge multi-utilisateur et le contrôle d’accès en fonction du rôle pour les clusters Apache Hadoop dans Azure HDInsight. Pour plus d’informations sur le pack ESP, consultez [Utiliser le Pack Sécurité Entreprise dans HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. Connectez-vous avec SSH au nœud principal de votre cluster Apache Spark. Pour plus d’informations sur la connexion à votre cluster via SSH, consultez [Se connecter à HDInsight (Apache Hadoop) avec SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Tapez `kinit` et connectez-vous avec un utilisateur du domaine.

1. Démarrez spark-shell avec la liste complète des paramètres de configuration, comme indiqué ci-dessous. Toutes les valeurs en majuscules et entre crochets doivent être spécifiées en fonction de votre cluster. Si vous devez connaître les valeurs à saisir pour l'un des paramètres ci-dessous, reportez-vous à la section [Configuration du connecteur d’entrepôt Hive](#hive-warehouse-connector-setup) :

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Création de DataFrames Spark à partir de requêtes Hive

Les résultats de toutes les requêtes utilisant la bibliothèque HWC sont renvoyés sous forme de DataFrame. Les exemples suivants montrent comment créer une requête de base.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Les résultats de la requête sont des DataFrames Spark, qui peuvent être utilisés avec les bibliothèques Spark comme MLIB et SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Écriture de DataFrames Spark dans des tables Hive

Spark ne prend pas en charge nativement l'écriture dans des tables ACID gérées par Hive. En utilisant HWC, cependant, vous pouvez écrire n'importe quel DataFrame dans une table Hive. Vous pouvez voir cette fonctionnalité en action dans l'exemple suivant :

1. Créez une table appelée `sampletable_colorado` et spécifiez ses colonnes en utilisant la commande suivante :

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrez la table `hivesampletable`, où la colonne `state` est égale à `Colorado`. Cette requête de la table Hive est retournée sous la forme d'un DataFrame Spark. Le DataFrame est ensuite enregistré dans la table Hive `sampletable_colorado` à l'aide de la fonction `write`.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Visualisez les résultats avec la commande suivante :

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![Connecteur d’entrepôt Hive - Affichage de la table Hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Écritures à l’aide du streaming structuré

Le connecteur d’entrepôt Hive vous permet d’utiliser le streaming Spark pour écrire des données dans des tables Hive.

Procédez comme suit pour créer un exemple de connecteur d'entrepôt Hive qui ingère les données d'un flux Spark sur le port localhost 9999 dans une table Hive.

1. Suivez les étapes de la section [Connexion et exécution des requêtes](#connecting-and-running-queries).

1. Démarrez le flux Spark à l’aide de la commande suivante :

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Procédez comme suit pour générer les données du flux Spark que vous avez créé :
    1. Ouvrez une deuxième session SSH sur le même cluster Spark.
    1. À l’invite de commandes, tapez `nc -lk 9999`. Cette commande utilise l'utilitaire netcat pour envoyer des données de la ligne de commande au port spécifié.

1. Revenez à la première session SSH et créez une nouvelle table Hive pour contenir les données de streaming. Dans l’interpréteur de commandes de Spark, entrez la commande suivante :

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Puis, écrivez les données de streaming dans la table nouvellement créée à l’aide de la commande suivante :

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Les options `metastoreUri` et `database` doivent actuellement être définies manuellement en raison d'un problème connu dans Apache Spark. Pour plus d'informations sur ce problème, voir [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Revenez à la deuxième session SSH et entrez les valeurs suivantes :

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Revenez à la première session SSH et notez la brève activité. Pour afficher les données, utilisez la commande suivante :

    ```scala
    hive.table("stream_table").show()
    ```

Utilisez **Ctrl + C** pour arrêter netcat sur la deuxième session SSH. Utilisez `:q` pour quitter l’interpréteur de commandes de Spark sur la première session SSH.

### <a name="securing-data-on-spark-esp-clusters"></a>Sécurisation des données sur les clusters ESP Spark

1. Créez une table `demo` avec quelques exemples de données en entrant les commandes suivantes :

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Visualisez le contenu de la table avec la commande suivante. Avant l’application de la stratégie, la table `demo` affiche la colonne complète.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![table de démonstration avant application d’une stratégie Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Appliquez une stratégie de masquage des colonnes qui n'affiche que les quatre derniers caractères de la colonne.  
    1. Accédez à l’interface utilisateur de l’administrateur Ranger à l’adresse `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Cliquez sur le service Hive pour votre cluster sous **Hive**.
        ![Gestionnaire de service Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Cliquez sur l’onglet **Masquage**, puis sur **Ajouter une nouvelle stratégie**

        ![Connecteur de l’entrepôt Hive - Liste des stratégies Ranger Hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Nommez la stratégie. Sélectionnez la base de données : **Default**, Table Hive : **demo**, Colonne Hive : **name**, Utilisateur : **rsadmin2**, Types d’accès : **select**, et **Partial mask: show last 4** dans le menu **Select Masking Option** (Sélectionner l’option de masquage). Cliquez sur **Add**.
                ![Créer une stratégie](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Affichez à nouveau le contenu de la table. Après avoir appliqué la stratégie Ranger, nous ne voyons que les quatre derniers caractères de la colonne.

    ![table de démonstration après application d’une stratégie Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser Interactive Query avec HDInsight](./apache-interactive-query-get-started.md)
* [Exemples d'interaction avec le connecteur d'entrepôt Hive en utilisant Zeppelin, Livy, spark-submit et pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
