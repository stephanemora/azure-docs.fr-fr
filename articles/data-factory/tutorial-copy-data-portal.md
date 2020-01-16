---
title: Utiliser le portail Azure pour créer un pipeline de fabrique de données
description: Ce didacticiel fournit des instructions détaillées sur l’utilisation d’un portail Azure pour créer une fabrique de données avec un pipeline. Le pipeline utilise l’activité de copie pour copier des données d’un stockage Blob Azure vers une base de données SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 135a18f275137e72b5ff4d79f6a32bd39bd9c00c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977402"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Copier des données à partir d’un stockage Blob Azure vers une base de données SQL en utilisant Azure Data Factory
Dans ce didacticiel, vous créez une fabrique de données à l’aide de l’interface utilisateur (IU) d’Azure Data Factory. Le pipeline de cette fabrique de données copie les données d’un stockage Blob Azure vers une base de données SQL. Le modèle de configuration de ce didacticiel s’applique à la copie depuis un magasin de données de fichiers vers un magasin de données relationnelles. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Si vous débutez avec Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Dans ce tutoriel, vous effectuerez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un pipeline avec une activité de copie.
> * Effectuer une série de tests sur le pipeline.
> * Déclencher le pipeline manuellement.
> * Déclencher le pipeline en fonction d’une planification.
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>Conditions préalables requises
* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Azure Storage**. Vous utilisez le stockage Blob comme magasin de données *source*. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte de stockage Azure](../storage/common/storage-account-create.md) pour découvrir comment en créer un.
* **Azure SQL Database**. Vous utilisez la base de données comme magasin de données *récepteur*. Si vous ne disposez pas d’une base de données SQL, consultez [Créer une base de données SQL](../sql-database/sql-database-get-started-portal.md) pour découvrir comment en créer une.

### <a name="create-a-blob-and-a-sql-table"></a>Créer un objet blob et une table SQL

À présent, préparez votre stockage Blob et votre base de données SQL pour ce didacticiel, en procédant comme suit.

#### <a name="create-a-source-blob"></a>Créer un objet blob source

1. Lancez le Bloc-notes. Copiez le texte suivant et enregistrez-le comme fichier **emp.txt** sur votre disque :

    ```
    John,Doe
    Jane,Doe
    ```

1. Créez un conteneur nommé **adftutorial** dans votre stockage Blob. Créez un dossier nommé **input** dans ce conteneur. Ensuite, chargez le fichier **emp.txt** dans le dossier **input**. Utilisez le portail Azure ou des outils tels que l’[Explorateur Stockage Azure](https://storageexplorer.com/) pour effectuer ces tâches.

#### <a name="create-a-sink-sql-table"></a>Créer une table SQL de récepteur

1. Utilisez le script SQL suivant pour créer la table **dbo.emp** dans votre base de données SQL :

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Autorisez les services Azure à accéder au serveur SQL. Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est **ACTIVÉ** pour votre serveur SQL afin que Data Factory puisse écrire des données sur votre serveur SQL. Pour vérifier et activer ce paramètre, accédez à votre serveur SQL Azure > Vue d’ensemble > Définir le pare-feu serveur > définissez l’option **Autoriser l’accès aux services Azure** sur **Activé**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données
À cette étape, vous allez créer une fabrique de données et démarrer l’interface utilisateur de Data Factory afin de créer un pipeline dans la fabrique de données.

1. Ouvrez **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Analytics** > **Data Factory** :

   ![Sélection Data Factory dans le volet « Nouveau »](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.

   Le nom de la fabrique de données Azure doit être un nom *global unique*. Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. (par exemple, yournameADFTutorialDataFactory). Consultez l’article [Azure Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les règles Data Factory.

     ![Nouvelle fabrique de données](./media/doc-common-process/name-not-available-error.png)
4. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données.
5. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :

    a. Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    b. Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md). 
6. Sous **Version**, sélectionnez **V2**.
7. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (comme Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.
8. Sélectionnez **Create** (Créer).
9. Une fois la création terminée, vous voyez apparaître l’avis dans le centre de notifications. Sélectionnez **Accéder à la ressource** pour accéder à la page de la fabrique de données.
10. Sélectionnez **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé.


## <a name="create-a-pipeline"></a>Créer un pipeline
À cette étape, vous créez un pipeline avec une activité de copie dans la fabrique de données. L’activité de copie permet de copier les données d’un stockage Blob vers une base de données SQL. Dans le [didacticiel de démarrage rapide](quickstart-create-data-factory-portal.md), vous avez créé un pipeline en procédant comme suit :

1. Créer le service lié.
1. Créer des jeux de données d’entrée et de sortie.
1. Créer un pipeline.

Dans ce didacticiel, vous commencez par créer le pipeline. Puis vous créez des jeux de données et des services liés lorsque vous en avez besoin pour configurer le pipeline.

1. Dans la page **Prise en main**, cliquez sur **Créer un pipeline**.

   ![Création d’un pipeline](./media/doc-common-process/get-started-page.png)
1. Dans l’onglet **Général** du pipeline, entrez **CopyPipeline** pour le **Nom** du pipeline.

1. Dans la boîte à outils **Activités**, développez la catégorie **Déplacer et transformer**, puis faites glisser l’activité **Copier les données** de la boîte à outils vers l’aire du concepteur de pipeline. Spécifiez **CopyFromBlobToSql** pour le **Nom**.

    ![Activité de copie](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurer la source

1. Accédez à l’onglet **Source**. Sélectionnez **+ Nouveau** pour créer un jeu de données source.

1. Dans la boîte de dialogue **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis **Continuer**. Sachant que les données sources se trouvent dans un stockage Blob, vous devez sélectionner le **Stockage Blob Azure** pour le jeu de données source.

1. Dans la boîte de dialogue **Sélectionner le format**, choisissez le type de format de vos données, puis sélectionnez **Continuer**.

    ![Type de format de données](./media/doc-common-process/select-data-format.png)

1. Dans la boîte de dialogue **Définir les propriétés**, entrez **SourceBlobDataset** comme nom. En regard de la zone de texte **Service lié**, sélectionnez **+ Nouveau**.

1. Dans la boîte de dialogue **Nouveau service lié (Stockage Blob Azure)** , entrez **AzureStorageLinkedService** pour le nom, puis sélectionnez votre compte de stockage dans la liste **Nom du compte de stockage**. Testez la connexion, puis sélectionnez **Terminer** pour déployer le service lié.

1. Après la création du service lié, la page **Définir les propriétés** s’affiche de nouveau. En regard de **Chemin d’accès du fichier**, sélectionnez **Parcourir**.

1. Accédez au dossier **adftutorial/input**, sélectionnez le fichier **emp.txt**, puis sélectionnez **Terminer**.

1. La page du pipeline s’affiche automatiquement. Sous l’onglet **Source**, vérifiez que **SourceBlobDataset** est sélectionné. Pour afficher un aperçu des données dans cette page, sélectionnez **Aperçu des données**.

    ![Jeu de données source](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Configurer le récepteur

1. Accédez à l’onglet **Récepteur**, puis sélectionnez **+ Nouveau** pour créer un jeu de données récepteur.

1. Dans la boîte de dialogue **Nouveau jeu de données**, entrez « SQL » dans la zone de recherche pour filtrer les connecteurs, sélectionnez **Azure SQL Database**, puis **Continuer**. Dans ce didacticiel, vous copiez des données vers une base de données SQL.

1. Dans la boîte de dialogue **Définir les propriétés**, entrez **OutputSqlDataset** comme nom. En regard de la zone de texte **Service lié**, sélectionnez **+ Nouveau**. Un jeu de données doit être associé à un service lié. Le service lié comporte la chaîne de connexion utilisée par Data Factory pour établir la connexion à la base de données SQL lors de l’exécution. Le jeu de données spécifie le conteneur, le dossier et le fichier (facultatif) dans lequel les données sont copiées.

1. Dans la boîte de dialogue **Nouveau service lié (Azure SQL Database)** , effectuez les étapes suivantes :

    a. Sous **Nom**, entrez **AzureSqlDatabaseLinkedService**.

    b. Sous **Nom du serveur**, sélectionnez votre instance SQL Server.

    c. Sous **Nom de la base de données**, sélectionnez votre base de données SQL.

    d. Sous **Nom d’utilisateur**, entrez le nom de l’utilisateur.

    e. Sous **Mot de passe**, entrez le mot de passe de l’utilisateur.

    f. Sélectionnez **Tester la connexion** pour tester la connexion.

    g. Sélectionnez **Terminer** pour déployer le service lié.

    ![Enregistrer le nouveau service lié](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. La boîte de dialogue **Définir les propriétés** s’affiche automatiquement. Sélectionnez **[dbo].[emp]** dans le champ **Table**. Sélectionnez **Terminer**.

1. Accédez à l’onglet avec le pipeline, puis dans **Jeu de données récepteur**, vérifiez que l’option **OutputSqlDataset** est sélectionnée.

    ![Onglet Pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Vous pouvez éventuellement mapper le schéma de la source au schéma correspondant de la destination en vous reportant à [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

## <a name="validate-the-pipeline"></a>Valider le pipeline
Sélectionnez **Valider** dans la barre d’outils pour valider les paramètres du pipeline.

Vous pouvez voir le code JSON associé au pipeline en cliquant sur **Code** dans le coin supérieur droit.

## <a name="debug-and-publish-the-pipeline"></a>Déboguer et publier le pipeline
Vous pouvez déboguer un pipeline avant de publier des artefacts (services liés, jeux de données et pipeline) dans Data Factory ou votre propre référentiel Azure Repos Git.

1. Pour déboguer le pipeline, sélectionnez **Déboguer** dans la barre d’outils. L’état d’exécution du pipeline apparaît dans l’onglet **Sortie** au bas de la fenêtre.

1. Une fois que le pipeline peut s’exécuter correctement, sélectionnez **Publier tout** dans la barre d’outils supérieure. Cette action publie les entités (jeux de données et pipelines) que vous avez créées dans Data Factory.

1. Patientez jusqu’à voir le message **Publication réussie**. Pour afficher les messages de notification, cliquez sur **Afficher les notifications** en haut à droite (bouton en forme de cloche).

## <a name="trigger-the-pipeline-manually"></a>Déclencher le pipeline manuellement
Dans cette étape, vous déclenchez manuellement le pipeline que vous avez publié dans l’étape précédente.

1. Sélectionnez **Ajouter déclencheur** dans la barre d’outils, puis **Déclencher maintenant**. Dans la page **Exécution du pipeline**, sélectionnez **Terminer**.  

1. Accédez à l’onglet **Surveiller** sur la gauche. Vous voyez un pipeline qui est déclenché par un déclencheur manuel. Vous pouvez utiliser les liens dans la colonne **Actions** pour afficher les détails de l’activité et réexécuter le pipeline.

    ![Surveiller des exécutions de pipelines](./media/tutorial-copy-data-portal/monitor-pipeline.png)

1. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Dans cet exemple, il n’y a qu’une seule activité, vous ne voyez donc qu’une seule entrée dans la liste. Pour plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Sélectionnez **Exécutions de pipeline** au sommet de la page pour revenir à la vue des exécutions du pipeline. Sélectionnez **Actualiser** pour actualiser l’affichage.

    ![Surveiller des exécutions d’activités](./media/tutorial-copy-data-portal/view-activity-runs.png)

1. Vérifiez que deux lignes supplémentaires sont ajoutées à la table **emp** dans la base de données SQL.

## <a name="trigger-the-pipeline-on-a-schedule"></a>Déclencher le pipeline selon une planification
Dans cette planification, vous créez un déclencheur de planificateur pour le pipeline. Le déclencheur exécute le pipeline selon la planification spécifiée, par exemple toutes les heures ou tous les jours. Ici, vous définissez le déclencheur pour s’exécuter toutes les minutes jusqu’à la date/heure de fin spécifiée.

1. Accédez à l’onglet **Auteur** sur la gauche au-dessus de l’onglet Surveiller.

1. Accédez à votre pipeline, cliquez sur **Ajouter un déclencheur** dans la barre d’outils, puis sélectionnez **Nouveau/Modifier**.

1. Dans la boîte de dialogue **Ajouter des déclencheurs**, sélectionnez **+ Nouveau** dans la zone **Choisir un déclencheur**.

1. Dans la fenêtre **Nouveau déclencheur**, procédez comme suit :

    a. Sous **Nom**, entrez **RunEveryMinute**.

    b. Sous **Fin**, sélectionnez **On Date**.

    c. Sous **Fin à**, sélectionnez la liste déroulante.

    d. Sélectionnez le **jour actuel**. Par défaut, le jour de fin est défini sur le jour suivant.

    e. Mettez à jour la partie **Heure de fin** en choisissant quelques minutes après la date/heure actuelle. Le déclencheur n’est activé qu’après avoir publié les modifications. Si vous le définissez à quelques minutes d’intervalle mais ne publiez pas, vous ne voyez pas d’exécution du déclencheur.

    f. Sélectionnez **Appliquer**.

    g. Pour l’option **Activé**, sélectionnez **Oui**.

    h. Sélectionnez **Suivant**.

    ![Bouton Activé](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Chaque exécution de pipeline coûte de l’argent. Il est donc important de définir correctement la date de fin.
1. Dans la page **Paramètres d’exécution du déclencheur**, lisez l’avertissement, puis sélectionnez **Terminer**. Le pipeline de cet exemple n’accepte pas de paramètres.

1. Cliquez sur **Publier tout** pour publier la modification.

1. Accédez à l’onglet **Surveiller** sur la gauche pour voir les exécutions du pipeline déclenchées.

    ![Exécutions du pipeline déclenchées](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)   

1. Pour basculer de la vue des **exécutions du pipeline** vers la vue des **exécutions du déclencheur**, sélectionnez **Exécutions de déclencheur** en haut de la fenêtre.

1. Vous voyez les exécutions du déclencheur dans une liste.

1. Vérifiez que deux lignes par minute (pour chaque exécution du pipeline) sont insérées dans la table **emp** jusqu’à l’heure de fin spécifiée.

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob. Vous avez appris à :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un pipeline avec une activité de copie.
> * Effectuer une série de tests sur le pipeline.
> * Déclencher le pipeline manuellement.
> * Déclencher le pipeline en fonction d’une planification.
> * Surveiller les exécutions de pipeline et d’activité.


Passez au didacticiel suivant pour en savoir plus sur la copie des données locales vers le cloud :

> [!div class="nextstepaction"]
>[Copier des données locales vers le cloud](tutorial-hybrid-copy-portal.md)
