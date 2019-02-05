---
title: 'Tutoriel : Apprendre à extraire, charger et transférer des opérations à l’aide d’Azure Databricks'
description: Dans ce tutoriel, vous apprenez à extraire des données de la préversion Azure Data Lake Storage Gen2 vers Azure Databricks, à les transformer, puis à les charger dans Azure SQL Data Warehouse.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 01/14/2019
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4d0ff4941405f09c2231b9cde16f4e75e2b88b4b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251671"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Tutoriel : Extraire, transformer et charger des données à l’aide d’Azure Databricks

Dans ce tutoriel, vous allez effectuer une opération ETL (extraction, transformation et chargement de données) à l’aide d’Azure Databricks. Vous extrayez des données d’Azure Data Lake Storage Gen2 dans Azure Databricks, exécutez des transformations sur les données dans Azure Databricks, puis chargez les données transformées dans Azure SQL Data Warehouse.

Les étapes décrites dans ce didacticiel utilisent le connecteur SQL Data Warehouse pour Azure Databricks, afin de transférer des données à Azure Databricks. Ce connecteur utilise ensuite le Stockage Blob Azure en tant que stockage temporaire pour les données transférées entre un cluster Azure Databricks et Azure SQL Data Warehouse.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Créer un espace de travail Azure Databricks.
> * Créer un cluster Spark dans Azure Databricks.
> * Créer un système de fichiers, puis charger les données dans Azure Data Lake Storage Gen2.
> * Créer un principal de service.
> * Extraire des données à partir de Data Lake Store.
> * Transformer des données dans Azure Databricks.
> * Charger des données dans Azure SQL Data Warehouse.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

> [!div class="checklist"]
> * Créez un entrepôt de données SQL Azure, créez une règle de pare-feu au niveau du serveur et connectez-vous au serveur en tant qu’administrateur. Consultez [Démarrage rapide : Créer un entrepôt de données SQL Azure](../../sql-data-warehouse/create-data-warehouse-portal.md).
> * Créez une clé principale de base de données pour l’entrepôt de données SQL Azure. Consultez [Créer une clé principale de base de données](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
> * Créez un compte Azure Data Lake Storage Gen2. Consultez [Créer un compte Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
> * Créez un compte de stockage Blob Azure et un conteneur dans celui-ci. Consultez [Démarrage rapide : Créez un compte de stockage Blob Azure](storage-quickstart-blobs-portal.md).
> * Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

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

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Créer un cluster Spark dans Azure Databricks

1. Dans le portail Azure, accédez à l’espace de travail Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. Vous êtes redirigé vers le portail Azure Databricks. Dans le portail, sélectionnez **Cluster**.

    ![Databricks sur Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks sur Azure")

3. Dans la page **Nouveau cluster**, renseignez les valeurs pour créer un cluster.

    ![Créer un cluster Databricks Spark sur Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Créer un cluster Databricks Spark sur Azure")

4. Renseignez les champs suivants et acceptez les valeurs par défaut pour les autres champs :

    * Entrez un nom pour le cluster.

    * Pour cet article, créez un cluster avec le runtime **5.1**.

    * Veillez à cocher la case **Arrêter après \_\_ minutes d’inactivité**. Si le cluster n’est pas utilisé, indiquez une durée (en minutes) pour arrêter le cluster.

    * Sélectionnez **Créer un cluster**. Une fois que le cluster est en cours d’exécution, vous pouvez y attacher des notebooks et exécuter des travaux Spark.

## <a name="create-a-file-system-and-upload-sample-data"></a>Créer un système de fichiers et charger des exemples de données

Tout d’abord, vous créez un système de fichiers dans votre compte Data Lake Storage Gen2. Ensuite, vous allez charger un exemple de fichier de données sur Data Lake Store. Vous allez utiliser ce fichier ultérieurement dans Azure Databricks pour exécuter quelques transformations.

1. Téléchargez l’exemple de fichier de données [small_radio_json.json](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) sur votre système de fichiers local.

2. À partir du [portail Azure](https://portal.azure.com/), accédez au compte Data Lake Storage Gen2 que vous avez créé comme prérequis de ce tutoriel.

3. Dans la page **Vue d’ensemble** du compte de stockage, sélectionnez **Ouvrir dans l’Explorateur**.

   ![Ouvrir l’Explorateur Stockage](./media/data-lake-storage-handle-data-using-databricks/data-lake-storage-open-storage-explorer.png "Ouvrir l’Explorateur Stockage")

4. Sélectionnez **Ouvrir l’Explorateur Stockage Azure** pour ouvrir l’Explorateur de stockage.

   ![Deuxième invite Ouvrir l’Explorateur Stockage](./media/data-lake-storage-handle-data-using-databricks/data-lake-storage-open-storage-explorer-2.png "Deuxième invite Ouvrir l’Explorateur Stockage")

   L’Explorateur Stockage s’ouvre. Vous pouvez créer un système de fichiers et charger des exemples de données en suivant les instructions de cette rubrique : [Démarrage rapide : Utiliser l’Explorateur Stockage Azure pour gérer les données dans un compte Azure Data Lake Storage Gen2](data-lake-storage-explorer.md).

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Créer un principal du service

Créez un principal du service en suivant l’aide fournie dans cette rubrique : [Guide pratique pour Utilisez le portail pour créer une application Azure AD et un principal du service pouvant accéder aux ressources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Vous devrez faire certaines choses spécifiques pendant que vous suivrez les étapes décrites dans cet article.

:heavy_check_mark: Au cours des étapes indiquées dans la section [Créer une application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) de l’article, veillez à affecter l’URI de point de terminaison que vous venez de collecter au champ **URL de connexion** dans la boîte de dialogue **Créer**.

:heavy_check_mark: Au cours des étapes indiquées dans la section [Attribuer un rôle à l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) de l’article, veillez à affecter le **Rôle Contributeur de Stockage Blob** à votre application.

:heavy_check_mark: Au cours des étapes indiquées dans la section [Obtenir les valeurs de connexion](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) de l’article, collez les valeurs de l’ID de locataire, de l’ID d’application et de la clé d’authentification dans un fichier texte. Vous en aurez besoin bientôt.
Tout d’abord, vous créez un notebook dans votre espace de travail Azure Databricks, puis vous exécutez des extraits de code pour créer le système de fichiers dans votre compte de stockage.

## <a name="extract-data-from-the-data-lake-store"></a>Extraire des données à partir de Data Lake Store

Dans cette section, vous allez créer un bloc-notes dans l’espace de travail Azure Databricks, puis exécuter les extraits de code afin d’extraire les données de Data Lake Store et les importer dans Azure Databricks.

1. Dans le [portail Azure](https://portal.azure.com), accédez à l’espace de travail Azure Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. À gauche, sélectionnez **Espace de travail**. Dans la liste déroulante **Espace de travail**, sélectionnez **Créer** > **Notebook**.

    ![Créer un notebook dans Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Créer un notebook dans Databricks")

3. Dans la boîte de dialogue **Créer un bloc-notes**, entrez un nom pour le bloc-notes. Sélectionnez **Scala** comme langage, puis sélectionnez le cluster Spark que vous avez créé précédemment.

    ![Fournir des détails pour un notebook dans Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "fournir des détails pour un notebook dans Databricks")

4. Sélectionnez **Créer**.

5. Copiez et collez le bloc de code suivant dans la première cellule.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   ```

6. Dans ce bloc de code, remplacez les valeurs d’espace réservé `application-id`, `authentication-id` et `tenant-id` par les valeurs que vous avez collectées quand vous avez effectué les étapes décrites dans [Mettre de côté la configuration du compte de stockage](#config). Remplacez la valeur d’espace réservé `storage-account-name` par le nom de votre compte de stockage.

7. Appuyez sur les touches **Maj +Entrée** pour exécuter le code de ce bloc.

8. Vous pouvez maintenant charger l’exemple de fichier JSON en tant que trame de données dans Azure Databricks. Collez le code suivant dans une nouvelle cellule. Remplacez les espaces réservés indiqués entre crochets par vos valeurs.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Remplacez la valeur d’espace réservé `file-system-name` par le nom que vous avez donné à votre système de fichiers dans l’Explorateur Stockage.

   * Remplacez la valeur d’espace réservé `storage-account-name` par le nom de votre compte de stockage.

9. Appuyez sur les touches **Maj +Entrée** pour exécuter le code de ce bloc.

10. Exécutez le code suivant pour voir le contenu du dataframe :

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

## <a name="transform-data-in-azure-databricks"></a>Transformer des données dans Azure Databricks

Le fichier brut de l’exemple de données **small_radio_json.json** capture l’audience d’une station de radio et présente différentes colonnes. Dans cette section, vous transformez les données pour récupérer uniquement des colonnes spécifiques du jeu de données.

1. Tout d’abord, récupérez uniquement les colonnes **firstName**, **lastName**, **gender**, **location** et **level** du dataframe que vous avez créé.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
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

2. Vous pouvez transformer ces données pour renommer la colonne **level** en **subscription_type**.

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

## <a name="load-data-into-azure-sql-data-warehouse"></a>Chargement de données dans Azure SQL Data Warehouse

Dans cette section, vous chargez les données transformées dans Azure SQL Data Warehouse. Vous utilisez le connecteur Azure SQL Data Warehouse pour Azure Databricks pour charger directement un dataframe sous forme de table dans un entrepôt de données SQL.

Comme mentionné précédemment, le connecteur SQL Data Warehouse utilise le Stockage Blob Azure comme stockage temporaire pour charger des données entre Azure Databricks et Azure SQL Data Warehouse. Vous commencez donc par fournir la configuration pour vous connecter au compte de stockage. Vous devez déjà avoir créé le compte dans le cadre des prérequis de cet article.

1. Fournissez la configuration pour accéder au compte de Stockage Azure à partir d’Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val authenticationKey =  "<authentication-key>"
   ```

2. Spécifiez un dossier temporaire à utiliser lors du déplacement des données entre Azure Databricks et Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blob-container-name + "@" + blobStorage +"/tempDirs"
   ```

3. Exécutez l’extrait de code suivant pour stocker les clés d’accès du Stockage Blob Azure dans la configuration. Cette action vous dispense d’avoir à conserver la clé d’accès dans le notebook en texte brut.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, authenticationKey)
   ```

4. Indiquez les valeurs pour vous connecter à l’instance Azure SQL Data Warehouse. Vous devez avoir créé un entrepôt de données SQL dans le cadre des prérequis.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Exécutez l’extrait de code suivant pour charger le dataframe transformé, **renamedColumnsDF**, en tant que table dans un entrepôt de données SQL. Cet extrait de code crée une table appelée **SampleTable** dans la base de données SQL.

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

6. Connectez-vous à la base de données SQL et vérifiez que vous y voyez une base de données nommée **SampleTable**.

   ![Vérifier la présence de l’exemple de table](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "Vérifier la présence de l’exemple de table")

7. Exécutez une requête SELECT pour vérifier le contenu de la table. Celle-ci doit avoir les mêmes données que celles présentes dans le dataframe **renamedColumnsDf**.

    ![Vérifier le contenu de l’exemple de table](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "Vérifier le contenu de l’exemple de table")

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous avez terminé le tutoriel, vous pouvez terminer le cluster. Dans l’espace de travail Azure Databricks, sélectionnez **Clusters** à gauche. Pour terminer le cluster, sous **Actions**, pointez sur les points de suspension (...) et sélectionnez l’icône **Terminer**.

![Arrêter un cluster Databricks](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Arrêter un cluster Databricks")

Si vous ne terminez pas le cluster manuellement, il s’arrête automatiquement, à condition d’avoir coché la case **Arrêter après \_\_ minutes d’inactivité** lors de la création du cluster. Dans ce cas, le cluster s’arrête automatiquement s’il a été inactif pendant la période renseignée.

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant pour découvrir le streaming des données en temps réel dans Azure Databricks à l’aide d’Azure Event Hubs.

> [!div class="nextstepaction"]
>[Streaming des données dans Azure Databricks à l’aide d’Event Hubs](../../azure-databricks/databricks-stream-from-eventhubs.md)
