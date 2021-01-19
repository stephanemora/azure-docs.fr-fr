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
ms.date: 07/20/2020
ms.openlocfilehash: 3b5f5d64498922e9fc35942ff4570d801aa6c516
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118878"
---
# <a name="analyze-with-apache-spark"></a>Analyser avec Apache Spark

Dans ce tutoriel, vous allez découvrir les étapes de base permettant de charger et d’analyser des données avec Apache Spark pour Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analyser les données NYC Taxi situées dans le Stockage Blob à l’aide de Spark

1. Dans le hub **Données**, sélectionnez **Ajouter une nouvelle ressource** (bouton plus situé au-dessus de **Lié**) > **Parcourir la galerie**.
1. Sélectionnez **NYC Taxi & Limousine Commission - yellow taxi trip records**.
1. Au bas de la page, sélectionnez **Continuer** > **Ajouter un jeu de données**.
1. Dans le hub **Données** sous **Lié**, cliquez avec le bouton droit sur **Stockage Blob Azure**, puis sélectionnez **Exemples de jeux de données** > **nyc_tlc_yellow**.
1. Sélectionnez **Nouveau notebook** pour créer un notebook avec le code suivant :

    ```py
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```

1. Dans le menu **Attacher à** du notebook, choisissez un pool Spark serverless.
1. Sélectionnez **Exécuter** dans la cellule.
1. Si vous voulez voir uniquement le schéma du dataframe, exécutez une cellule avec le code suivant :

    ```py
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Charger les données NYC Taxi dans la base de données Spark appelée nyctaxi

Des données sont disponibles dans une table dans **SQLPOOL1**. Chargez-les dans une base de données Spark nommée **nyctaxi**.

1. Dans Synapse Studio, accédez au hub **Développer**.
1. Sélectionnez **+**  > **Notebook**.
1. En haut du notebook, définissez la valeur **Joindre à** sur **Spark1**.
1. Sélectionnez **Ajouter du code** pour ajouter une cellule de code du notebook, puis entrez le texte suivant :

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Sélectionnez **Exécuter** dans la cellule.
1. Dans le hub **Données**, cliquez avec le bouton droit sur **Bases de données**, puis sélectionnez **Actualiser**. Vous devez maintenant voir ces deux bases de données :

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analyser les données des taxis de New York à l’aide de Spark et des notebooks

1. Retournez dans votre notebook.
1. Créez une nouvelle cellule de code et entrez le texte suivant.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Exécutez la cellule pour afficher les données NYC Taxi que vous avez chargées dans la base de données Spark **nyctaxi**.
1. Exécutez le code suivant pour effectuer la même analyse que celle que vous avez faite précédemment avec le pool SQL dédié **SQLPOOL1**. Ce code enregistre et présente les résultats de l’analyse dans une table nommée **nyctaxi.passengercountstats**.

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

Vous avez précédemment copié les données de la table **SQLPOOL1.dbo.Trip** du pool SQL dédié dans la table Spark **nyctaxi.trip**. Ensuite, vous avez agrégé les données dans la table Spark **nyctaxi.passengercountstats**. Vous allez maintenant copier les données de **nyctaxi.passengercountstats** dans une table du pool SQL dédié nommée **SQLPOOL1.dbo.PassengerCountStats**.

Exécutez la cellule suivante dans votre notebook. La table Spark agrégée est de nouveau copiée dans la table du pool SQL dédié.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse des données à l’aide d’un pool SQL serverless](get-started-analyze-sql-on-demand.md)
