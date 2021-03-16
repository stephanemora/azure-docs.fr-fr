---
title: 'Démarrage rapide : Bien démarrer avec l’analyse Spark'
description: Dans ce tutoriel, vous allez voir comment analyser des données avec Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 6b3c1ac2ea3625a768e16a3465230a5386c98ddc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423711"
---
# <a name="analyze-with-apache-spark"></a>Analyser avec Apache Spark

Dans ce tutoriel, vous allez découvrir les étapes de base permettant de charger et d’analyser des données avec Apache Spark pour Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analyser les données NYC Taxi situées dans le Stockage Blob à l’aide de Spark

1. Dans le hub **Données**, cliquez sur le bouton **+** pour **Ajouter une nouvelle ressource**, puis cliquez sur >> **Parcourir la galerie**. 
1. Recherchez **NYC Taxi & Limousine Commission - yellow taxi trip records**, puis cliquez dessus. 
1. Au bas de la page, appuyez sur **Continuer**, puis sur **Ajouter un jeu de données**. 
1. Après quelques instants, dans le hub **Données** situé sous **Lié**, cliquez avec le bouton droit sur **Stockage Blob Azure >> Exemples de jeux de données >> nyc_tlc_yellow**. Sélectionnez ensuite **Nouveau notebook**, puis **Charger dans un dataframe**.
1. Un notebook sera créé avec le code suivant :
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. Dans le notebook, dans le menu **Attacher à**, choisissez le pool Spark serverless **Spark1** que nous avons créé précédemment.
1. Sélectionnez **Exécuter** dans la cellule. Synapse démarre une nouvelle session Spark pour exécuter cette cellule si nécessaire. Si une nouvelle session Spark est nécessaire, sa création prend environ deux secondes. 
1. Si vous souhaitez simplement voir le schéma du dataframe, exécutez une cellule avec le code suivant :
    ```

    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Charger les données NYC Taxi dans la base de données Spark appelée nyctaxi

Des données sont disponibles dans une table dans **SQLPOOL1**. Chargez-les dans une base de données Spark nommée **nyctaxi**.

1. Dans Synapse Studio, accédez au hub **Développer**.
1. Sélectionnez **+**  > **Notebook**.
1. En haut du notebook, définissez la valeur **Joindre à** sur **Spark1**.
1. Dans la première cellule de code du notebook, puis entrez le code suivant :


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. Exécutez le script. Cette opération peut prendre 2 à 3 minutes.
1. Dans le hub **Données**, sous l’onglet **Espace de travail**, cliquez avec le bouton droit sur **Bases de données**, puis sélectionnez **Actualiser**. Vous devez maintenant voir la base de données **nyctaxi (Spark)** dans la liste.


## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analyser les données des taxis de New York à l’aide de Spark et des notebooks

1. Retournez dans votre notebook.
1. Créez une nouvelle cellule de code, puis entrez le code suivant. 


   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Exécutez la cellule pour afficher les données NYC Taxi que nous avons chargées dans la base de données Spark **nyctaxi**.
1. Créez une nouvelle cellule de code, puis entrez le code suivant. Exécutez ensuite la cellule pour effectuer la même analyse que celle faite précédemment avec le pool SQL dédié **SQLPOOL1**. Ce code enregistre et affiche les résultats de l’analyse dans une table appelée **nyctaxi.passengercountstats**.


   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Dans les résultats de la cellule, sélectionnez **Graphique** pour afficher les données visualisées.

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Charger des données à partir d’une table Spark dans une table de pool SQL dédié

Nous avions copié les données de la table **SQLPOOL1.dbo.Trip** du pool SQL dédié dans la table Spark **nyctaxi.trip**. Ensuite, vous avez agrégé les données dans la table Spark **nyctaxi.passengercountstats**. Vous allez maintenant copier les données de **nyctaxi.passengercountstats** dans une table du pool SQL dédié appelée **SQLPOOL1.dbo.PassengerCountStats**.

1. Créez une nouvelle cellule de code, puis entrez le code suivant. Exécutez la cellule dans votre notebook. La table Spark agrégée est de nouveau copiée dans la table du pool SQL dédié.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse des données à l’aide d’un pool SQL serverless](get-started-analyze-sql-on-demand.md)
