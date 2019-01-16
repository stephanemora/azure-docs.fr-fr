---
title: 'Tutoriel : Effectuer des opérations d’extraction, transformation et chargement (ETL) à l’aide d’Apache Hive sur Azure HDInsight'
description: Dans ce tutoriel, vous allez découvrir comment extraire des données d’un jeu de données CSV brut, comment les transformer à l’aide d’Apache Hive sur Azure HDInsight, puis comment charger les données transformées dans Azure SQL Database à l’aide de Sqoop.
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jamesbak
ms.openlocfilehash: 65d2d69c788a54371664d1a443a79bd121332470
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105149"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>Tutoriel : Extraire, transformer et charger des données à l’aide d’Apache Hive sur Azure HDInsight

Dans ce tutoriel, vous effectuez une opération ETL : extraction, transformation et chargement de données. Vous prenez un fichier de données CSV brut, vous l’importez dans un cluster Azure HDInsight, le transformez avec Apache Hive, puis vous le chargez dans une base de données SQL Azure avec Apache Sqoop.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Extrayez et chargez les données dans un cluster HDInsight.
> * Transformez les données à l’aide d’Apache Hive.
> * Chargez les données dans une base de données SQL Azure à l’aide de Sqoop.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* **Cluster Hadoop Linux sur HDInsight** : Pour créer un cluster HDInsight Linux, consultez [Configurer des clusters dans HDInsight avec Hadoop, Spark, Kafka et bien plus encore](./data-lake-storage-quickstart-create-connect-hdi-cluster.md).

* **Azure SQL Database** : Vous allez utiliser une base de données SQL Azure comme magasin de données cible. Si vous n’avez pas de base de données SQL, consultez [Créer une base de données Azure SQL dans le portail Azure](../../sql-database/sql-database-get-started.md).

* **Azure CLI** : Si vous n’avez pas installé l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Un client SSH** : Pour plus d’informations, consultez [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Les étapes décrites dans cet article nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur Azure HDInsight version 3.4 ou ultérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="download-the-flight-data"></a>Téléchargement des données de vol

Ce tutoriel utilise des données de vol issues du Bureau of Transportation Statistics pour montrer comment effectuer une opération ETL. Vous devez télécharger ces données pour suivre ce tutoriel.

1. Accédez à [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

1. Sur la page, sélectionnez les valeurs suivantes :

   | NOM | Valeur |
   | --- | --- |
   | **Filtre année** |2013 |
   | **Filtre période** |Janvier |
   | **Champs** |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

1. Effacez tous les autres champs.

1. Sélectionnez **Télécharger**. Vous obtenez un fichier .zip avec les champs de données que vous avez sélectionnés.

## <a name="extract-and-upload-the-data"></a>Extraire et charger les données

Dans cette section, vous utilisez `scp` pour charger des données dans votre cluster HDInsight.

Ouvrez une invite de commandes et utilisez la commande suivante pour charger le fichier .zip sur le nœud principal du cluster HDInsight :

```bash
scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
```

* Remplacez \<FILE_NAME> par le nom du fichier .zip.
* Remplacez \<SSH_USER_NAME> par la connexion SSH du cluster HDInsight.
* Remplacez \<CLUSTER_NAME> par le nom du cluster HDInsight.

Si vous utilisez un mot de passe pour authentifier votre connexion SSH, vous êtes invité à l’indiquer. 

Si vous utilisez une clé publique, vous pouvez avoir besoin d’utiliser le paramètre `-i` et de spécifier le chemin de la clé privée correspondante. Par exemple : `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`.

À la fin du chargement, connectez-vous au cluster par l’intermédiaire de SSH. À l’invite de commandes, entrez la commande suivante :

```bash
ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
```

Utilisez la commande suivante pour décompresser le fichier .zip :

```bash
unzip <FILE_NAME>.zip
```

La commande extrait un fichier **.csv** d’environ 60 Mo.

Utilisez les commandes suivantes pour créer un répertoire, puis copiez le fichier *.csv* dans le répertoire :

```bash
hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
```

Utilisez la commande suivante pour créer le système de fichiers Data Lake Storage Gen2.

```bash
hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
```

## <a name="transform-the-data"></a>Transformer les données

Dans cette section, vous utilisez Beeline pour exécuter un travail Apache Hive.

Dans le cadre du travail Apache Hive, vous allez importer les données du fichier .csv dans une table Apache Hive nommée **delays**.

À partir de l’invite SSH que vous avez déjà pour le cluster HDInsight, utilisez la commande suivante pour créer et modifier un fichier nommé **flightdelays.hql** :

```bash
nano flightdelays.hql
```

Utilisez le texte suivant comme contenu de ce fichier :

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

Pour enregistrer le fichier, sélectionnez la touche ÉCHAP, puis entrez `:x`.

Pour démarrer Hive et exécuter le fichier **flightdelays.hql**, utilisez la commande suivante :

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
```

Dès l’exécution du script __flightdelays.hql__ terminée, utilisez la commande suivante pour ouvrir une session Beeline interactive :

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
```

Lorsque vous recevez l’invite `jdbc:hive2://localhost:10001/>`, utilisez la requête suivante pour récupérer des données à partir des données relatives aux retards de vol qui ont été importées :

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

Pour quitter Beeline, entrez `!quit` à l’invite de commandes.

## <a name="create-a-sql-database-table"></a>Créer une table de base de données SQL

Vous avez besoin du nom du serveur de votre base de données SQL pour cette opération. Effectuez ces étapes pour rechercher le nom de votre serveur.

1. Accédez au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Bases de données SQL**.

1. Filtrez sur le nom de la base de données que vous choisissez d’utiliser. Le nom du serveur est répertorié dans la colonne **Nom du serveur**.

1. Filtrez sur le nom de la base de données que vous voulez utiliser. Le nom du serveur est répertorié dans la colonne **Nom du serveur**.

    ![Obtenir les détails du serveur Azure SQL](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Obtenir les détails du serveur Azure SQL")

    Il existe de nombreuses façons de se connecter à la base de données SQL et de créer une table. Les étapes suivantes utilisent [FreeTDS](http://www.freetds.org/) à partir du cluster HDInsight.

Pour installer FreeTDS, utilisez la commande suivante à partir d’une connexion SSH avec le cluster :

```bash
sudo apt-get --assume-yes install freetds-dev freetds-bin
```

Dès l’installation terminée, utilisez la commande suivante pour vous connecter au serveur SQL Database.

* Remplacez \<SERVER_NAME> par le nom du serveur SQL Database.
* Remplacez \<ADMIN_LOGIN> par la connexion administrateur de SQL Database.
* Remplacez \<DATABASE_NAME> par le nom de la base de données.

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

À l’invite `1>`, entrez les instructions suivantes:

```hiveql
CREATE TABLE [dbo].[delays](
[origin_city_name] [nvarchar](50) NOT NULL,
[weather_delay] float,
CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
([origin_city_name] ASC))
GO
```

Une fois l’instruction `GO` entrée, les instructions précédentes sont évaluées.
La requête crée une table nommée **delays** avec un index cluster.

Utilisez la requête suivante pour vérifier que la table est créée :

```hiveql
SELECT * FROM information_schema.tables
GO
```

Le résultat ressemble au texte suivant :

```
TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
databaseName       dbo             delays        BASE TABLE
```

Entrée `exit` at the `1>` .

## <a name="export-and-load-the-data"></a>Exporter et charger les données

Dans les sections précédentes, vous avez copié les données transformées à l’emplacement `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Dans cette section, vous allez utiliser Sqoop pour exporter les données de `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` vers la table que vous avez créée dans la base de données SQL Azure.

Utilisez la commande suivante pour vérifier que Sqoop peut voir votre base de données SQL :

```bash
sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
```

La commande retourne une liste de bases de données, dont la base de données dans laquelle vous avez créé la table **delays**.

Utilisez la commande suivante pour exporter les données de la table **hivesampletable** vers la table **delays** :

```bash
sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
```

Sqoop se connecte à la base de données qui contient la table **delays** et exporte les données du répertoire `/tutorials/flightdelays/output` vers la table **delays**.

Après l’exécution de la commande `sqoop`, connectez-vous à la base de données à l’aide de l’utilitaire tsql :

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
```

Utilisez les instructions suivantes pour vérifier que les données ont été exportées dans la table **delays** :

```sql
SELECT * FROM delays
GO
```

Vous devez voir une liste des données dans la table. La table inclut le nom de la ville et le retard de vol moyen pour la ville en question.

Entrez `exit` pour quitter l’utilitaire tsql.

## <a name="clean-up-resources"></a>Supprimer des ressources

Toutes les ressources utilisées dans ce tutoriel existent déjà. Aucun nettoyage n’est nécessaire.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir d’autres façons d’utiliser les données dans HDInsight, consultez l’article suivant :

> [!div class="nextstepaction"]
> [Extraire, transformer et charger des données à l’aide d’Azure Databricks](./data-lake-storage-use-hdi-cluster.md)
