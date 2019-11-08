---
title: Synchroniser des données à partir d’Azure SQL Database Edge avec Azure Data Factory | Microsoft Docs
description: Découvrez comment synchroniser des données entre Azure SQL Database Edge et le stockage Blob Azure
keywords: sql database edge, synchroniser des données à partir de sql database edge, sql database edge data factory
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509203"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>Didacticiel : Synchroniser des données entre SQL Database Edge et le stockage Blob Azure avec Azure Data Factory

Dans ce tutoriel, vous allez utiliser Azure Data Factory pour synchroniser des données de façon incrémentielle entre une table appartenant à une instance d’Azure SQL Database Edge et le stockage Blob Azure.

## <a name="before-you-begin"></a>Avant de commencer

Si vous n’avez pas encore créé une base de données ou une table dans votre déploiement Azure SQL Database Edge, utilisez l’une des méthodes suivantes pour en créer une :

* Utilisez [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) ou [Azure Data Studio](/sql/azure-data-studio/download/) pour vous connecter à SQL Database Edge et exécuter un script SQL en vue de créer la base de données et la table.
* Créez une base de données et une table SQL à l’aide de [SQLCMD](/sql/tools/sqlcmd-utility/) en vous connectant directement au module SQL Database Edge. Pour plus d’informations, consultez [Se connecter au moteur de base de données avec sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Utilisez SQLPackage.exe pour déployer un fichier dacpac dans le conteneur SQL Database Edge. Vous pouvez automatiser ce processus en spécifiant l’URI du fichier SQLPackage dans le cadre de la configuration des propriétés souhaitées des modules, ou en utilisant directement l’outil client SqlPackage.exe pour déployer un package DAC dans SQL Database Edge.

    Pour télécharger SqlPackage, consultez [Télécharger et installer SqlPackage](/sql/tools/sqlpackage-download/). Les exemples de commandes suivants sont fournis pour SqlPackage.exe. Pour plus d’informations, consultez la documentation sur SqlPackage.

    **Créer un package DAC** :

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **Appliquer un package DAC** :

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Créer une table et une procédure SQL pour stocker et mettre à jour les niveaux de filigrane

La table de filigranes est utilisée pour stocker la date et l’heure auxquelles les données ont été synchronisées pour la dernière fois avec le stockage Azure. La procédure stockée Transact-SQL (T-SQL) est utilisée pour mettre à jour la table de filigranes après chaque synchronisation. 

Exécutez les commandes suivantes sur l’instance SQL Database Edge :

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-workflow"></a>Créer un workflow Data Factory

Dans cette section, vous allez créer un pipeline Azure Data Factory pour synchroniser des données entre une table Azure SQL Database Edge et le stockage Blob Azure.

### <a name="create-data-factory-using-the-data-factory-ui"></a>Créer une fabrique de données à l’aide de l’interface utilisateur Data Factory

Créez une fabrique de données en suivant les instructions de ce [tutoriel](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Créer un pipeline Data Factory

1. Dans la page **Prise en main** de l’interface utilisateur Data Factory, sélectionnez la vignette **Créer un pipeline**.

    ![Data Factory - Créer un pipeline](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Dans la page **Général** de la fenêtre **Propriétés** du pipeline, entrez le nom **PeriodicSync**.

3. Ajoutez l’activité **Recherche** pour obtenir l’ancienne valeur de filigrane. Dans la **boîte à outils Activités**, développez **Général**, puis glissez-déposez l’activité **Recherche** sur l’aire du concepteur de pipeline. Remplacez le nom de l’activité par *OldWatermark*.

    ![Recherche de l’ancien filigrane](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Basculez vers l’onglet **Paramètres**, puis sélectionnez **+ Nouveau** comme **jeu de données source**. Dans cette étape, vous allez créer un jeu de données pour représenter les données de la table de filigranes. Cette table contient l’ancien filigrane utilisé dans l’opération de copie précédente.

5. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Azure SQL Server**, puis **Continuer**.  

6. Dans la fenêtre **Définir des propriétés** du jeu de données, entrez *WatermarkDataset* pour le nom.

7. Pour **Service lié**, sélectionnez **Nouveau**, puis effectuez les étapes suivantes :

    1. Entrez *SQLDBEdgeLinkedService* pour le **nom**.

    2. Entrez les détails de votre serveur SQL Database Edge dans **Nom du serveur**.

    3. Entrez le **nom de la base de données** qui figure dans la liste déroulante.

    4. Entrez votre **nom d’utilisateur** et votre **mot de passe**.

    5. Pour tester la connexion à l’instance SQL Database Edge, sélectionnez **Tester la connexion**.

    6. Sélectionnez **Create** (Créer).

    ![Création d’un service lié](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Sélectionnez **OK**.

8. Dans l’onglet **Paramètres**, sélectionnez **Paramètres**.

9. Sous l’onglet **Connexion**, sélectionnez *[dbo].[watermarktable]* pour **Table**. Si vous souhaitez voir un aperçu des données de la table, sélectionnez **Aperçu des données**.

10. Basculez vers l’éditeur de pipeline en sélectionnant l’onglet Pipeline en haut ou en sélectionnant le nom du pipeline dans l’arborescence à gauche. Dans la fenêtre Propriétés de l’activité **Recherche**, vérifiez que **WatermarkDataset** est sélectionné dans le champ **Jeu de données source**.

11. Dans la boîte à outils **Activités**, développez **Général**, glissez-déposez une autre activité **Recherche** sur l’aire du concepteur de pipeline, puis définissez le nom sur **NewWatermark** dans l’onglet **Général** de la fenêtre Propriétés. Cette activité de recherche obtient la nouvelle valeur de filigrane à partir de la table avec les données sources à copier vers la destination.

12. Dans la fenêtre Propriétés de la deuxième activité **Recherche**, basculez vers l’onglet **Paramètres**, puis sélectionnez **Nouveau** pour créer un jeu de données qui pointe vers la table source contenant la nouvelle valeur de filigrane.

13. Dans la fenêtre **Nouveau jeu de données**, sélectionnez l’instance SQL Database Edge, puis sélectionnez **Continuer**.

    1. Dans la fenêtre **Définir des propriétés**, entrez **SourceDataset** comme **nom**. Sélectionnez *SQLDBEdgeLinkedService* comme service lié.

    2. Sélectionnez la ***table que vous souhaitez synchroniser*** dans la section Table. Vous pouvez également spécifier une requête pour ce jeu de données, comme indiqué plus loin dans le tutoriel. La requête a la priorité sur la table spécifiée à cette étape.

    3. Sélectionnez **OK**.

14. Basculez vers l’éditeur de pipeline en sélectionnant l’onglet Pipeline en haut ou en sélectionnant le nom du pipeline dans l’arborescence à gauche. Dans la fenêtre Propriétés pour l’activité de **recherche**, vérifiez que **SourceDataset** est sélectionné dans le champ **Jeu de données source**.

15. Sélectionnez **Requête** pour le champ **Utiliser une requête**, puis entrez la requête suivante après avoir mis à jour le nom de la table dans la requête. Vous sélectionnez uniquement la valeur maximale d’horodatage dans la table. Vérifiez que vous avez également coché **Première ligne uniquement**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![sélectionner la requête](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Dans la boîte à outils **Activités**, développez **Déplacer et transformer**, glissez-déposez l’activité **Copie** à partir de la boîte à outils Activités, puis définissez le nom sur **IncrementalCopy**.

17. Connectez les deux activités **Recherche** à l’activité **Copie** en faisant glisser le **bouton vert** attaché aux activités **Recherche** et **Copie**. Relâchez le bouton de la souris lorsque la couleur de bordure de l’activité Copie passe au bleu.

18. Sélectionnez l’activité **Copie** et vérifiez que les propriétés de l’activité figurent bien dans la fenêtre **Propriétés**.

19. Basculez vers l’onglet **Source** dans la fenêtre**Propriétés**, et procédez comme suit :

    1. Sélectionnez **SourceDataset** pour le champ **Jeu de données source**.

    2. Sélectionnez **Requête** pour le champ **Utiliser la requête**.

    3. Entrez la requête SQL dans le champ **Requête**. Exemple de requête ci-dessous.

    4. Requête SQL :

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Basculez vers l’onglet **Récepteur**, puis sélectionnez **+ Nouveau** pour le champ **Jeu de données récepteur**.

21. Dans ce tutoriel, le magasin de données récepteur est de type **Stockage Blob Azure**. Sélectionnez **Stockage Blob Azure**, puis sélectionnez **Continuer** dans la fenêtre **Nouveau jeu de données**.

22. Dans la fenêtre **Sélectionner le format**, sélectionnez le type de format de vos données, puis sélectionnez **Continuer**.

23. Dans la fenêtre **Définir des propriétés**, entrez **SinkDataset** pour le nom. Pour Service lié, sélectionnez **+ Nouveau**. Dans cette étape, vous créez une connexion (service lié) à votre **stockage Blob Azure**.

24. Dans la fenêtre **Nouveau service lié (Stockage Blob Azure)** , effectuez les étapes suivantes :

    1. Entrez *AzureStorageLinkedService* pour le nom.

    2. Sélectionnez votre compte de stockage Azure pour **Nom du compte de stockage** avec votre abonnement Azure.

    3. Testez la **connexion**, puis sélectionnez **Terminer**.

25. Dans la fenêtre **Définir des propriétés**, vérifiez que *AzureStorageLinkedService* est sélectionné comme **service lié**. Ensuite, sélectionnez **Créer** et **OK**.

26. Sous l’onglet **Récepteur**, sélectionnez **Modifier**.

27. Accédez à l’onglet **Connexion** de *SinkDataset*, puis effectuez les étapes suivantes :

    1. Dans le champ **Chemin du fichier**, entrez *asdedatasync/incrementalcopy*, où **asdedatasync** correspond au nom du conteneur d’objets blob et **incrementalcopy** correspond au nom du dossier. Créez le conteneur s’il n’existe pas ou attribuez-lui le nom d’un conteneur existant. Azure Data Factory crée automatiquement le dossier de sortie *incrementalcopy* s’il n’existe pas. Vous pouvez également utiliser le bouton **Parcourir** pour le **chemin d’accès du fichier** afin d’accéder à un dossier dans un conteneur d’objets blob.

    2. Pour la partie **Fichier** du champ **Chemin du fichier**, sélectionnez **Ajouter du contenu dynamique [Alt+P]** , puis *entrez @CONCAT('Incremental-', pipeline().RunId, '.txt')* dans la fenêtre ouverte. Sélectionnez ensuite **Terminer**. Le nom de fichier est généré dynamiquement à l’aide de l’expression. Chaque exécution de pipeline possède un ID unique. L’activité de copie utilise l’ID d’exécution pour générer le nom de fichier.

28. Basculez vers l’éditeur de **pipeline** en sélectionnant l’onglet Pipeline en haut ou en sélectionnant le nom du pipeline dans l’arborescence à gauche.

29. Dans la boîte à outils **Activités**, développez **Général**, et faites glisser et déposez l’activité **Procédure stockée** de la boîte à outils **Activités** sur la surface du concepteur de pipeline. **Connectez** le résultat vert (succès) de l’activité de **copie** à l’activité **Procédure stockée**.

30. Sélectionnez **Activité de procédure stockée** dans le concepteur de pipeline, puis remplacez son nom par *SPtoUpdateWatermarkActivity*.

31. Passez à l’onglet **Compte SQL** et sélectionnez *SQLDBEdgeLinkedService* comme **service lié**.

32. Basculez vers l’onglet **Procédure stockée**, et procédez comme suit :

    1. Pour **Nom de la procédure stockée**, sélectionnez *[dbo].[usp_write_watermark]* .

    2. Pour spécifier des valeurs correspondant aux paramètres de procédure stockée, sélectionnez Import parameter (Paramètre d’importation), puis entrez les valeurs suivantes pour les paramètres :

    |Nom|type|Valeur|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|Chaîne|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Pour valider les paramètres du pipeline, sélectionnez **Valider** dans la barre d’outils. Vérifiez qu’il n’y a aucune erreur de validation. Pour fermer la fenêtre **Rapport de validation de pipeline**, sélectionnez **>>** .

34. Publiez des entités (services liés, jeux de données et pipelines) pour le service Azure Data Factory en sélectionnant le bouton **Publier tout**. Patientez jusqu’à voir le message de réussite de la publication.

## <a name="trigger-a-pipeline-on-schedule"></a>Déclencher un pipeline selon une planification

1. Dans la barre d’outils du pipeline, sélectionnez **Ajouter un déclencheur**, puis sélectionnez **Nouveau/Modifier** et **+ Nouveau**.

2. Nommez votre déclencheur *HourlySync*, choisissez **Type** comme planification, puis définissez une **récurrence** toutes les heures.

3. Sélectionnez **OK**.

4. Sélectionnez **Publier tout**.

5. Sélectionnez **Déclencher maintenant**.

6. Basculez vers l’onglet **Surveiller** sur la gauche. Vous pouvez voir l’état de l’exécution du pipeline déclenchée par le déclencheur manuel. Sélectionnez le bouton **Actualiser** pour actualiser la liste.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, le pipeline Azure Data Factory copie les données d’une table de l’instance SQL Database Edge dans un emplacement du stockage Blob Azure toutes les heures. Pour plus d’informations sur l’utilisation de Data Factory dans d’autres scénarios, consultez ces [tutoriels](../data-factory/tutorial-copy-data-portal.md).
