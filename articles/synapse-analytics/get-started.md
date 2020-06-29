---
title: 'Tutoriel : Bien démarrer avec Azure Synapse Analytics'
description: Dans ce tutoriel, vous allez découvrir les étapes de base à suivre pour configurer et utiliser Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791858"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Bien démarrer avec Azure Synapse Analytics

Ce tutoriel vous guide dans les étapes de base à suivre pour configurer et utiliser Azure Synapse Analytics.

## <a name="prepare-a-storage-account"></a>Préparer un compte de stockage

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Créez un compte de stockage avec les paramètres suivants :

    |Onglet|Paramètre | Valeur suggérée | Description |
    |---|---|---|---|
    |Concepts de base|**Nom du compte de stockage**| Vous pouvez lui donner le nom que vous voulez.|Dans ce document, nous l’appellerons **contosolake**.|
    |Concepts de base|**Type de compte**|Doit être défini sur **StorageV2**.||
    |Concepts de base|**Lieu**|Choisissez un emplacement.| Nous vous conseillons de choisir votre espace de travail Azure Synapse Analytics et votre compte Azure Data Lake Storage Gen2 dans la même région.|
    |Avancé|**Data Lake Storage Gen2**|**Activé**| Azure Synapse fonctionne uniquement avec les comptes de stockage dans lesquels ce paramètre est activé.|
    |||||

1. Après avoir créé le compte de stockage, sélectionnez **Contrôle d’accès (IAM)** dans le volet gauche. Attribuez ensuite les rôles suivants ou vérifiez qu’ils sont déjà attribués :
    1. Attribuez-vous le rôle **Propriétaire**.
    1. Attribuez-vous le rôle **Propriétaire des données Blob du stockage**.
1. Dans le volet de gauche, sélectionnez **Conteneurs** et créez un conteneur.
1. Attribuez au conteneur un nom de votre choix. Dans ce document, nous appellerons le conteneur **users**.
1. Acceptez le paramètre par défaut **Niveau d’accès public**, puis sélectionnez **Créer**.

À l’étape suivante, vous configurerez votre espace de travail Azure Synapse pour utiliser ce compte de stockage comme compte de stockage « principal » et le conteneur pour stocker les données de l’espace de travail. L’espace de travail stocke les données dans des tables Apache Spark. Il stocke les journaux des applications Spark dans un dossier appelé **/synapse/workspacename**.

## <a name="create-a-synapse-workspace"></a>Créer un espace de travail Synapse

1. Ouvrez le [Portail Azure](https://portal.azure.com) et, en haut, recherchez **Synapse**.
1. Dans les résultats de la recherche, sous **Services**, sélectionnez **Azure Synapse Analytics (préversion des espaces de travail)** .
1. Sélectionnez **Ajouter** pour créer un espace de travail avec ces paramètres :

    |Onglet|Paramètre | Valeur suggérée | Description |
    |---|---|---|---|
    |Concepts de base|**Nom de l’espace de travail**|Vous pouvez lui donner le nom que vous voulez.| Dans ce document, nous l’appellerons **myworkspace**.|
    |Concepts de base|**Région**|Choisissez la même région que le compte de stockage.|

1. Sous **sélectionnez Data Lake Storage Gen 2**, sélectionnez le compte et le conteneur que vous avez créés précédemment.
1. Sélectionnez **Vérifier + créer** > **Créer**. Votre espace de travail est prêt en quelques minutes.

## <a name="verify-access-to-the-storage-account"></a>Vérifier l’accès au compte de stockage

Les identités managées pour votre espace de travail Azure Synapse ont peut-être déjà accès au compte de stockage. Vérifiez ce point en effectuant ces étapes :

1. Ouvrez le [Portail Azure](https://portal.azure.com) et le compte de stockage principal choisi pour votre espace de travail.
1. Dans le volet de gauche, sélectionnez **Contrôle d’accès (IAM)** .
1. Attribuez les rôles suivants ou vérifiez qu’ils sont déjà attribués. Nous utilisons le même nom pour l’identité de l’espace de travail et le nom de l’espace de travail.
    1. Pour le rôle **Contributeur aux données Blob du stockage** sur le compte de stockage, affectez **myworkspace** comme identité de l’espace de travail.
    1. Attribuez le nom **myworkspace** à l’espace de travail.

1. Sélectionnez **Enregistrer**.

## <a name="open-synapse-studio"></a>Ouvrir Synapse Studio

Après avoir créé votre espace de travail Azure Synapse, vous pouvez ouvrir Synapse Studio de deux manières :

* Ouvrez votre espace de travail Synapse dans le [Portail Azure](https://portal.azure.com). En haut de la section **Vue d’ensemble**, sélectionnez **Lancer Synapse Studio**.
* Accédez à https://web.azuresynapse.net et connectez-vous à votre espace de travail.

## <a name="create-a-sql-pool"></a>Créer un pool SQL

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Gérer** > **Pools SQL**.
1. Sélectionnez **Nouveau** et renseignez les paramètres suivants :

    |Paramètre | Valeur suggérée | 
    |---|---|---|
    |**Nom du pool SQL**| **SQLDB1**|
    |**Niveau de performances**|**DW100C**|
    |||

1. Sélectionnez **Vérifier + créer** > **Créer**. Votre pool SQL sera prêt en quelques minutes. Votre pool SQL est associé à une base de données du pool SQL qui est également appelée **SQLDB1**.

Un pool SQL consomme des ressources facturables tant qu’il est actif. Vous pouvez suspendre le pool plus tard pour réduire les coûts.

## <a name="create-an-apache-spark-pool"></a>Créer un pool Apache Spark

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Gérer** > **Pools Apache Spark**.
1. Sélectionnez **Nouveau** et renseignez les paramètres suivants :

    |Paramètre | Valeur suggérée | 
    |---|---|---|
    |**Nom du pool Apache Spark**|**Spark1**
    |**Taille du nœud**| **Petite**|
    |**Nombre de nœuds**| Définissez la valeur minimale sur 3 et la valeur maximale sur 3|

1. Sélectionnez **Vérifier + créer** > **Créer**. Votre pool Apache Spark sera prêt en quelques secondes.

> [!NOTE]
> Malgré le nom, un pool Apache Spark n’est pas identique à un pool SQL. Il s’agit simplement de quelques métadonnées de base que vous utilisez pour indiquer à l’espace de travail Azure Synapse comment interagir avec Spark.

Comme ce sont des métadonnées, les pools Spark ne peuvent pas être démarrés ni arrêtés.

Lorsque vous effectuez une activité Spark dans Azure Synapse, vous spécifiez le pool Spark à utiliser. Le pool indique à Azure Synapse la quantité de ressources Spark à utiliser. Vous payez uniquement les ressources consommées. Quand vous cessez d’utiliser activement le pool, les ressources expirent automatiquement et sont recyclées.

> [!NOTE]
> Les bases de données Spark sont créées indépendamment à partir des pools Spark. Un espace de travail est toujours associé à une base de données Spark par défaut appelée **default**. Vous pouvez créer d’autres bases de données Spark.

## <a name="the-sql-on-demand-pool"></a>Le pool SQL à la demande

Chaque espace de travail est fourni avec un pool prédéfini appelé **SQL à la demande**. Ce pool ne peut pas être supprimé. Le pool SQL à la demande vous permet d’utiliser SQL sans devoir créer ni gérer un pool SQL dans Azure Synapse.

Contrairement aux autres types de pools, la facturation du pool SQL à la demande est basée sur la quantité de données numérisées (pas sur la quantité de ressources consommées) pour exécuter la requête.

* Le pool SQL à la demande possède ses propres bases de données SQL à la demande qui existent indépendamment de tout pool SQL à la demande.
* Un espace de travail a toujours un seul pool SQL à la demande nommé **SQL à la demande**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Charger les données de l’exemple NYC Taxi dans la base de données SQLDB1

1. Dans Synapse Studio, sur le menu bleu tout en haut, sélectionnez l’icône **?** .
1. Sélectionnez **Démarrer** > **Hub Démarrer**.
1. Dans la carte **Exemple de données de requête**, sélectionnez le pool SQL nommé **SQLDB1**.
1. Sélectionnez **Données de requête**. Une notification de chargement de l’exemple de données s’affiche brièvement. La barre d’état de couleur bleu clair située en haut de Synapse Studio indique que les données sont en cours de chargement dans SQLDB1.
1. Quand la barre d’état devient verte, fermez-la.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explorer les données NYC Taxi dans le pool SQL

1. Dans Synapse Studio, accédez au hub **Données**.
1. Accédez à **SQLDB1** > **Tables**. Vous voyez plusieurs tables chargées.
1. Cliquez avec le bouton droit sur la table **dbo.Trip** et sélectionnez **Nouveau script SQL** > **Sélectionner les 100 premières lignes**.
1. Patientez pendant la création et l’exécution du nouveau script SQL.
1. Notez que, en haut du script SQL, **Se connecter à** est automatiquement défini sur le pool SQL appelé **SQLDB1**.
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

    Cette requête montre de quelle manière les distances totales et la distance moyenne des trajets sont liées au nombre de passagers.
1. Dans la fenêtre de résultat du script SQL, changez la **Vue** à **Graphique** pour visualiser les résultats sous forme de graphique en courbes.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Charger les données NYC Taxi dans la base de données Spark appelée nyctaxi

Des données sont disponibles dans une table dans **SQLDB1**. Chargez-les dans une base de données Spark nommée **nyctaxi**.

1. Dans Synapse Studio, accédez au hub **Développer**.
1. Sélectionnez **+**  > **Notebook**.
1. En haut du notebook, définissez la valeur **Joindre à** sur **Spark1**.
1. Sélectionnez **Ajouter du code** pour ajouter une cellule de code du notebook, puis collez le texte suivant :

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Accédez au hub **Données**, cliquez avec le bouton droit sur **Bases de données**, puis sélectionnez **Actualiser**. Vous devez maintenant voir ces deux bases de données :

    - **SQLDB1** (pool SQL)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analyser les données des taxis de New York à l’aide de Spark et des notebooks

1. Retournez dans votre notebook.
1. Créez une nouvelle cellule de code et entrez le texte suivant. Exécutez ensuite la cellule pour afficher les données NYC Taxi que nous avons chargées dans la base de données Spark **nyctaxi**.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Exécutez le code suivant pour effectuer la même analyse que celle faite précédemment avec le pool SQL **SQLDB1**. Ce code enregistre les résultats de l’analyse dans une table nommée **nyctaxi.passengercountstats** et crée une visualisation des résultats.

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

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Personnaliser la visualisation des données avec Spark et des notebooks

Vous pouvez contrôler le rendu des graphiques à l’aide de notebooks. Le code suivant présente un exemple simple. Il utilise les bibliothèques populaires **matplotlib** et **seaborn**. Le code effectue le rendu dans le même type de graphique en courbes que les requêtes SQL que nous avons exécutées précédemment.

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

Nous avions copié les données de la table **SQLDB1.dbo.Trip** du pool SQL dans la table Spark **nyctaxi.trip**. Ensuite, dans Spark, nous avions agrégé les données dans la table Spark **nyctaxi.passengercountstats**. Nous allons maintenant copier les données de **nyctaxi.passengercountstats** dans une table du pool SQL appelée **SQLDB1.dbo.PassengerCountStats**.

Exécutez la cellule suivante dans votre notebook. La table Spark agrégée est de nouveau copiée dans la table du pool SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analyser les données NYC Taxi dans des bases de données Spark à l’aide de SQL à la demande

Les tables des bases de données Spark sont automatiquement visibles et interrogeables par SQL à la demande.

1. Dans Synapse Studio, accédez au hub **Développer** et créez un autre script SQL.
1. Définissez **Se connecter à** sur **SQL à la demande**.
1. Collez le texte suivant dans le script et exécutez le script.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Lors de la première exécution d’une requête qui utilise SQL à la demande, il faut environ 10 secondes à SQL à la demande pour collecter les ressources SQL nécessaires à l’exécution des requêtes. Les requêtes suivantes s’exécutent beaucoup plus vite.
  
## <a name="orchestrate-activities-with-pipelines"></a>Orchestrer des activités avec des pipelines

Vous pouvez orchestrer un large éventail de tâches dans Azure Synapse.

1. Dans Synapse Studio, accédez au hub **Orchestrer**.
1. Sélectionnez **+**  > **Pipeline** pour créer un pipeline.
1. Accédez au hub **Développer** et recherchez le notebook que vous avez créé précédemment.
1. Faites glisser ce notebook dans le pipeline.
1. Dans le pipeline, sélectionnez **Ajouter un déclencheur** > **Nouveau/modifier**.
1. Dans **Choisissez un déclencheur**, sélectionnez **Nouveau** et, pour la **périodicité**, définissez le déclencheur pour qu’il s’exécute toutes les heures.
1. Sélectionnez **OK**.
1. Sélectionnez **Publier tout**. Le pipeline s’exécute toutes les heures.
1. Pour lancer l’exécution du pipeline immédiatement, sans attendre l’heure suivante, sélectionnez **Ajouter un déclencheur** > **Nouveau/modifier**.

## <a name="work-with-data-in-a-storage-account"></a>Utiliser des données dans un compte de stockage

Jusqu’à présent, nous avons vu des scénarios où les données se trouvent dans des bases de données de l’espace de travail. Maintenant, nous allons voir comment utiliser des fichiers dans des comptes de stockage. Dans ce scénario, nous utilisons le compte de stockage principal de l’espace de travail et le conteneur que nous avons spécifiés au moment de la création de l’espace de travail.

* Le nom du compte de stockage est **contosolake**
* Le nom du conteneur dans le compte de stockage est **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Créer des fichiers CSV et Parquet dans votre compte de stockage

Exécutez le code suivant dans un notebook. Il crée un fichier CSV et un fichier Parquet dans le compte de stockage.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analyser des données dans un compte de stockage

1. Dans Synapse Studio, accédez au hub **Données**, puis sélectionnez **Liées**.
1. Accédez à **Comptes de stockage** > **myworkspace (Principal - contosolake)** .
1. Sélectionnez **utilisateurs (Principaux) »** . Vous devez normalement voir le dossier **NYCTaxi**, qui contient les deux dossiers **PassengerCountStats.csv** et **PassengerCountStats.parquet**.
1. Ouvrez le dossier **PassengerCountStats.parquet**. Ce dossier contient un fichier Parquet avec un nom comme *part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet*.
1. Cliquez avec le bouton droit sur **.parquet**, puis sélectionnez **nouveau notebook**. Cette action crée un notebook contenant une cellule semblable à celle-ci :

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Exécutez la cellule.
1. Cliquez avec le bouton droit sur le fichier Parquet qui s’y trouve, puis sélectionnez **Nouveau script SQL** > **Sélectionner les 100 premières lignes**. Cela crée un script SQL semblable à ceci :

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     Dans le script, le champ **Joindre à** est défini sur **SQL à la demande**.

1. Exécutez le script.

## <a name="visualize-data-with-power-bi"></a>Visualiser des données avec Power BI

À partir des données NYC Taxi, nous avons créé des jeux de données agrégés dans deux tables :
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

Vous pouvez lier un espace de travail Power BI à votre espace de travail Azure Synapse. Cela vous permet d’accéder facilement aux données dans votre espace de travail Power BI. Vous pouvez modifier vos rapports Power BI directement dans votre espace de travail Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Création d’un espace de travail Power BI

1. Connectez-vous à [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Créez un espace de travail Power BI nommé **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Lier votre espace de travail Azure Synapse à votre nouvel espace de travail Power BI

1. Dans Synapse Studio, accédez à **Gérer** > **Services liés**.
1. Sélectionnez **Nouveau** > **Se connecter à Power BI**, puis définissez les champs suivants :

    |Paramètre | Valeur suggérée | 
    |---|---|
    |**Nom**|**NYCTaxiWorkspace1**|
    |**Nom de l’espace de travail**|**NYCTaxiWorkspace1**|

1. Sélectionnez **Create** (Créer).

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Créer un jeu de données Power BI qui utilise des données dans votre espace de travail Azure Synapse

1. Dans Synapse Studio, accédez au hub **Développer** > **Power BI**.
1. Accédez à **NYCTaxiWorkspace1** > **Jeux de données Power BI** et sélectionnez **Nouveau jeu de données Power BI**.
1. Pointez sur la base de données **SQLDB1**, puis sélectionnez **Télécharger le fichier .pbids**.
1. Ouvrez le fichier **.pbids** téléchargé. Power BI Desktop s’ouvre et se connecte automatiquement à **SQLDB1** dans votre espace de travail Azure Synapse.
1. Si vous voyez s’afficher une boîte de dialogue appelée **Base de données SQL Server** :
    1. Sélectionnez **Compte Microsoft**.
    1. Sélectionnez **Se connecter** et connectez-vous à votre compte.
    1. Sélectionnez **Connecter**.
1. Une fois la boîte de dialogue **Navigateur** ouverte, recherchez la table **PassengerCountStats** et sélectionnez **Charger**.
1. Dans la boîte de dialogue **Paramètres de connexion** qui s’affiche, sélectionnez **DirectQuery** > **OK**.
1. Sélectionnez le bouton **Rapport** sur le côté gauche.
1. Ajoutez **Graphique en, courbes** à votre rapport.
    1. Faites glisser la colonne **PassengerCount** vers **Visualisations** > **Axe**.
    1. Faites glisser les colonnes **SumTripDistance** et **AvgTripDistance** vers **Visualisations** > **Valeurs**.
1. Sous l’onglet **Accueil**, sélectionnez **Publier**.
1. Cliquez sur **Enregistrer** pour enregistrer vos modifications.
1. Choisissez le nom de fichier **PassengerAnalysis.pbix**, puis sélectionnez **Enregistrer**.
1. Dans **Sélectionner une destination**, choisissez **NYCTaxiWorkspace1**, puis cliquez sur **Sélectionner**.
1. Attendez la fin de la publication.

### <a name="configure-authentication-for-your-dataset"></a>Configurer l’authentification pour votre jeu de données

1. Ouvrez [powerbi.microsoft.com](https://powerbi.microsoft.com/) et sélectionnez **Se connecter**.
1. Sur le côté gauche, sous **Espaces de travail**, sélectionnez l’espace de travail **NYCTaxiWorkspace1**.
1. Dans cet espace de travail, recherchez un jeu de données appelé **Passenger Analysis** et un rapport appelé **Passenger Analysis**.
1. Pointez sur le jeu de données **PassengerAnalysis**, sélectionnez les points de suspension (...), puis sélectionnez **Paramètres**.
1. Dans **Informations d’identification de la source de données**, définissez la **méthode d’authentification** sur **OAuth2**, puis sélectionnez **Se connecter**.

### <a name="edit-a-report-in-synapse-studio"></a>Modifier un rapport dans Synapse Studio

1. Retournez dans Synapse Studio et sélectionnez **Fermer et actualiser**.
1. Accédez au hub **Développer**.
1. Pointez sur **Power BI**, puis sélectionnez l’icône pour actualiser le nœud **Rapports Power BI**.
1. Sous **Power BI** vous devez normalement voir :
    1. Sous **NYCTaxiWorkspace1** > **Jeux de données Power BI**, un nouveau jeu de données appelé **PassengerAnalysis**.
    1. Sous **NYCTaxiWorkspace1** > **Rapports Power BI**, un nouveau rapport appelé **PassengerAnalysis**.
1. Sélectionnez le rapport **PassengerAnalysis**. Le rapport s’ouvre. Vous pouvez le modifier directement dans Synapse Studio.

## <a name="monitor-activities"></a>Activités d'analyse

1. Dans Synapse Studio, accédez au hub **Superviser**.
1. À cet emplacement, vous pouvez voir un historique de toutes les activités qui se produisent dans l’espace de travail et celles qui sont actuellement actives.
1. Explorez les activités **Exécutions du pipeline**, **Applications Apache Spark** et **Requêtes SQL** pour voir tout ce que vous avez déjà fait dans l’espace de travail.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur [Azure Synapse Analytics (espaces de travail, préversion)](overview-what-is.md).

