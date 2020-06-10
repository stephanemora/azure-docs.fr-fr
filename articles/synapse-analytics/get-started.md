---
title: 'Tutoriel : Prise en main d’Azure Synapse Analytics'
description: Comprendre rapidement étapes par étapes les concepts de base dans Azure Synapse
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 24a34ae6f00eca7154021162184f5e71503da06b
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248326"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Prise en main d’Azure Synapse Analytics

Ce document vous guide dans toutes les étapes de base nécessaires à la configuration et à l’utilisation d’Azure Synapse Analytics.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Préparez un compte de stockage pour l’utilisation d’un espace de travail Synapse

* Ouvrez le [portail Azure](https://portal.azure.com)
* Créez un nouveau compte de stockage en procédant comme suit :

    |Onglet|Paramètre | Valeur suggérée | Description |
    |---|---|---|---|
    |Concepts de base|**Nom du compte de stockage**| Vous pouvez lui donner le nom que vous voulez.|Dans ce document, nous l’appellerons `contosolake`.|
    |Concepts de base|**Type de compte**|Il doit être défini sur `StorageV2`||
    |Concepts de base|**Lieu**|Vous pouvez choisir n’importe quel emplacement| Nous vous recommandons de choisir votre espace de travail Synapse et votre compte Azure Data Lake Storage (ADLS) Gen2 dans la même région.|
    |Avancé|**Data Lake Storage Gen2**|`Enabled`| Azure Synapse fonctionne uniquement avec les comptes de stockage dans lesquels ce paramètre est activé.|

1. Une fois le compte de stockage créé, sélectionnez **Contrôle d’accès (IAM)** dans le volet de navigation de gauche. Attribuez ensuite les rôles suivants ou assurez-vous qu’ils sont déjà attribués. 

    a. * Attribuez-vous le rôle de **Propriétaire** sur le compte de stockage b. Attribuez-vous le rôle de **Propriétaire des données blob du stockage** sur le compte de stockage

1. Dans le volet de navigation de gauche, sélectionnez **conteneurs** et créez un conteneur. Vous pouvez lui donner le nom que vous voulez. Vous pouvez utiliser le **Niveau d’accès public** par défaut. Dans ce document, nous appellerons le conteneur `users`. Sélectionnez **Create** (Créer). 

À l’étape suivante, vous allez configurer votre espace de travail Synapse pour utiliser ce compte de stockage comme compte de stockage « principal » et le conteneur pour stocker les données de l’espace de travail. L’espace de travail stockera les données dans des tables Apache Spark et les journaux des applications Spark dans ce compte sous un dossier nommé `/synapse/workspacename`.

## <a name="create-a-synapse-workspace"></a>Créer un espace de travail Synapse

* Ouvrez le [Portail Azure](https://portal.azure.com) et en haut de la liste, recherchez `Synapse`.
* Dans les résultats de la recherche, sous **Services**, sélectionnez **Azure Synapse Analytics (préversion des espaces de travail)**
* Sélectionnez **+ Ajouter** pour créer un espace de travail avec ces paramètres.

    |Onglet|Paramètre | Valeur suggérée | Description |
    |---|---|---|---|
    |Concepts de base|**Nom de l’espace de travail**|Vous pouvez lui donner le nom que vous voulez.| Dans ce document, nous utiliserons `myworkspace`|
    |Concepts de base|**Région**|Faire correspondre la région du compte de stockage|

1. Sous **sélectionnez Data Lake Storage Gen 2**, sélectionnez le compte et le conteneur que vous avez créés précédemment.

1. Sélectionnez **Revoir + créer**. Sélectionnez **Create** (Créer). Votre espace de travail sera prêt en quelques minutes.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Vérifier que le MSI de l’espace de travail Synapse a accès au compte de stockage

Cela a peut-être déjà été fait pour vous. Dans tous les cas, vérifiez-le.

1. Ouvrez le [Portail Azure](https://portal.azure.com) et ouvrez le compte de stockage principal choisi pour votre espace de travail.
1. Dans le menu de navigation de gauche, sélectionnez **Contrôle d’accès (IAM)** . Attribuez ensuite les rôles suivants ou assurez-vous qu’ils sont déjà attribués. 
    a. Attribuez à l’identité de l’espace de travail le rôle de **Contributeur aux données blob du stockage (préversion)** sur le compte de stockage. L’identité de l’espace de travail porte le même nom que l’espace de travail. Dans ce document, le nom de l’espace de travail est `myworkspace` pour que l’identité de l’espace de travail soit `myworkspaced`
1. Sélectionnez **Enregistrer**.
    
## <a name="launch-synapse-studio"></a>Lancer Synapse Studio

Une fois votre espace de travail Synapse créé, vous disposez de deux méthodes pour ouvrir Synapse Studio :
* Ouvrez votre espace de travail Synapse dans le [Portail Azure](https://portal.azure.com) et en haut de la section **Vue d’ensemble**, sélectionnez **Lancer Synapse Studio**
* Accédez directement à https://web.azuresynapse.net et connectez-vous à votre espace de travail.

## <a name="create-a-sql-pool"></a>Créer un pool SQL

1. Dans Synapse Studio, dans la partie gauche de la barre de navigation, sélectionnez **Gérer > pools SQL**
1. Sélectionnez **+Nouveau** et entrez les paramètres suivants :

    |Paramètre | Valeur suggérée | 
    |---|---|
    |**Nom du pool SQL**| `SQLDB1`|
    |**Niveau de performances**|`DW100C`|

1. Sélectionnez **Vérifier + créer**, puis **Créer**.
1. Votre pool SQL sera prêt en quelques minutes. Lorsque votre pool SQL est créé, il est associé à une base de données de pools SQL également appelée **SQLDB1**.

Un pool SQL consomme des ressources facturables tant qu’il est actif. Vous pouvez suspendre le pool plus tard pour réduire les coûts.

## <a name="create-an-apache-spark-pool"></a>Créer un pool Apache Spark

1. Dans Synapse Studio, dans la partie gauche de la barre de navigation, sélectionnez **Gérer > pools Apache Spark**
1. Sélectionnez **+Nouveau** et entrez les paramètres suivants :

    |Paramètre | Valeur suggérée | 
    |---|---|
    |**Nom du pool Apache Spark**|`Spark1`
    |**Taille du nœud**| `Small`|
    |**Nombre de nœuds**| Définissez la valeur minimale sur 3 et la valeur maximale sur 3|

1. Sélectionnez **Vérifier + créer**, puis **Créer**.
1. Votre pool Apache Spark sera prêt en quelques secondes.

> [!NOTE]
> Malgré le nom, un pool Apache Spark n’est pas identique à un pool SQL. Il s’agit simplement de quelques métadonnées de base que vous utilisez pour indiquer à l’espace de travail Synapse comment interagir avec Spark. 

Étant donné qu’il s’agit de métadonnées, les pools Spark ne peuvent pas être démarrés ni arrêtés. 

Lorsque vous effectuez une activité Spark dans Synapse, vous spécifiez un pool Spark à utiliser. Le pool indique à Synapse le nombre de ressources Spark à utiliser. Vous payez uniquement les ressources que vous utilisez. Lorsque vous arrêtez activement l’utilisation du pool, les ressources expirent automatiquement et sont recyclées.

> [!NOTE]
> Les bases de données Spark sont créées indépendamment à partir des pools Spark. Un espace de travail a toujours une base de données Spark appelée **par défaut** et vous pouvez créer des bases de données Spark supplémentaires.

## <a name="the-sql-on-demand-pool"></a>Le pool SQL à la demande

Chaque espace de travail est fourni avec un pool prédéfini et non supprimable appelé **SQL à la demande**. Le pool SQL à la demande vous permet d’utiliser SQL sans devoir créer ni réfléchir à la gestion d’un pool Synapse SQL. Contrairement aux autres types de pools, la facturation pour SQL à la demande est basée sur la quantité de données numérisées, et non sur le nombre de ressources utilisées, pour exécuter la requête.

* SQL à la demande possède également ses propres bases de données SQL à la demande qui existent indépendamment de tout pool SQL à la demande.
* Actuellement, un espace de travail a toujours un seul pool SQL à la demande nommé **SQL à la demande**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Charger les exemples de données de taxis de New York dans la base de données SQLDB1

1. Dans Synapse Studio, dans le menu bleu tout en haut, sélectionnez l’icône **?** .
1. Sélectionnez **Prise en main > Hub de prise en main**
1. Dans la carte nommée **Exemple de données de requête**, sélectionnez le pool SQL nommé `SQLDB1`
1. Sélectionnez **Données de requête**. Une notification indiquant « chargement des exemples de données » s’affichera et disparaîtra.
1. Vous verrez une barre de notification bleu clair vers le haut de Synapse Studio pour indiquer que les données sont chargées dans SQLDB1. Patientez jusqu’à ce qu’il devienne vert, puis masquez-le.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explorez les données des taxis de New York dans le pool SQL

1. Dans Synapse Studio, accédez au hub **Données**
1. Accédez à **SQLDB1 > Tables**. Vous verrez que plusieurs tables ont été chargées.
1. Cliquez avec le bouton de droite sur la table **dbo.Trip** et sélectionnez **Nouveau script SQL > Sélectionner les 100 premières lignes**
1. Un nouveau script SQL sera créé et exécuté automatiquement.
1. Notez que, en haut du script SQL, **Se connecter à** est automatiquement défini sur le pool SQL appelé `SQLDB1`.
1. Remplacez le texte du script SQL par ce code et exécutez-le.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

1. Cette requête montre comment les distances totales et la distance moyenne des trajets sont liées au nombre de passagers
1. Dans la fenêtre de résultat du script SQL, remplacez la **Vue** par **Graphique** pour afficher une visualisation des résultats sous forme de graphique en courbes

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>Charger les exemples de données de taxis de New York dans la base de données nyctaxi Spark

Des données sont disponibles dans une table de `SQLDB1`. À présent, nous les chargeons dans une base de données Spark nommée `nyctaxi`.

1. Dans Synapse Studio, accédez au hub **Développer**
1. Sélectionnez **+** et sélectionnez **Notebook**
1. En haut de notebook, définissez la valeur **Joindre à** sur `Spark1`
1. Sélectionnez **Ajouter du code** pour ajouter une cellule de code du notebook et collez le texte ci-dessous :

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Accédez au hub **Données**, cliquez avec le bouton de droite sur **Bases de données**, puis sélectionnez **Actualiser**.
1. Les bases de données suivantes s’affichent à présent :
    - SQLDB (pool SQL)
    - nyctaxi (Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analyser les données des taxis de New York à l’aide de Spark et des notebooks

1. Revenez à votre notebook
1. Créez une cellule de code, entrez le texte ci-dessous, puis exécutez la cellule pour obtenir des exemples de données de taxis à New York chargées dans la base de données Spark `nyctaxi`.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Exécutez le code suivant pour effectuer la même analyse que précédemment avec le pool SQL `SQLDB1`. Ce code enregistre également les résultats de l’analyse dans une table nommée `nyctaxi.passengercountstats` et visualise les résultats.

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

1. Dans les résultats de la cellule, sélectionnez **Graphique** pour afficher les données visualisées
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Personnaliser les données de visualisation des données avec Spark et des notebooks

Les notebooks vous permettent de contrôler le mode de rendu des graphiques. Le code suivant illustre un exemple simple à l’aide des bibliothèques populaires `matplotlib` et `seaborn`. Le type de graphique en courbes sera le même que celui que vous avez vu lors de l’exécution des précédentes requêtes SQL.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```
    
## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Charger des données à partir d’une table Spark dans une table de pools SQL

Charger des données à partir d’une table Spark dans une table de pools `SQLDB1.dbo.Trip` dans une table Spark `nyctaxi.trip`. Ensuite, à l’aide de Spark, nous avons rassemblé les données dans la table Spark `nyctaxi.passengercountstats`. À présent, nous allons copier les données de `nyctaxi.passengercountstats` dans une table de pools SQL appelée `SQLDB1.dbo.PassengerCountStats`. 

Exécutez la cellule ci-dessous dans votre notebook. La table Spark agrégée sera à nouveau copiée dans la table de pools SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analyser les données des taxis de New York dans des bases de données Spark à l’aide de SQL à la demande 

Les tables des bases de données Spark sont automatiquement visibles et interrogeables par SQL à la demande.

1. Dans Synapse Studio, accédez au hub **Développer** et créez un nouveau script SQL
1. Définir **Se connecter à** sur **SQL à la demande** 
1. Collez le texte suivant dans le script et exécutez le script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > Lors de la première exécution d’une requête qui utilise SQL à la demande, il faut environ 10 secondes à SQL à la demande pour collecter les ressources SQL nécessaires pour exécuter vos requêtes. Les requêtes suivantes n’ont pas besoin de cette durée et sont beaucoup plus rapides.
  
## <a name="orchestrate-activities-with-pipelines"></a>Orchestrer des activités avec des pipelines

Vous pouvez orchestrer un large éventail de tâches dans Azure Synapse. Dans cette section, vous verrez à quel point cela est facile.

1. Dans Synapse Studio, accédez au hub **Orchestrer**.
1. Sélectionnez **+** puis sélectionnez **Pipeline**. Un nouveau pipeline sera créé.
1. Accédez au hub Développement et recherchez le notebook que vous avez créé précédemment.
1. Faites glisser ce notebook dans le pipeline.
1. Dans le pipeline, sélectionnez **Ajouter un déclencheur > Nouveau/modifier**.
1. Dans **Choisissez un déclencheur** sélectionnez **Nouveau**, puis dans périodicité, définissez le déclencheur pour qu’il s’exécute toutes les heures.
1. Sélectionnez **OK**.
1. Sélectionnez **Tout publier** et le pipeline s’exécutera toutes les heures.
1. Si vous souhaitez que le pipeline s’exécute maintenant sans attendre l’heure suivante, sélectionnez **Ajouter un déclencheur > Nouveau/modifier**.

## <a name="working-with-data-in-a-storage-account"></a>Utilisation des données dans un compte de stockage

Jusqu’à présent, nous avons abordé les scénarios où les données se trouvaient dans des bases de données de l’espace de travail. À présent, nous allons montrer comment utiliser des fichiers dans des comptes de stockage. Dans ce scénario, nous allons utiliser le compte de stockage principal de l’espace de travail et du conteneur que nous avons spécifiés lors de la création de l’espace de travail.

* Le nom du compte de stockage : `contosolake`
* Le nom du conteneur dans le compte de stockage : `users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Création de fichiers CSV et Parquet dans votre compte de stockage

Exécutez le code suivant dans un notebook. Il crée un fichier CSV et un fichier Parquet dans le compte de stockage

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analyse des données dans un compte de stockage

1. Dans Synapse Studio, accédez au hub **Données**
1. Sélectionnez **Lié**
1. Accédez à **Comptes de stockage > monespacedetravail (Principal - contosolake)**
1. Sélectionner **utilisateurs (Principaux) »**
1. Vous devriez voir un dossier nommé `NYCTaxi`. Celui-ci doit comprendre les deux dossiers `PassengerCountStats.csv` et `PassengerCountStats.parquet`.
1. Accédez au dossier `PassengerCountStats.parquet`.
1. Cliquez avec le bouton de droite sur le fichier parquet à l’intérieur, puis sélectionnez **nouveau notebook**, cela permettra de créer un notebook avec une cellule semblable à celle-ci :

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Exécutez la cellule.
1. Cliquez avec le bouton de droite sur le fichier parquet à l’intérieur, puis sélectionnez **Nouveau script SQL > Sélectionner les 100 premières lignes**, cela permettra de créer un script SQL comme suit :

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. Dans le script, le champ **Joindre à** sera défini sur **SQL à la demande**.
1. Exécutez le script.

## <a name="visualize-data-with-power-bi"></a>Visualiser des données avec Power BI

À partir des données de taxi NYX, nous avons créé des jeux de données agrégés dans deux tables :
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

Vous pouvez lier un espace de travail Power BI à votre espace de travail Synapse. Cela vous permet d’accéder facilement aux données dans votre espace de travail Power BI et de modifier vos rapports Power BI directement dans votre espace de travail Synapse.

### <a name="create-a-power-bi-workspace"></a>Créer un espace de travail Power BI

1. Connectez-vous [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Créez un nouvel espace de travail Power BI nommé `NYCTaxiWorkspace1`.

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>Liez votre espace de travail Synapse à votre nouvel espace de travail Power BI

1. Dans Synapse Studio, accédez à **Gérer > Services liés**.
1. Sélectionnez **+ Nouveau** et sélectionnez **Se connecter à Power BI** et définissez les champs suivants :

    |Paramètre | Valeur suggérée | 
    |---|---|
    |**Nom**|`NYCTaxiWorkspace1`|
    |**Nom de l’espace de travail**|`NYCTaxiWorkspace1`|
        
1. Sélectionnez **Create** (Créer).

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Créer un jeu de données Power BI qui utilise des données dans votre espace de travail Synapse

1. Dans Synapse Studio, accédez à **Développer > Power BI**.
1. Accédez à **NYCTaxiWorkspace1 > Jeux de données Power BI** et sélectionnez **Nouveau jeu de données Power Bi**.
1. Pointez sur la base de données `SQLDB1`, puis sélectionnez **Télécharger le fichier .pbids**.
1. Ouvrez le fichier `.pbids` téléchargé. 
1. Cette opération lance le bureau Power BI et le connecte automatiquement à `SQLDB1` dans votre espace de travail Synapse.
1. Si vous voyez s’afficher une boîte de dialogue appelée **Base de données SQL Server** : a. Sélectionnez **Compte Microsoft**. 
    b. Sélectionnez **Se connecter** pour vous connecter.
    c. Sélectionnez **Connecter**.
1. La boîte de dialogue **Navigateur** s’ouvre. À ce moment, vérifiez la table **PassengerCountStats** et sélectionnez **Charger**.
1. Une boîte de dialogue **Paramètres de connexion** s’affiche. Sélectionnez **DirectQuery** et sélectionnez **OK**
1. Sélectionnez le bouton **Rapport** sur la gauche.
1. Ajoutez **Graphique en, courbes** à votre rapport.
    a. Faites glisser la colonne **PasssengerCount** vers **Visualisations > Axe** b. Faites glisser les colonnes **SumTripDistance** et **AvgTripDistance** vers **Visualisations > Valeurs**.
1. Sous l’onglet **Accueil**, sélectionnez **Publier**.
1. Il vous sera demandé si vous voulez enregistrer vos modifications. Sélectionnez **Enregistrer**.
1. Vous serez invité à choisir un nom de fichier. Choisissez `PassengerAnalysis.pbix` et sélectionnez **Enregistrer**.
1. Vous êtes invité à **Sélectionner une destination**, sélectionnez`NYCTaxiWorkspace1` et sélectionnez **Sélectionner**.
1. Attendez la fin de la publication.

### <a name="configure-authentication-for-your-dataset"></a>Configurer l’authentification pour votre jeu de données

1. Ouvrez [powerbi.microsoft.com](https://powerbi.microsoft.com/) et sélectionnez **Se connecter**
1. À gauche, sous **Espaces de travail** sélectionnez l’espace de travail `NYCTaxiWorkspace1`.
1. Dans cet espace de travail, vous devriez voir un jeu de données appelé `Passenger Analysis` et un rapport appelé `Passenger Analysis`.
1. Pointez sur le jeu de données `PassengerAnalysis` et sélectionnez l’icône avec les trois points puis, sélectionnez **Paramètres**.
1. Dans **Informations d’identification de la source de données**, définissez la **méthode d’authentification** sur **OAuth2** et sélectionnez **Se connecter**.

### <a name="edit-a-report-in-synapse-studio"></a>Modifier un rapport dans Synapse Studio

1. Retournez à Synapse Studio et sélectionnez **Fermer et actualiser** 
1. Accédez au hub **Développer** 
1. Pointez sur **Power BI**, puis cliquez sur le nœud d’actualisation des **Rapports Power BI**.
1. Maintenant, sous **Power BI** vous devriez voir : a. * Sous **NYCTaxiWorkspace1 > Jeux de données Power BI**, un nouveau jeu de données appelé **PassengerAnalysis**.
    b. * Sous **NYCTaxiWorkspace1 > Rapports Power BI**, un nouveau jeu de données appelé **PassengerAnalysis**.
1. Sélectionnez le rapport **PassengerAnalysis**. 
1. Le rapport s’ouvre et vous pouvez maintenant le modifier directement dans Synapse Studio.

## <a name="monitor-activities"></a>Activités d'analyse

1. Dans Synapse Studio, accédez au hub d’analyse.
1. À cet emplacement, vous pouvez voir un historique de toutes les activités qui se produisent dans l’espace de travail et celles qui sont actuellement actives.
1. Explorez les **Exécutions du pipeline**, **Applications Apache Spark** et **Requêtes SQL** et vous pouvez voir ce que vous avez déjà fait dans l’espace de travail.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [Azure Synapse Analytics (Préversion)](overview-what-is.md)

