---
title: 'Tutoriel : Effectuer des opérations d’extraction, de transformation et de chargement (ETL) à l’aide d’Apache Hive sur Azure HDInsight'
description: Découvrez comment extraire des données d’un jeu de données CSV brutes, les transformer dans Hive sur HDInsight, puis charger les données transformées dans la base de données Azure SQL à l’aide de Sqoop.
services: storage
documentationcenter: ''
author: jamesbak
manager: jahogg
tags: azure-portal
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: jamesbak
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: 4f71a4e0570695abbe580ffc16b3361d5cd71ac1
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136484"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>Tutoriel : Extraire, transformer et charger des données à l’aide d’Apache Hive sur Azure HDInsight

Dans ce tutoriel, vous allez utiliser un fichier de données brutes CSV, l’importer dans un cluster HDInsight, puis transformer les données à l’aide d’Apache Hive sur Azure HDInsight. Une fois les données transformées, chargez-les dans une base de données Azure SQL à l’aide d’Apache Sqoop. Cet article utilise des données de vol accessibles au public.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Télécharger l’exemple des données de vol
> * Charger des données vers un cluster HDInsight
> * Transformer les données à l’aide de Hive
> * Créer une table dans la base de données Azure SQL
> * Utiliser Sqoop pour exporter des données vers la base de données Azure SQL


L’illustration suivante montre un flux d’application ETL standard.

![Opération ETL à l’aide d’Apache Hive sur Azure HDInsight](./media/tutorial-extract-transform-load-hive/hdinsight-etl-architecture.png "Opération ETL à l’aide d’Apache Hive sur Azure HDInsight")

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* **Cluster Hadoop Linux sur HDInsight**. Consultez [Configurer des clusters dans HDInsight avec Hadoop, Spark, Kafka et bien plus encore](./quickstart-create-connect-hdi-cluster.md) pour savoir comment créer un cluster HDInsight basé sur Linux.

* **Base de données SQL Azure**. Vous allez utiliser une base de données SQL Azure comme magasin de données cible. Si vous n’avez pas de base de données SQL, consultez [Créer une base de données Azure SQL dans le portail Azure](../../sql-database/sql-database-get-started.md).

* **Azure CLI 2.0**. Si vous n’avez pas installé l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour connaître les étapes à suivre.

* **Un client SSH**. Pour en savoir plus, consultez la page [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur Azure HDInsight version 3.4 ou ultérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="download-the-flight-data"></a>Téléchargement des données de vol

1. Accédez à [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].

2. Sur la page, sélectionnez les valeurs suivantes :

   | NOM | Valeur |
   | --- | --- |
   | Filtre année |2013 |
   | Filtre période |Janvier |
   | Champs |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Effacez tous les autres champs

3. Sélectionnez **Télécharger**. Vous obtenez un fichier .zip avec les champs de données que vous avez sélectionnés.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Charger des données vers un cluster HDInsight

De nombreuses méthodes permettent de charger des données vers l’espace de stockage associé à un cluster HDInsight. Dans cette section, vous utilisez `scp` pour charger des données. Pour en savoir plus sur les autres façons de charger des données, consultez [Utilisation de Distcp pour copier des données entre les objets blob de stockage Azure et la préversion de Data Lake Storage Gen2](use-distcp.md).

1. Ouvrez une invite de commandes et utilisez la commande suivante pour charger le fichier .zip sur le nœud principal du cluster HDInsight :

    ```bash
    scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
    ```

    Remplacez *FILE_NAME* par le nom du fichier .zip. Remplacez *SSH_USER_NAME* par la connexion SSH pour le cluster HDInsight. Remplacez *CLUSTER_NAME* par le nom du cluster HDInsight.

   > [!NOTE]
   > Si vous utilisez un mot de passe pour authentifier votre connexion SSH, vous êtes invité à l’indiquer. Si vous utilisez une clé publique, vous pouvez avoir besoin d’utiliser le paramètre `-i` et de spécifier le chemin de la clé privée correspondante. Par exemple : `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`.

2. À la fin du chargement, connectez-vous au cluster par l’intermédiaire de SSH. À l’invite de commandes, entrez la commande suivante :

    ```bash
    ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
    ```

3. Utilisez la commande suivante pour décompresser le fichier .zip :

    ```bash
    unzip <FILE_NAME>.zip
    ```

    Cette commande extrait un fichier *.csv* d’environ 60 Mo.

4. Utilisez les commandes suivantes pour créer un répertoire, puis copiez le fichier *.csv* dans le répertoire :

    ```bash
    hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
    hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
    ```

5. Créez le système de fichiers Data Lake Storage Gen2.

    ```bash
    hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformer des données à l’aide d’une requête Hive

De nombreuses méthodes permettent d’exécuter un travail Hive sur un cluster HDInsight. Dans cette section, vous allez utiliser Beeline pour exécuter un travail Hive. Pour plus d’informations sur les autres méthodes d’exécution d’un travail Hive, consultez l’article relatif à [l’utilisation de Hive sur HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md).

Dans le cadre du travail Hive, vous allez importer les données du fichier .csv dans une table Hive nommée **Delays**.

1. À partir de l’invite SSH que vous avez déjà pour le cluster HDInsight, utilisez la commande suivante pour créer et modifier un fichier nommé **flightdelays.hql** :

    ```bash
    nano flightdelays.hql
    ```

2. Utilisez le texte suivant comme contenu de ce fichier :

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Pour enregistrer le fichier, appuyez sur **Échap**, puis entrez `:x`.

3. Pour démarrer Hive et exécuter le fichier **flightdelays.hql**, utilisez la commande suivante :

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Dès l’exécution du script __flightdelays.hql__ terminée, utilisez la commande suivante pour ouvrir une session Beeline interactive :

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Lorsque vous recevez l’invite `jdbc:hive2://localhost:10001/>`, utilisez la requête suivante pour récupérer des données à partir des données relatives aux retards de vol qui ont été importées :

    ```hiveql
    INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Cette requête récupère la liste des villes qui ont enregistré des retards liés aux conditions météo, ainsi que le temps de retard moyen, et l’enregistre dans `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Sqoop lit ensuite les données à partir de cet emplacement et les exporte vers Azure SQL Database.

6. Pour quitter Beeline, entrez `!quit` à l’invite de commandes.

## <a name="create-a-sql-database-table"></a>Créer une table de base de données SQL

Cette section part du principe que vous avez déjà créé une base de données Azure SQL. Si vous n’avez pas de base de données SQL, consultez [Créer une base de données Azure SQL dans le portail Azure](../../sql-database/sql-database-get-started.md) pour en créer une.

Si vous disposez déjà d’une base de données SQL, vous devez obtenir le nom du serveur. Pour trouver le nom du serveur sur le [portail Azure](https://portal.azure.com), sélectionnez **Bases de données SQL**, puis filtrez sur le nom de la base de données que vous décidez d’utiliser. Le nom du serveur est répertorié dans la colonne **Nom du serveur**.

![Obtenir les détails du serveur Azure SQL](./media/tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Obtenir les détails du serveur Azure SQL")

> [!NOTE]
> Il existe de nombreuses façons de se connecter à la base de données SQL et de créer une table. Les étapes suivantes utilisent [FreeTDS](http://www.freetds.org/) à partir du cluster HDInsight.


1. Pour installer FreeTDS, utilisez la commande suivante à partir d’une connexion SSH avec le cluster :

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Dès l’installation terminée, utilisez la commande suivante pour vous connecter au serveur SQL Database. Remplacez **serverName** par le nom du serveur de base de données SQL. Remplacez **adminLogin** et **adminPassword** par les informations d’identification de la base de données SQL. Remplacez **databaseName** par le nom de la base de données.

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
    ```

    Lorsque vous y êtes invité, entrez le mot de passe de connexion administrateur de SQL Database.

    Le résultat ressemble au texte suivant :

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. À l’invite de commandes `1>` , entrez les lignes suivantes :

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Une fois l’instruction `GO` entrée, les instructions précédentes sont évaluées. Cette requête crée une table nommée **delays** avec un index cluster.

    Utilisez la requête suivante pour vérifier que la table a été créée :

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    Le résultat ressemble au texte suivant :

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

5. Entrée `exit` at the `1>` .


## <a name="export-data-to-sql-database-using-sqoop"></a>Exporter des données vers la base de données SQL à l’aide de Sqoop

Dans les sections précédentes, vous avez copié les données transformées dans `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Dans cette section, vous allez utiliser Sqoop pour exporter les données de `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` vers la table que vous avez créée dans la base de données Azure SQL. 

1. Utilisez la commande suivante pour vérifier que Sqoop peut voir votre base de données SQL :

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
    ```

    Cette commande retourne une liste de bases de données, dont la base de données dans laquelle vous avez créé précédemment la table des retards.

2. Utilisez la commande suivante pour exporter des données depuis hivesampletable dans la table des retards :

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' 
    --fields-terminated-by '\t' -m 1
    ```

    Sqoop se connecte à la base de données qui contient la table des retards et exporte les données du répertoire `/tutorials/flightdelays/output` vers la table des retards.

3. Après l’exécution de la commande sqoop, connectez-vous à la base de données à l’aide de l’utilitaire tsql :

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
    ```

    Utilisez les instructions suivantes pour vérifier que les données ont été exportées dans la table des retards :

    ```sql
    SELECT * FROM delays
    GO
    ```

    Vous devez voir une liste des données dans la table. La table inclut le nom de la ville et le retard de vol moyen pour la ville en question. 

    Tapez `exit` pour quitter l’utilitaire tsql.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir d’autres façons d’utiliser les données dans HDInsight, consultez les articles suivants :

* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Développer des programmes MapReduce Java pour Hadoop sur HDInsight][hdinsight-develop-mapreduce]
* [Développer des programmes MapReduce de diffusion en continu Python pour HDInsight][hdinsight-develop-streaming]
* [Utilisation d’Oozie avec HDInsight][hdinsight-use-oozie]
* [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: ../../hdinsight/hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:../../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-provision]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: ../../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: ../../hdinsight/hdinsight-upload-data.md
[hdinsight-get-started]: ../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:../../hdinsight/hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:../../hdinsight/hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:../../hdinsight/hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:../../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
