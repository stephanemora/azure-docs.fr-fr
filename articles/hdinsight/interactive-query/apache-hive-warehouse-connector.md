---
title: Intégrer Apache Spark et Apache Hive avec le connecteur de l’entrepôt Hive
description: Découvrez comment intégrer Apache Spark et Apache Hive avec le connecteur de l’entrepôt Hive sur Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/18/2019
ms.openlocfilehash: f5c4b07326bfd469720ab07b522aefb9ed074cbb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014214"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Intégrer Apache Spark et Apache Hive avec le connecteur de l’entrepôt Hive

Apache Hive Warehouse Connector (HWC) est une bibliothèque qui permet de travailler plus facilement avec Apache Spark et Apache Hive en prenant en charge des tâches telles que le déplacement de données entre les trames de données Spark et des tables Hive et également diriger Spark streaming des données dans des tables Hive. Fonctionnement du connecteur de l’entrepôt Hive comme un pont entre Spark et Hive. Il prend en charge de Scala, Java et Python pour le développement.

Le connecteur de l’entrepôt Hive vous permet de tirer parti des fonctionnalités uniques de Hive et Spark pour créer de puissantes applications big data. Apache Hive prend en charge pour les transactions de base de données qui sont atomique, cohérente, isolée et Durable (ACID). Pour plus d’informations sur ACID et les transactions dans Hive, consultez [Transactions Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive propose également des contrôles de sécurité détaillés via Apache Ranger et faible latence Analytical Processing non disponible dans Apache Spark.

Apache Spark, a une API de diffusion en continu structurée qui offre des fonctionnalités de diffusion non disponible dans Apache Hive. À compter avec Hortonworks Data Platform (HDP) 3.0, Apache Spark et Apache Hive ont metastores distinct, ce qui peut compliquer l’interopérabilité. Le connecteur de l’entrepôt Hive facilite l’utilisation simultanée Spark et Hive. La bibliothèque HWC charge les données à partir de démons LLAP vers exécuteurs Spark en parallèle, rendant plus efficaces et évolutives à l’aide d’une connexion de JDBC standard entre Spark et Hive.

![Architecture](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Certaines opérations prises en charge par le connecteur de l’entrepôt Hive sont :

* Qui décrit une table
* Création d’une table de données au format ORC
* Sélection des données de ruche et la récupération d’une trame de données
* Écriture d’une trame de données dans Hive en lot
* Exécution d’une instruction de mise à jour de Hive
* Lecture des données de table à partir de Hive, transformant dans Spark et leur écriture dans une nouvelle table Hive
* Écriture d’un flux de données ou Spark dans Hive à l’aide de HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Installation du connecteur de l’entrepôt Hive

Suivez ces étapes pour configurer le connecteur de l’entrepôt Hive entre un cluster Spark et Interactive Query dans Azure HDInsight :

1. Créer un cluster HDInsight Spark 4.0 à l’aide du portail Azure avec un compte de stockage et un réseau virtuel Azure personnalisé. Pour plus d’informations sur la création d’un cluster dans un réseau virtuel Azure, consultez [ajouter HDInsight à un réseau virtuel existant](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet).
1. Créer un cluster HDInsight Interactive Query (LLAP) 4.0 à l’aide du portail Azure avec le même compte de stockage et le réseau virtuel Azure que le cluster Spark.
1. Copiez le contenu de la `/etc/hosts` fichier sur headnode0 du cluster Interactive Query de la `/etc/hosts` fichier sur le headnode0 de votre cluster Spark. Cette étape permettra à votre cluster Spark résoudre les adresses IP des nœuds de cluster Interactive Query. Afficher le contenu du fichier mis à jour avec `cat /etc/hosts`. La sortie ressemble à ce qui est affiché dans la capture d’écran ci-dessous.

    ![affichage du fichier d’hôtes](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Configurer les paramètres de cluster Spark en procédant comme suit : 
    1. Accédez au portail Azure, sélectionnez des clusters HDInsight, puis cliquez sur le nom de votre cluster.
    1. Sur le côté droit, sous **des tableaux de bord de Cluster**, sélectionnez **Ambari domestique**.
    1. Dans l’interface utilisateur web Ambari, cliquez sur **SPARK2** > **CONFIGS** > **Custom spark2-defaults**.

        ![Configuration de Spark2 Ambari](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Définissez `spark.hadoop.hive.llap.daemon.service.hosts` à la même valeur que la propriété **nom de l’application LLAP** sous **avancé hive interactif env**. Par exemple, `@llap0`

    1. Définissez `spark.sql.hive.hiveserver2.jdbc.url` à la chaîne de connexion JDBC, qui se connecte à Hiveserver2 sur le cluster Interactive Query. La chaîne de connexion pour votre cluster se présente comme URI ci-dessous. `CLUSTERNAME` est le nom de votre cluster Spark et le `user` et `password` paramètres sont définis sur les valeurs correctes pour votre cluster.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > L’URL JDBC doit contenir des informations d’identification pour se connecter à Hiveserver2, y compris un nom d’utilisateur et le mot de passe.

    1. Définissez `spark.datasource.hive.warehouse.load.staging.dir` dans un répertoire intermédiaire compatible avec HDFS approprié. Si vous avez deux clusters différents, le répertoire de transit doit être un dossier dans le répertoire intermédiaire du compte de stockage du cluster LLAP afin que HiveServer2 y a accès. Par exemple, `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` où `STORAGE_ACCOUNT_NAME` est le nom du compte de stockage utilisé par le cluster, et `STORAGE_CONTAINER_NAME` est le nom du conteneur de stockage.

    1. Définir `spark.datasource.hive.warehouse.metastoreUri` avec la valeur de l’URI du cluster Interactive Query metastore. Pour trouver le metastoreUri pour votre cluster LLAP, recherchez le **hive.metastore.uris** propriété dans l’UI Ambari pour votre LLAP cluster sous **Hive** > **avancé**  >  **Général**. La valeur ressemblera à l’URI suivant :

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Définissez `spark.security.credentials.hiveserver2.enabled` à `false` pour le client de fils, déployez le mode.
    1. Définissez `spark.hadoop.hive.zookeeper.quorum` dans le quorum Zookeeper de votre Cluster LLAP. Pour trouver le quorum Zookeeper pour votre cluster LLAP, recherchez le **hive.zookeeper.quorum** propriété dans l’UI Ambari pour votre LLAP cluster sous **Hive** > **avancé**  >  **Site hive avancé**. La valeur ressemblera à la chaîne suivante :

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Pour tester la configuration de votre connecteur entrepôt Hive, suivez les étapes décrites dans la section [qui se connectés et en cours d’exécution de requêtes](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>Utilisation du connecteur de l’entrepôt Hive

### <a name="connecting-and-running-queries"></a>Connexion et l’exécution des requêtes

Vous pouvez choisir entre plusieurs méthodes différentes pour vous connecter à votre cluster Interactive Query et exécuter des requêtes à l’aide du connecteur de l’entrepôt Hive. Méthodes prises en charge incluent les outils suivants :

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* Spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Tous les exemples fournis dans cet article seront exécutées via l’interpréteur de commandes spark.

Pour démarrer une session d’interpréteur de commandes spark, procédez comme suit :

1. Utilisez SSH dans le nœud principal pour votre cluster. Pour plus d’informations sur la connexion à votre cluster avec SSH, consultez [se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Modifier dans le répertoire approprié en tapant `cd /usr/hdp/current/hive_warehouse_connector` ou fournir le chemin d’accès complet à tous les fichiers jar utilisé en tant que paramètres dans la commande d’interpréteur de commandes spark.
1. Entrez la commande suivante pour démarrer l’interpréteur de commandes :

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Vous verrez un message d’accueil et un `scala>` invite où vous pouvez entrer des commandes.

1. Après le démarrage de l’interpréteur de commandes spark, une instance de connecteur de l’entrepôt Hive peut être démarrée à l’aide des commandes suivantes :

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Connexion et l’exécution des requêtes sur des clusters Enterprise Security Package (ESP)

Le Package de sécurité Enterprise (ESP) fournit des fonctionnalités d’entreprise telles que l’authentification basée sur Active Directory, prise en charge multi-utilisateur et le contrôle d’accès en fonction du rôle pour les clusters Apache Hadoop dans Azure HDInsight. Pour plus d’informations sur ESP, consultez [introduction à la sécurité Apache Hadoop avec Enterprise Security Package](../domain-joined/apache-domain-joined-introduction.md).

1. Suivez les étapes initiales 1 et 2 sous [qui se connectés et en cours d’exécution de requêtes](#connecting-and-running-queries).
1. Type `kinit` et connectez-vous avec un utilisateur de domaine.
1. Start-interpréteur de commandes spark avec la liste complète des paramètres de configuration comme indiqué ci-dessous. Toutes les valeurs dans toutes les lettres majuscules entre crochets pointus doivent être spécifié en fonction de votre cluster. Si vous avez besoin connaître les valeurs à entrer pour tous les paramètres ci-dessous, consultez la section sur [le programme d’installation du connecteur de l’entrepôt Hive](#hive-warehouse-connector-setup). :

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Création des trames de données Spark à partir de requêtes Hive

Les résultats de toutes les requêtes à l’aide de la bibliothèque HWC sont retournés comme une trame de données. Les exemples suivants montrent comment créer une requête de base.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Les résultats de la requête sont des trames de données Spark, qui peut être utilisé avec les bibliothèques Spark comme MLIB et SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Écriture de trames de données Spark vers des tables Hive

Spark ne prend en charge l’écriture dans les tables ACID gérées de Hive. À l’aide de HWC, toutefois, vous pouvez écrire n’importe quel tableau de données dans une table Hive. Vous pouvez voir cette fonctionnalité en action dans l’exemple suivant :

1. Créer une table appelée `sampletable_colorado` et spécifiez ses colonnes à l’aide de la commande suivante :

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Filtrer la table `hivesampletable` où la colonne `state` est égal à `Colorado`. Cette requête de la table Hive est retournée comme une trame de données Spark. La trame de données est enregistré dans la table Hive `sampletable_colorado` à l’aide de la `write` (fonction).
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Vous pouvez voir la table résultante dans la capture d’écran ci-dessous.

![afficher la table résultante](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Écritures de diffusion en continu structurées

À l’aide du connecteur de l’entrepôt Hive, vous pouvez utiliser pour écrire des données dans des tables Hive de diffusion en continu de Spark.

Suivez les étapes ci-dessous pour créer un exemple de connecteur de l’entrepôt Hive qui ingère des données à partir d’un flux de données Spark sur le port localhost 9999 dans une table Hive.

1. Ouvrez un terminal sur votre cluster Spark.
1. Commencer le flux de données spark avec la commande suivante :

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Générer des données pour le flux de données Spark que vous avez créé, en procédant comme suit :
    1. Ouvrez un autre terminal sur le même cluster Spark.
    1. Dans l’invite de commandes, tapez `nc -lk 9999`. Cette commande utilise l’utilitaire netcat pour envoyer des données à partir de la ligne de commande pour le port spécifié.
    1. Tapez les mots que vous souhaitez que le flux de Spark pour recevoir, suivi par un retour chariot.
        ![données d’entrée de flux de données spark](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Créer une nouvelle table Hive qui contiendra les données de diffusion en continu. À spark-shell, tapez les commandes suivantes :

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Écrire les données de diffusion en continu dans la table nouvellement créée à l’aide de la commande suivante :

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Le `metastoreUri` et `database` options actuellement doivent être définies manuellement en raison d’un problème connu dans Apache Spark. Pour plus d’informations sur ce problème, consultez [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Vous pouvez afficher les données insérées dans la table avec la commande suivante :

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Sécurisation des données sur des clusters Spark ESP

1. Créer une table `demo` avec des exemples de données en entrant les commandes suivantes :

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Afficher le contenu de la table avec la commande suivante. Avant d’appliquer la stratégie, le `demo` table affiche la colonne complète.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![table de démonstration avant d’appliquer la stratégie ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Appliquer une stratégie qui affiche uniquement les quatre derniers caractères de la colonne de masquage de colonne.  
    1. Accédez à l’interface utilisateur administrateur de Ranger au `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Cliquez sur le service Hive pour votre cluster sous **Hive**.
        ![table de démonstration avant d’appliquer la stratégie ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Cliquez sur le **masquage** onglet, puis **ajouter une nouvelle stratégie** ![liste des stratégies](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Fournissez un nom de la stratégie de votre choix. Sélectionnez la base de données : **Par défaut**, table Hive : **démonstration**, Hive colonne : **nom**, utilisateur : **rsadmin2**, Types d’accès : **sélectionnez**et **Masque partiels : afficher les 4 derniers** à partir de la **sélectionner l’Option masquage** menu. Cliquez sur **Add**.
                ![liste des stratégies](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Affichez le contenu de la table à nouveau. Après avoir appliqué la stratégie ranger, nous pouvons voir uniquement les quatre derniers caractères de la colonne.

    ![table de démonstration après avoir appliqué la stratégie ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser Interactive Query avec HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Exemples de l’interaction avec le connecteur d’entrepôt Hive à l’aide de Zeppelin, Livy, spark-submit et pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
