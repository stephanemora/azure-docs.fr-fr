---
title: Accéder aux données Azure Data Lake Storage Gen2 Preview avec Azure Databricks à l’aide de Spark | Microsoft Docs
description: Apprenez à exécuter des requêtes Spark sur un cluster Databricks afin d’accéder aux données sur un compte de stockage Azure Data Lake Storage Gen2.
services: hdinsight,storage
tags: azure-portal
author: dineshm
manager: twooley
ms.component: data-lake-storage-gen2
ms.service: storage
ms.workload: big-data
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: 41c34b2c1459178c59af66a75e7b34e2ec158025
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136401"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Didacticiel : accéder aux données Azure Data Lake Storage Gen2 Preview avec Azure Databricks à l’aide de Spark

Dans ce didacticiel, vous allez apprendre à exécuter des requêtes Spark sur un cluster Azure Databricks afin d’interroger des données sur un compte compatible avec Azure Data Lake Storage Gen2 Preview.

> [!div class="checklist"]
> * Créer un cluster Databricks
> * Ingérer des données non structurées dans un compte de stockage
> * Déclencher une fonction Azure pour traiter les données
> * Exécution d’analyses sur vos données dans le stockage Blob

## <a name="prerequisites"></a>Prérequis

Ce tutoriel montre comment consommer et interroger des données de vol de billet d’avion, qui sont mis à disposition par le [Ministère des transports américain](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time). Téléchargez au moins deux ans de données de billet d’avion (en sélectionnant tous les champs) et enregistrez le résultat sur votre machine. Veillez à noter le nom de fichier et le chemin d’accès de votre téléchargement ; vous aurez besoin de ces informations dans une étape ultérieure.

> [!NOTE]
> Cochez la case **Prezipped file** (Fichier précompressé) pour sélectionner tous les champs de données. Le téléchargement aura une taille de plusieurs gigaoctets, mais cette quantité de données est nécessaire pour l’analyse.

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>Créer un compte Azure Data Lake Storage Gen2

Pour commencer, créez un nouveau [compte Azure Data Lake Storage Gen2](quickstart-create-account.md) et donnez-lui un nom unique. Accédez ensuite au compte de stockage pour récupérer les paramètres de configuration.

> [!IMPORTANT]
> Dans la préversion, Azure Functions ne fonctionne qu’avec des comptes Azure Data Lake Storage Gen2 créés avec un espace de noms plat.

1. Sous **Paramètres**, cliquez sur **Clés d’accès**.
3. Cliquez sur le bouton **Copier** situé en regard de **key1** pour copier la valeur de clé.

Le nom et la clé du compte sont nécessaires dans les étapes ultérieures de ce didacticiel. Ouvrez un éditeur de texte et mettez de côté le nom et la clé du compte pour référence ultérieure.

## <a name="create-a-databricks-cluster"></a>Créer un cluster Databricks

L’étape suivante consiste à créer un [cluster Databricks](https://docs.azuredatabricks.net/) afin de créer un espace de travail de données.

1. Créez un [service Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) et nommez-le **myFlightDataService** (veillez à cocher la case *Épingler au tableau de bord* lorsque vous créez le service).
2. Cliquez sur **Launch Workspace** (Lancer l’espace de travail) pour ouvrir l’espace de travail dans une nouvelle fenêtre de navigateur.
3. Cliquez sur **Clusters** dans la barre de navigation de gauche.
4. Cliquez sur **Créer un cluster**.
5. Entrez **myFlightDataCluster** dans le champ *Nom du cluster*.
6. Sélectionnez **Standard_D8s_v3** dans le champ *Type de traitement Web*.
7. Définissez la valeur **Min Workers** (Rôles de travail min) sur *4*.
8. Cliquez sur **Créer un cluster** en haut de la page (ce processus peut prendre jusqu’à 5 minutes).
9. Lorsque le processus est terminé, sélectionnez **Azure Databricks** en haut à gauche de la barre de navigation.
10. Sélectionnez **Notebook** dans la section **Nouveau** au bas de la page.
11. Entrez un nom de votre choix dans le champ **Nom**, puis sélectionnez **Python** comme langage.
12. Tous les autres champs peuvent être laissés sur leurs valeurs par défaut.
13. Sélectionnez **Créer**.
14. Collez le code suivant dans la cellule **Cmd 1**, remplacez les valeurs par les valeurs que vous avez conservées dans votre compte de stockage.

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfs://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

## <a name="ingest-data"></a>Réception de données

### <a name="copy-source-data-into-the-storage-account"></a>Copier des données sources dans le compte de stockage

La tâche suivante consiste à utiliser AzCopy pour copier des données du fichier *.csv* dans le stockage Azure. Ouvrez une fenêtre d’invite de commandes et entrez les commandes suivantes. Veillez à remplacer les espaces réservés `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` et `<ACCOUNT_KEY>` par les valeurs correspondantes que vous avez mises de côté dans une étape précédente.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Utiliser Databricks Notebook pour convertir CSV en Parquet

Rouvrez Databricks dans votre navigateur et suivez les étapes suivantes :

1. Sélectionnez **Azure Databricks** en haut à gauche de la barre de navigation.
2. Sélectionnez **Notebook** dans la section **Nouveau** au bas de la page.
3. Entrez **CSV2Parquet** dans le champ **Nom**.
4. Tous les autres champs peuvent être laissés sur leurs valeurs par défaut.
5. Sélectionnez **Créer**.
6. Collez le code suivant dans la cellule **Cmd 1** (ce code s’auto-enregistre dans l’éditeur).

    ```python
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>' 
    accountkey = " <insert account key>'
    fullname = "fs.azure.account.key." +accountname+ ".blob.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".blob.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>Explorer des données à l’aide du système de fichiers DFS Hadoop

Revenez dans l’espace de travail Databricks, puis cliquez sur l’icône **Récent** dans la barre de navigation gauche.

1. Cliquez sur le notebook **Flight Data Analytics**.
2. Appuyez sur **Ctrl + Alt + N** pour créer une nouvelle cellule.

Entrez chacun des blocs de code suivants dans **Cmd 1** et appuyez sur **Cmd + Entrée** pour exécuter le script Python.

Pour obtenir la liste des fichiers CSV chargés via AzCopy, exécutez le script suivant :

```python
import os.path
import IPython
from pyspark.sql import SQLContext
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

Pour créer un nouveau fichier et répertorier les fichiers dans le dossier *parquet/flights* dossier, exécutez ce script :

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
Avec ces exemples de code, vous avez exploré la nature hiérarchique de HDFS à l’aide des données stockées dans un compte compatible avec Azure Data Lake Storage Gen2.

## <a name="query-the-data"></a>Interroger les données

Vous pouvez ensuite commencer à interroger les données que vous avez chargées dans Azure Data Lake Storage. Entrez chacun des blocs de code suivants dans **Cmd 1** et appuyez sur **Cmd + Entrée** pour exécuter le script Python.

### <a name="simple-queries"></a>Exemples de requêtes

Pour créer des trames de données pour vos sources de données, exécutez le script suivant :

> [!IMPORTANT]
> Veillez à remplacer l’espace réservé **< YOUR_CSV_FILE_NAME >** par le nom du fichier que vous avez téléchargé au début de ce didacticiel.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created via the Azure Function
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferrably run this in a separate cmd cell
display(flightDF)
```

Pour exécuter des requêtes d’analyse sur les données, exécutez le script suivant :

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```
### <a name="complex-queries"></a>Requêtes complexes

Pour exécuter les requêtes plus complexes suivantes, exécutez chaque segment à la fois dans le notebook et examinez les résultats.

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="next-steps"></a>Étapes suivantes

* [Extraire, transformer et charger des données à l’aide d’Apache Hive sur Azure HDInsight](tutorial-extract-transform-load-hive.md)