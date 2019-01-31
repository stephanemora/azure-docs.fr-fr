---
title: 'Didacticiel : Accéder aux données Azure Data Lake Storage Gen2 Preview avec Azure Databricks à l’aide de Spark | Microsoft Docs'
description: Ce tutoriel montre comment exécuter des requêtes Spark sur un cluster Azure Databricks afin d’accéder aux données dans un compte de stockage Azure Data Lake Storage Gen2.
services: storage
author: dineshmurthy
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: dineshm
ms.openlocfilehash: 31d18d7ea4ee195f7ffcfa04fb247b5dfd525c6a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251473"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Didacticiel : Accéder aux données Data Lake Storage Gen2 Preview avec Azure Databricks à l’aide de Spark

Ce tutoriel vous montre comment connecter un cluster Azure Databricks aux données contenues dans un compte de stockage Azure compatible avec Azure Data Lake Storage Gen2 (Preview). Cette connexion vous permet d’exécuter en mode natif des requêtes et analyses sur des données à partir de votre cluster.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
> * Créer un cluster Databricks
> * Ingérer des données non structurées dans un compte de stockage
> * Exécution d’analyses sur vos données dans le stockage Blob

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Ce tutoriel montre comment consommer et interroger des données de vol de billet d’avion, qui sont mis à disposition par le [Ministère des transports américain](https://transtats.bts.gov/DL_SelectFields.asp). 

1. Cochez la case **Prezipped file** (Fichier précompressé) pour sélectionner tous les champs de données.
2. Sélectionnez **Télécharger** et enregistrez les résultats sur votre machine.
3. Notez le nom du fichier et le chemin du téléchargement, car vous aurez besoin de ces informations à une étape ultérieure.

Pour suivre ce tutoriel, vous avez besoin d’un compte de stockage avec des capacités analytiques. Nous vous recommandons de suivre notre [guide de démarrage rapide](data-lake-storage-quickstart-create-account.md) sur le sujet afin d’en créer un. 

## <a name="set-aside-storage-account-configuration"></a>Mettre de côté la configuration du compte de stockage

Vous allez avoir besoin du nom de votre compte de stockage et d’un URI de point de terminaison de système de fichiers.

Pour obtenir le nom de votre compte de stockage sur le portail Azure, choisissez **Tous les services**, puis effectuez un filtrage basé sur le terme *stockage*. Sélectionnez ensuite **Comptes de stockage**, puis localisez votre compte de stockage.

Pour obtenir l’URI du point de terminaison de système de fichiers, choisissez **Propriétés**. Dans le volet des propriétés, recherchez la valeur du champ **Point de terminaison de système de fichiers ADLS principal**.

Collez ces deux valeurs dans un fichier texte. Vous en aurez besoin bientôt.

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Créer un principal du service

Créez un principal du service en suivant l’aide fournie dans cette rubrique : [Guide pratique pour Utilisez le portail pour créer une application Azure AD et un principal du service pouvant accéder aux ressources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Vous devrez faire certaines choses spécifiques pendant que vous suivrez les étapes décrites dans cet article.

:heavy_check_mark: Au cours des étapes indiquées dans la section [Créer une application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) de l’article, veillez à affecter l’URI de point de terminaison que vous venez de collecter au champ **URL de connexion** dans la boîte de dialogue **Créer**.

:heavy_check_mark: Au cours des étapes indiquées dans la section [Attribuer un rôle à l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) de l’article, veillez à affecter le **Rôle Contributeur de Stockage Blob** à votre application.

:heavy_check_mark: Au cours des étapes indiquées dans la section [Obtenir les valeurs de connexion](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) de l’article, collez les valeurs de l’ID de locataire, de l’ID d’application et de la clé d’authentification dans un fichier texte. Vous en aurez besoin bientôt.

## <a name="create-a-databricks-cluster"></a>Créer un cluster Databricks

L’étape suivante consiste à créer un cluster Databricks pour créer un espace de travail de données.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.
2. Entrez **Azure Databricks** dans le champ de recherche.
3. Sélectionnez **Créer** dans le panneau Azure Databricks.
4. Nommez le service Databricks **myFlightDataService** (n’oubliez pas de cocher la case *Épingler au tableau de bord* lorsque vous créez le service).
5. Sélectionnez **Launch Workspace** (Lancer l’espace de travail) pour ouvrir l’espace de travail dans une nouvelle fenêtre de navigateur.
6. Sélectionnez **Clusters** dans la barre de navigation de gauche.
7. Sélectionnez **Créer un cluster**.
8. Entrez **myFlightDataCluster** dans le champ **Nom du cluster**.
9. Sélectionnez **Standard_D8s_v3** dans le champ **Type de traitement Web**.
10. Définissez la valeur **Min Workers** (Rôles de travail min) sur **4**.
11. Sélectionnez **Créer un cluster** en haut de la page. (Ce processus peut prendre jusqu’à 5 minutes.)
12. Quand le processus est terminé, sélectionnez **Azure Databricks** en haut à gauche de la barre de navigation.
13. Sélectionnez **Notebook** dans la section **Nouveau** au bas de la page.
14. Entrez un nom de votre choix dans le champ **Nom**, puis sélectionnez **Python** comme langage.
15. Tous les autres champs peuvent être laissés sur leurs valeurs par défaut.
16. Sélectionnez **Créer**.
17. Copiez et collez le bloc de code suivant dans la première cellule, mais n’exécutez pas ce code pour l’instant.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<application-id>",
           "fs.azure.account.oauth2.client.secret": "<authentication-id>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```
18. Dans ce bloc de code, remplacez les valeurs d’espace réservé `storage-account-name`, `application-id`, `authentication-id` et `tenant-id` par les valeurs que vous avez collectées quand vous avez effectué les étapes des sections [Mettre de côté la configuration du compte de stockage](#config) et [Créer un principal du service](#service-principal) décrites dans cet article. Remplacez l’espace réservé `file-system-name` par n’importe quel nom que vous souhaitez donner à votre système de fichiers.

19. Appuyez sur les touches **Maj +Entrée** pour exécuter le code de ce bloc.

## <a name="ingest-data"></a>Réception de données

### <a name="copy-source-data-into-the-storage-account"></a>Copier des données sources dans le compte de stockage

La tâche suivante consiste à utiliser AzCopy pour copier des données du fichier *.csv* dans le stockage Azure. Ouvrez une fenêtre d’invite de commandes et entrez les commandes suivantes. Veillez à remplacer les espaces réservés `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` et `<ACCOUNT_KEY>` par les valeurs correspondantes que vous avez notées dans une étape précédente.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Utiliser Databricks Notebook pour convertir CSV en Parquet

Rouvrez Databricks dans votre navigateur et exécutez les étapes suivantes :

1. Sélectionnez **Azure Databricks** en haut à gauche de la barre de navigation.
2. Sélectionnez **Notebook** dans la section **Nouveau** au bas de la page.
3. Entrez **CSV2Parquet** dans le champ **Nom**.
4. Tous les autres champs peuvent être laissés sur leurs valeurs par défaut.
5. Sélectionnez **Créer**.
6. Collez le code ci-après dans la cellule **Cmd 1**. (Ce code s’enregistre automatiquement dans l’éditeur.)

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>Explorer les données

Revenez dans l’espace de travail Databricks, puis sélectionnez l’icône **Récent** dans la barre de navigation de gauche.

1. Sélectionnez le notebook **Flight Data Analytics**.
2. Appuyez sur **Ctrl + Alt + N** pour créer une nouvelle cellule.

Entrez chacun des blocs de code suivants dans **Cmd 1** et appuyez sur **Cmd + Entrée** pour exécuter le script Python.

Pour obtenir la liste des fichiers CSV chargés via AzCopy, exécutez le script suivant :

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

Pour créer un nouveau fichier et répertorier les fichiers dans le dossier *parquet/flights* dossier, exécutez ce script :

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

Avec ces exemples de code, vous avez exploré la nature hiérarchique de HDFS avec des données stockées dans un compte de stockage compatible avec Azure Data Lake Storage Gen2.

## <a name="query-the-data"></a>Interroger les données

Vous pouvez ensuite commencer à interroger les données que vous avez chargées dans votre compte de stockage. Entrez chacun des blocs de code suivants dans **Cmd 1** et appuyez sur **Cmd + Entrée** pour exécuter le script Python.

### <a name="run-simple-queries"></a>Exécuter des requêtes simples

Pour créer des trames de données pour vos sources de données, exécutez le script suivant :

> [!IMPORTANT]
> Veillez à remplacer l’espace réservé **< YOUR_CSV_FILE_NAME >** par le nom du fichier que vous avez téléchargé au début de ce didacticiel.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

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
#preferably run this in a separate cmd cell
display(flightDF)
```

Pour exécuter des requêtes d’analyse sur les données, exécutez le script suivant :

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
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

### <a name="run-complex-queries"></a>Exécuter des requêtes complexes

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

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources du compte de stockage, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

[!div class="nextstepaction"] 
> [Extraire, transformer et charger des données à l’aide d’Apache Hive sur Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)

