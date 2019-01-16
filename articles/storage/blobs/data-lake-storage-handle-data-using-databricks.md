---
title: 'Tutoriel : Apprendre à extraire, charger et transférer des opérations à l’aide d’Azure Databricks'
description: Dans ce tutoriel, vous apprenez à extraire des données de la préversion Azure Data Lake Storage Gen2 vers Azure Databricks, à les transformer, puis à les charger dans Azure SQL Data Warehouse.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 12/06/2018
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 6b2812e31174c4e5d61ae9941563e39357de9522
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107087"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Tutoriel : Extraire, transformer et charger des données à l’aide d’Azure Databricks

Dans ce tutoriel, vous utilisez Azure Databricks pour effectuer une opération ETL (extraction, transformation et chargement de données). Vous déplacez des données depuis un compte de stockage Azure vers Azure Data Lake Storage Gen2 compatible avec Azure SQL Data Warehouse.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un espace de travail Azure Databricks.
> * Créer un cluster Spark dans Azure Databricks.
> * Créer un compte compatible avec Azure Data Lake Storage Gen2.
> * Charger des données dans Azure Data Lake Storage Gen2.
> * Créer un notebook dans Azure Databricks.
> * Extraire des données de Data Lake Storage Gen2.
> * Transformer des données dans Azure Databricks.
> * Charger des données dans Azure SQL Data Warehouse.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

* Créez un entrepôt de données SQL Azure, créez une règle de pare-feu au niveau du serveur et connectez-vous au serveur en tant qu’administrateur. Suivez les instructions données dans l’article [Démarrage rapide : Créer un entrepôt de données SQL Azure](../../sql-data-warehouse/create-data-warehouse-portal.md).
* Créez une clé principale de base de données pour l’entrepôt de données SQL Azure. Suivez les instructions données dans l’article [Créer une clé principale de base de données](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
* [Créez un compte Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* Téléchargez (**small_radio_json.json**) à partir du dépôt [U-SQL Examples and Issue Tracking](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) et prenez note du chemin où vous enregistrez le fichier.
* Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-the-workspace"></a>Créer l’espace de travail

Dans cette section, vous créez un espace de travail Azure Databricks en utilisant le portail Azure.

1. Dans le portail Azure, sélectionnez **Créer une ressource** >  **Analytique** > **Azure Databricks**.

    ![Databricks sur le portail Azure](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Databricks sur le portail Azure")

1. Sous **Service Azure Databricks**, renseignez les valeurs suivantes pour créer un espace de travail Databricks :

    |Propriété  |Description  |
    |---------|---------|
    |**Nom de l’espace de travail**     | Fournissez un nom pour votre espace de travail Databricks.        |
    |**Abonnement**     | Sélectionnez votre abonnement Azure dans la liste déroulante.        |
    |**Groupe de ressources**     | Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Lieu**     | Sélectionnez **USA Ouest 2**.        |
    |**Niveau tarifaire**     |  Sélectionnez **Standard**.     |

    ![Créer un espace de travail Azure Databricks](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Créer un espace de travail Azure Databricks")

1. Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

1. La création du compte prend quelques minutes. Pendant la création du compte, le portail affiche la vignette **Envoi du déploiement pour Azure Databricks** à droite. Pour superviser l’état de l’opération, regardez la barre de progression située en haut.

    ![Vignette de déploiement Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Vignette de déploiement Databricks")

## <a name="create-the-spark-cluster"></a>Créer le cluster Spark

Pour effectuer les opérations décrites dans ce tutoriel, vous avez besoin d’un cluster Spark. Effectuez les étapes ci-après pour créer le cluster Spark.

1. Dans le portail Azure, accédez à l’espace de travail Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

1. Vous êtes redirigé vers le portail Azure Databricks. Dans le portail, sélectionnez **Cluster**.

    ![Databricks sur Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks sur Azure")

1. Dans la page **Nouveau cluster**, renseignez les valeurs pour créer un cluster.

    ![Créer un cluster Databricks Spark sur Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Créer un cluster Databricks Spark sur Azure")

1. Renseignez les champs suivants et acceptez les valeurs par défaut pour les autres champs :

    * Entrez un nom pour le cluster.
    * Pour cet article, créez un cluster avec le runtime **5.1**.
    * Veillez à cocher la case **Arrêter après \_\_ minutes d’inactivité**. Si le cluster n’est pas utilisé, indiquez une durée (en minutes) pour arrêter le cluster.

1. Sélectionnez **Créer un cluster**.

Une fois que le cluster est en cours d’exécution, vous pouvez y attacher des notebooks et exécuter des travaux Spark.

## <a name="create-a-file-system"></a>Créer un système de fichiers

Pour stocker des données dans votre compte de stockage Data Lake Storage Gen2, vous avez besoin de créer un système de fichiers.

Tout d’abord, vous créez un notebook dans votre espace de travail Azure Databricks, puis vous exécutez des extraits de code pour créer le système de fichiers dans votre compte de stockage.

1. Dans le [portail Azure](https://portal.azure.com), accédez à l’espace de travail Azure Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

1. À gauche, sélectionnez **Espace de travail**. Dans la liste déroulante **Espace de travail**, sélectionnez **Créer** > **Notebook**.

    ![Créer un notebook dans Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Créer un notebook dans Databricks")

1. Dans la boîte de dialogue **Créer un bloc-notes**, entrez un nom pour le bloc-notes. Sélectionnez **Scala** comme langage, puis sélectionnez le cluster Spark que vous avez créé précédemment.

    ![Fournir des détails pour un notebook dans Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "fournir des détails pour un notebook dans Databricks")

    Sélectionnez **Créer**.

1. Entrez le code suivant dans la première cellule du notebook et exécutez le code. Remplacez les espaces réservés affichés entre crochets dans l’exemple par vos propres valeurs :

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
     
    dbutils.fs.mount(
        source = "abfss://<file-system-name>@<account-name>.dfs.core.windows.net/[<directory-name>]",
        mount_point = "/mnt/<mount-name>",
        extra_configs = configs)
    ```

1. Appuyez sur les touches Maj+Entrée pour exécuter le code.

Le système de fichiers est créé pour le compte de stockage.

## <a name="upload-the-sample-data"></a>Charger les exemples de données

L’étape suivante consiste à charger un exemple de fichier de données dans le compte de stockage à transformer ultérieurement dans Azure Databricks.

Chargez l’exemple de données que vous avez téléchargé dans votre compte de stockage. La méthode que vous utilisez pour charger les données dans votre compte de stockage varie selon que l’espace de noms hiérarchique est activé ou non.

Vous pouvez utiliser Azure Data Factory, distp ou AzCopy (version 10) pour effectuer le chargement. AzCopy version 10 est actuellement disponible uniquement par le biais de la préversion. Pour utiliser AzCopy, collez le code suivant dans une fenêtre de commande :

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
```

## <a name="extract-the-data"></a>Extraire les données

Pour utiliser l’exemple de données dans Databricks, vous avez besoin d’extraire les données de votre compte de stockage.

Revenez à votre notebook Databricks et entrez le code suivant dans une nouvelle cellule de votre notebook.

Ajoutez l’extrait de code suivant dans une cellule vide. Remplacez les espaces réservés indiqués entre crochets par les valeurs que vous avez enregistrées précédemment dans le compte de stockage.

```scala
dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
```

Appuyez sur les touches Maj+Entrée pour exécuter le code.

Vous pouvez maintenant charger l’exemple de fichier JSON en tant que trame de données dans Azure Databricks. Collez le code suivant dans une nouvelle cellule. Remplacez les espaces réservés indiqués entre crochets par vos valeurs.

```scala
val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
```

Appuyez sur les touches Maj+Entrée pour exécuter le code.

Exécutez le code suivant pour voir le contenu du dataframe :

```scala
df.show()
```

Le résultat ressemble à ce qui suit :

```bash
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
|               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
| El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
| Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
| Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
...
...
```

Vous avez extrait les données d’Azure Data Lake Storage Gen2 dans Azure Databricks.

## <a name="transform-the-data"></a>Transformer les données

Le fichier brut de l’exemple de données **small_radio_json.json** capture l’audience d’une station de radio et présente différentes colonnes. Dans cette section, vous transformez les données pour récupérer uniquement des colonnes spécifiques du jeu de données.

Tout d’abord, récupérez uniquement les colonnes **firstName**, **lastName**, **gender**, **location** et **level** du dataframe que vous avez créé.

```scala
val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
```

Vous recevez une sortie similaire à l’extrait de code suivant :

```bash
+---------+----------+------+--------------------+-----+
|firstname|  lastname|gender|            location|level|
+---------+----------+------+--------------------+-----+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
+---------+----------+------+--------------------+-----+
```

Vous pouvez transformer ces données pour renommer la colonne **level** en **subscription_type**.

```scala
val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
renamedColumnsDF.show()
```

Vous recevez une sortie similaire à l’extrait de code suivant.

```bash
+---------+----------+------+--------------------+-----------------+
|firstname|  lastname|gender|            location|subscription_type|
+---------+----------+------+--------------------+-----------------+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
+---------+----------+------+--------------------+-----------------+
```

## <a name="load-the-data"></a>Chargement des données

Dans cette section, vous chargez les données transformées dans Azure SQL Data Warehouse. Vous utilisez le connecteur Azure SQL Data Warehouse pour Azure Databricks pour charger directement un dataframe sous forme de table dans un entrepôt de données SQL.

Le connecteur SQL Data Warehouse utilise le stockage Blob Azure comme stockage temporaire pour charger des données entre Azure Databricks et Azure SQL Data Warehouse. Vous commencez donc par fournir la configuration pour vous connecter au compte de stockage. Vous devez déjà avoir créé le compte dans le cadre des prérequis de cet article.

Fournissez la configuration pour accéder au compte de Stockage Azure à partir d’Azure Databricks.

```scala
val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
val fileSystemName = "<FILE_SYSTEM_NAME>"
val accessKey =  "<ACCESS_KEY>"
```

Spécifiez un dossier temporaire à utiliser lors du déplacement des données entre Azure Databricks et Azure SQL Data Warehouse.

```scala
val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
```

Exécutez l’extrait de code suivant pour stocker les clés d’accès du Stockage Blob Azure dans la configuration. Cette action vous dispense d’avoir à conserver la clé d’accès dans le notebook en texte brut.

```scala
val acntInfo = "fs.azure.account.key."+ storageURI
sc.hadoopConfiguration.set(acntInfo, accessKey)
```

Indiquez les valeurs pour vous connecter à l’instance Azure SQL Data Warehouse. Vous devez avoir créé un entrepôt de données SQL dans le cadre des prérequis.

```scala
//SQL Data Warehouse related settings
val dwDatabase = "<DATABASE NAME>"
val dwServer = "<DATABASE SERVER NAME>" 
val dwUser = "<USER NAME>"
val dwPass = "<PASSWORD>"
val dwJdbcPort =  "1433"
val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
```

Exécutez l’extrait de code suivant pour charger le dataframe transformé, **renamedColumnsDF**, en tant que table dans un entrepôt de données SQL. Cet extrait de code crée une table appelée **SampleTable** dans la base de données SQL.

```scala
spark.conf.set(
    "spark.sql.parquet.writeLegacyFormat",
    "true")
    
renamedColumnsDF.write
    .format("com.databricks.spark.sqldw")
    .option("url", sqlDwUrlSmall) 
    .option("dbtable", "SampleTable")
    .option( "forward_spark_azure_storage_credentials","True")
    .option("tempdir", tempDir)
    .mode("overwrite")
    .save()
```

Connectez-vous à la base de données SQL et vérifiez que vous y voyez une base de données nommée **SampleTable**.

![Vérifier la présence de l’exemple de table](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "Vérifier la présence de l’exemple de table")

Exécutez une requête SELECT pour vérifier le contenu de la table. Celle-ci doit avoir les mêmes données que celles présentes dans le dataframe **renamedColumnsDf**.

![Vérifier le contenu de l’exemple de table](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "Vérifier le contenu de l’exemple de table")

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous avez terminé le tutoriel, vous pouvez terminer le cluster. Dans l’espace de travail Azure Databricks, sélectionnez **Clusters** à gauche. Pour terminer le cluster, sous **Actions**, pointez sur les points de suspension (...) et sélectionnez l’icône **Terminer**.

![Arrêter un cluster Databricks](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Arrêter un cluster Databricks")

Si vous ne terminez pas le cluster manuellement, il s’arrête automatiquement, à condition d’avoir coché la case **Arrêter après \_\_ minutes d’inactivité** lors de la création du cluster. Dans ce cas, le cluster s’arrête automatiquement s’il a été inactif pendant la période renseignée.

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant pour découvrir le streaming des données en temps réel dans Azure Databricks à l’aide d’Azure Event Hubs.

> [!div class="nextstepaction"]
>[Streaming des données dans Azure Databricks à l’aide d’Event Hubs](../../azure-databricks/databricks-stream-from-eventhubs.md)
