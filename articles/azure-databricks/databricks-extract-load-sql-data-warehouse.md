---
title: Tutoriel - Exécuter des opérations ETL à l’aide d’Azure Databricks
description: Dans ce tutoriel, découvrez comment extraire des données de Data Lake Storage Gen2 dans Azure Databricks, les transformer, puis les charger dans Azure SQL Data Warehouse.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: e0805a9827474e4f52a5a10e019f7dedd1ab45fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411045"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Tutoriel : Extraire, transformer et charger des données à l’aide d’Azure Databricks

Dans ce tutoriel, vous allez effectuer une opération ETL (extraction, transformation et chargement de données) à l’aide d’Azure Databricks. Vous extrayez des données d’Azure Data Lake Storage Gen2 dans Azure Databricks, exécutez des transformations sur les données dans Azure Databricks, puis chargez les données transformées dans Azure SQL Data Warehouse.

Les étapes décrites dans ce didacticiel utilisent le connecteur SQL Data Warehouse pour Azure Databricks, afin de transférer des données à Azure Databricks. Ce connecteur utilise ensuite le Stockage Blob Azure en tant que stockage temporaire pour les données transférées entre un cluster Azure Databricks et Azure SQL Data Warehouse.

L’illustration suivante montre le flux d’application :

![Azure Databricks avec Data Lake Store et SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks avec Data Lake Store et SQL Data Warehouse")

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Créez un service Azure Databricks.
> * Créer un cluster Spark dans Azure Databricks.
> * Créer un système de fichiers dans le compte Data Lake Storage Gen2.
> * Chargez des exemples de données dans le compte Azure Data Lake Storage Gen2.
> * Créer un principal de service.
> * Extraire les données du compte Azure Data Lake Storage Gen2.
> * Transformer des données dans Azure Databricks.
> * Charger des données dans Azure SQL Data Warehouse.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!Note]
> Ce didacticiel ne peut pas être suivi avec un **abonnement d’essai gratuit Azure**.
> Si vous avez un compte gratuit, accédez à votre profil et modifiez votre abonnement sur **Paiement à l’utilisation**. Pour plus d’informations, consultez la page [Compte Azure gratuit](https://azure.microsoft.com/free/). Ensuite, [supprimez la limite de dépense](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit), et [demandez une augmentation du quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pour les processeurs virtuels dans votre région. Lorsque vous créez votre espace de travail Azure Databricks, vous pouvez sélectionner le tarif **Version d’évaluation (Premium - 14 jours de DBU offerts)** pour donner à l’accès de l’espace de travail un accès gratuit aux DBU d’Azure Databricks pendant 14 jours.
     
## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer ce tutoriel, effectuez les tâches suivantes :

* Créez un entrepôt de données SQL Azure, créez une règle de pare-feu au niveau du serveur et connectez-vous au serveur en tant qu’administrateur. Consultez [Démarrage rapide : Créer et interroger un entrepôt de données SQL Azure dans le portail Azure](../sql-data-warehouse/create-data-warehouse-portal.md).

* Créez une clé principale pour l’entrepôt de données SQL Azure. Consultez [Créer une clé principale de base de données](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Créez un compte de stockage Blob Azure et un conteneur dans celui-ci. Récupérez également la clé d’accès au compte de stockage. Consultez [Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide du portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

* Créez un compte de stockage Azure Data Lake Storage Gen2. Consultez [Démarrage rapide : Créer un compte de stockage Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Créer un principal de service. Consultez [Procédure : Utilisez le portail pour créer une application Azure AD et un principal du service pouvant accéder aux ressources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Vous devrez faire certaines choses spécifiques pendant que vous suivrez les étapes décrites dans cet article.

   * Au cours des étapes décrites dans la section [Attribuer un rôle à l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) de l’article, veillez à affecter le rôle **Contributeur aux données Blob du stockage** au principal de service dans l’étendue du compte Data Lake Storage Gen2. Si vous attribuez le rôle à l’abonnement ou au groupe de ressources parent, des erreurs d’autorisation seront générées tant que ces attributions de rôles ne seront pas propagées au compte de stockage.

      Si vous préférez utiliser une liste de contrôle d’accès (ACL) pour associer le principal de service à un fichier ou répertoire spécifique, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

   * Au cours des étapes indiquées dans la section [Obtenir les valeurs de connexion](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) de l’article, collez les valeurs de l’ID de locataire, de l’ID d’application et du mot de passe dans un fichier texte. Vous en aurez besoin bientôt.

* Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Collecter les informations dont vous avez besoin

Vérifiez que vous remplissez les prérequis de ce tutoriel.

   Avant de commencer, vous devez disposer des informations suivantes :

   :heavy_check_mark:  Nom de la base de données, nom du serveur de base de données, nom d’utilisateur et mot de passe de votre entrepôt de données SQL Azure.

   :heavy_check_mark:  Clé d’accès associée à votre compte de stockage d’objets blob.

   :heavy_check_mark:  Nom de votre compte de stockage Data Lake Storage Gen2.

   :heavy_check_mark:  ID de locataire de votre abonnement.

   :heavy_check_mark:  ID de l’application que vous avez inscrite auprès d’Azure Active Directory (Azure AD).

   :heavy_check_mark:  Clé d’authentification pour l’application que vous avez inscrite auprès d’Azure AD.

## <a name="create-an-azure-databricks-service"></a>Créer un service Azure Databricks

Dans cette section, vous créez un service Azure Databricks en utilisant le portail Azure.

1. Dans le menu du Portail Azure, sélectionnez **Créer une ressource**.

    ![Créer une ressource sur le portail Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Sélectionnez ensuite **Analytics** > **Azure Databricks**.

    ![Créer Azure Databricks sur le portail Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. Sous **Service Azure Databricks**, renseignez les valeurs suivantes pour créer un service Databricks :

    |Propriété  |Description  |
    |---------|---------|
    |**Nom de l’espace de travail**     | Fournissez un nom pour votre espace de travail Databricks.        |
    |**Abonnement**     | Sélectionnez votre abonnement Azure dans la liste déroulante.        |
    |**Groupe de ressources**     | Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../azure-resource-manager/management/overview.md). |
    |**Lieu**     | Sélectionnez **USA Ouest 2**.  Pour les autres régions disponibles, consultez [Disponibilité des services Azure par région](https://azure.microsoft.com/regions/services/).      |
    |**Niveau tarifaire**     |  Sélectionnez **Standard**.     |

3. La création du compte prend quelques minutes. Pour superviser l’état de l’opération, regardez la barre de progression située en haut.

4. Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Créer un cluster Spark dans Azure Databricks

1. Dans le portail Azure, accédez au service Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. Vous êtes redirigé vers le portail Azure Databricks. Dans le portail, sélectionnez **Cluster**.

    ![Databricks sur Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks sur Azure")

3. Dans la page **Nouveau cluster**, renseignez les valeurs pour créer un cluster.

    ![Créer un cluster Databricks Spark sur Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Créer un cluster Databricks Spark sur Azure")

4. Renseignez les champs suivants et acceptez les valeurs par défaut pour les autres champs :

    * Entrez un nom pour le cluster.

    * Veillez à cocher la case **Arrêter après \_\_ minutes d’inactivité**. Si le cluster n’est pas utilisé, indiquez une durée (en minutes) pour arrêter le cluster.

    * Sélectionnez **Créer un cluster**. Une fois que le cluster est en cours d’exécution, vous pouvez y attacher des notebooks et exécuter des travaux Spark.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Créer un système de fichiers dans le compte Azure Data Lake Storage Gen2

Dans cette section, vous créez un bloc-notes dans l’espace de travail Azure Databricks, puis vous exécutez des extraits de code pour configurer le compte de stockage

1. Dans le [portail Azure](https://portal.azure.com), accédez au service Azure Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. À gauche, sélectionnez **Espace de travail**. Dans la liste déroulante **Espace de travail**, sélectionnez **Créer** > **Notebook**.

    ![Créer un notebook dans Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Créer un notebook dans Databricks")

3. Dans la boîte de dialogue **Créer un bloc-notes**, entrez un nom pour le bloc-notes. Sélectionnez **Scala** comme langage, puis sélectionnez le cluster Spark que vous avez créé précédemment.

    ![Fournir des détails pour un notebook dans Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Fournir des détails pour un notebook dans Databricks")

4. Sélectionnez **Create** (Créer).

5. Le bloc de code suivant définit les informations d’identification par défaut du principal de service pour tout compte ADLS Gen 2 sollicité dans la session Spark. Le deuxième bloc de code ajoute le nom du compte au paramètre afin de spécifier les informations d’identification pour un compte ADLS Gen 2 spécifique.  Copiez et collez l’un ou l’autre bloc de code dans la première cellule de votre bloc-notes Azure Databricks.

   **Configuration de session**

   ```scala
   val appID = "<appID>"
   val password = "<password>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   ```

   **Configuration du compte**

   ```scala
   val storageAccountName = "<storage-account-name>"
   val appID = "<app-id>"
   val password = "<password>"
   val fileSystemName = "<file-system-name>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type." + storageAccountName + ".dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type." + storageAccountName + ".dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id." + storageAccountName + ".dfs.core.windows.net", "" + appID + "")
   spark.conf.set("fs.azure.account.oauth2.client.secret." + storageAccountName + ".dfs.core.windows.net", "" + password + "")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint." + storageAccountName + ".dfs.core.windows.net", "https://login.microsoftonline.com/" + tenantID + "/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://" + fileSystemName  + "@" + storageAccountName + ".dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. Dans ce bloc de code, remplacez les valeurs d’espace réservé `<app-id>`, `<password>`, `<tenant-id>` et `<storage-account-name>` par celles que vous avez collectées au moment de la finalisation des prérequis de ce tutoriel. Remplacez la valeur d’espace réservé `<file-system-name>` par le nom de système de fichiers de votre choix.

   * `<app-id>` et `<password>` proviennent de l’application que vous avez inscrite auprès d’Active Directory dans le cadre de la création d’un principal de service.

   * `<tenant-id>` provient de votre abonnement.

   * `<storage-account-name>` est le nom de votre compte de stockage Azure Data Lake Storage Gen2.

7. Appuyez sur les touches **Maj +Entrée** pour exécuter le code de ce bloc.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Ingérer des exemples de données dans le compte Azure Data Lake Storage Gen2

Avant de commencer cette section, vous devez effectuer les prérequis suivants :

Entrez le code suivant dans une cellule du bloc-notes :

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Dans la cellule, appuyez sur **Maj+Entrée** pour exécuter le code.

À présent, dans une nouvelle cellule en dessous de celle-ci, entrez le code suivant et remplacez les valeurs entre crochets par les valeurs que vous avez utilisées plus tôt :

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

Dans la cellule, appuyez sur **Maj+Entrée** pour exécuter le code.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Extraire les données du compte Azure Data Lake Storage Gen2

1. Vous pouvez maintenant charger l’exemple de fichier JSON en tant que trame de données dans Azure Databricks. Collez le code suivant dans une nouvelle cellule. Remplacez les espaces réservés indiqués entre crochets par vos valeurs.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```
2. Appuyez sur les touches **Maj +Entrée** pour exécuter le code de ce bloc.

3. Exécutez le code suivant pour voir le contenu du dataframe :

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

Comme mentionné précédemment, le connecteur SQL Data Warehouse utilise le Stockage Blob Azure comme stockage temporaire pour charger des données entre Azure Databricks et Azure SQL Data Warehouse. Vous commencez donc par fournir la configuration pour vous connecter au compte de stockage. Vous devez déjà avoir créé le compte pour les prérequis de cet article.

1. Fournissez la configuration pour accéder au compte de Stockage Azure à partir d’Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Spécifiez un dossier temporaire à utiliser lors du déplacement des données entre Azure Databricks et Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Exécutez l’extrait de code suivant pour stocker les clés d’accès du Stockage Blob Azure dans la configuration. Cette action vous dispense d’avoir à conserver la clé d’accès dans le notebook en texte brut.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Indiquez les valeurs pour vous connecter à l’instance Azure SQL Data Warehouse. Vous devez avoir créé un entrepôt de données SQL dans le cadre des prérequis. Utilisez le nom complet du serveur pour **dwServer**. Par exemple : `<servername>.database.windows.net`.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Exécutez l’extrait de code suivant pour charger le dataframe transformé, **renamedColumnsDF**, en tant que table dans un entrepôt de données SQL. Cet extrait de code crée une table appelée **SampleTable** dans la base de données SQL.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > Cet exemple utilise l’indicateur `forward_spark_azure_storage_credentials`, qui fait en sorte que l’entrepôt de données SQL accède aux données à partir du stockage d’objets blob à l’aide d’une clé d’accès. Il s’agit de la seule méthode d’authentification prise en charge.
   >
   > Si votre Stockage Blob Azure est limité à certains réseaux virtuels, SQL Data Warehouse nécessite [Managed Service Identity au lieu de clés d’accès](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Cela génère l’erreur « Cette requête n’est pas autorisée à effectuer cette opération ».

6. Connectez-vous à la base de données SQL et vérifiez que vous y voyez une base de données nommée **SampleTable**.

   ![Vérifier l’exemple de table](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Vérifier l’exemple de table")

7. Exécutez une requête SELECT pour vérifier le contenu de la table. Celle-ci doit avoir les mêmes données que celles présentes dans le dataframe **renamedColumnsDf**.

    ![Vérifier le contenu de l’exemple de table](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Vérifier le contenu de l’exemple de table")

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous avez terminé le tutoriel, vous pouvez terminer le cluster. Dans l’espace de travail Azure Databricks, sélectionnez **Clusters** à gauche. Pour terminer le cluster, sous **Actions**, pointez sur les points de suspension (...) et sélectionnez l’icône **Terminer**.

![Arrêter un cluster Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Arrêter un cluster Databricks")

Si vous ne terminez pas le cluster manuellement, il s’arrête automatiquement, à condition d’avoir coché la case **Arrêter après \_\_ minutes d’inactivité** lors de la création du cluster. Dans ce cas, le cluster s’arrête automatiquement s’il a été inactif pendant la période renseignée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un service Azure Databricks
> * Créer un cluster Spark dans Azure Databricks
> * Créer un notebook dans Azure Databricks
> * Extraire les données d’un compte Data Lake Storage Gen2
> * Transformer des données dans Azure Databricks
> * Chargement de données dans Azure SQL Data Warehouse

Passez au tutoriel suivant pour en savoir plus sur la diffusion en continu des données en temps réel dans Azure Databricks à l’aide d’Azure Event Hubs.

> [!div class="nextstepaction"]
>[Diffuser en continu des données dans Azure Databricks à l’aide d’Event Hubs](databricks-stream-from-eventhubs.md)
