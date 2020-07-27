---
title: Utiliser des points de terminaison privés pour créer un pipeline Azure Data Factory
description: Ce didacticiel fournit des instructions détaillées sur l’utilisation d’un portail Azure pour créer une fabrique de données avec un pipeline. Le pipeline utilise l’activité de copie pour copier des données du Stockage Blob Azure vers une base de données Azure SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: 9458c89922fab8450b7cb8e202491d4c47e250e8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540478"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Copier des données du stockage Blob Azure vers une base de données SQL de manière sécurisée en utilisant des points de terminaison privés

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dans ce didacticiel, vous créez une fabrique de données à l’aide de l’interface utilisateur (IU) d’Azure Data Factory. **Le pipeline de cette fabrique de données copie les données du stockage Blob Azure vers une base de données Azure SQL de manière sécurisée (les deux autorisant l’accès aux réseaux sélectionnés uniquement) en utilisant des points de terminaison privés dans un [réseau virtuel managé Azure Data Factory](managed-virtual-network-private-endpoint.md).** Le modèle de configuration de ce didacticiel s’applique à la copie depuis un magasin de données de fichiers vers un magasin de données relationnelles. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs, consultez le tableau [Magasins de données pris en charge](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).

> [!NOTE]
>
> - Si vous débutez avec Data Factory, consultez [Présentation d’Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

Dans ce tutoriel, vous effectuerez les étapes suivantes :

> * Créer une fabrique de données
> * Créer un pipeline avec une activité de copie


## <a name="prerequisites"></a>Prérequis
* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Azure Storage**. Vous utilisez le stockage Blob comme magasin de données *source*. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) pour découvrir comment en créer un. **Assurez-vous que le compte de stockage autorise l’accès à partir des « réseaux sélectionnés » uniquement.** 
* **Azure SQL Database**. Vous utilisez la base de données comme magasin de données *récepteur*. Si vous ne disposez pas d’une base de données Azure SQL, consultez [Créer une base de données SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) pour découvrir comment en créer une. **Assurez-vous que le compte Azure SQL Database autorise l’accès à partir des « réseaux sélectionnés » uniquement.** 

### <a name="create-a-blob-and-a-sql-table"></a>Créer un objet blob et une table SQL

À présent, préparez votre stockage Blob et votre base de données SQL pour ce didacticiel, en procédant comme suit.

#### <a name="create-a-source-blob"></a>Créer un objet blob source

1. Lancez le Bloc-notes. Copiez le texte suivant et enregistrez-le comme fichier **emp.txt** sur votre disque :

    ```
    FirstName,LastName
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


2. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Analytics** > **Data Factory**.

3. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.

   Le nom de la fabrique de données Azure doit être un nom *global unique*. Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. (par exemple, yournameADFTutorialDataFactory). Consultez l’article [Azure Data Factory - Règles d’affectation des noms](https://docs.microsoft.com/azure/data-factory/naming-rules) pour savoir comment nommer les règles Data Factory.

4. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données.

5. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :

    a. Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    b. Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview). 

6. Sous **Version**, sélectionnez **V2**.

7. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (comme Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.


8. Sélectionnez **Create** (Créer).


9. Une fois la création terminée, vous voyez apparaître l’avis dans le centre de notifications. Sélectionnez **Accéder à la ressource** pour accéder à la page de la fabrique de données.

10. Sélectionnez **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Créer un runtime d’intégration Azure dans un réseau virtuel managé ADF
Dans le cadre de cette étape, vous allez créer un runtime d’intégration Azure et activer un réseau virtuel managé.

1. Dans le portail ADF, sélectionnez **Gérer**, puis cliquez sur **Nouveau** pour créer un runtime d’intégration Azure.
   ![Créer un runtime d’intégration Azure](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Choisissez de créer un runtime d’intégration Azure**.
   ![Nouveau runtime d’intégration Azure](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Activez l’option **Configuration du réseau virtuel**.
   ![Nouveau runtime d’intégration Azure](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Sélectionnez **Create** (Créer).

## <a name="create-a-pipeline"></a>Créer un pipeline
À cette étape, vous créez un pipeline avec une activité de copie dans la fabrique de données. L’activité de copie permet de copier les données d’un stockage Blob vers une base de données SQL. Dans le [didacticiel de démarrage rapide](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal), vous avez créé un pipeline en procédant comme suit :

1. Créer le service lié.
1. Créer des jeux de données d’entrée et de sortie.
1. Créer un pipeline.

Dans ce didacticiel, vous commencez par créer le pipeline. Puis vous créez des jeux de données et des services liés lorsque vous en avez besoin pour configurer le pipeline.

1. Dans la page **Prise en main**, cliquez sur **Créer un pipeline**.

   ![Création d’un pipeline](./media/doc-common-process/get-started-page.png)
1. Dans le volet **Propriétés** du pipeline, sous **Nom**, entrez **CopyPipeline** pour le nom du pipeline.

1. Dans la boîte à outils **Activités**, développez la catégorie **Déplacer et transformer**, puis faites glisser l’activité **Copier les données** de la boîte à outils vers l’aire du concepteur de pipeline. Spécifiez **CopyFromBlobToSql** pour le **Nom**.

    ![Activité de copie](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurer la source

>[!TIP]
>Dans ce tutoriel, vous utilisez une *clé de compte* comme type d’authentification pour votre magasin de données source, mais vous pouvez choisir d’autres méthodes d’authentification prises en charge : un *URI SAS*, un *principal de service* et une *identité managée*, si nécessaire. Pour plus d’informations, reportez-vous aux sections correspondantes de [cet article](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties).
>Pour stocker de manière sécurisée des secrets de magasins de données, il est également recommandé d’utiliser un coffre de clés Azure. Pour obtenir des illustrations détaillées, reportez-vous à [cet article](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault).

#### <a name="create-source-dataset-and-linked-service"></a>Créer un jeu de données source et un service lié

1. Accédez à l’onglet **Source**. Sélectionnez **+ Nouveau** pour créer un jeu de données source.

1. Dans la boîte de dialogue **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis **Continuer**. Sachant que les données sources se trouvent dans un stockage Blob, vous devez sélectionner le **Stockage Blob Azure** pour le jeu de données source.

1. Dans la boîte de dialogue **Sélectionner le format**, choisissez le type de format de vos données, puis sélectionnez **Continuer**.

1. Dans la boîte de dialogue **Définir les propriétés**, entrez **SourceBlobDataset** comme nom. Cochez la case **Première ligne comme en-tête**. Sous la zone de texte **Service lié**, sélectionnez **+ Nouveau**.

1. Dans la boîte de dialogue **Nouveau service lié (Stockage Blob Azure)** , entrez **AzureStorageLinkedService** pour le nom, puis sélectionnez votre compte de stockage dans la liste **Nom du compte de stockage**. 

1. Veillez à activer **Création interactive**. L’activation peut prendre environ 1 minute.

    ![Création interactive](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Sélectionnez **Tester la connexion**. Celle-ci doit échouer quand le compte de stockage autorise l’accès uniquement à partir du « réseau sélectionné » et nécessite Azure Data Factory pour créer un point de terminaison privé (qui doit être approuvé avant son utilisation) vers le compte. Dans le message d’erreur, vous devez voir un lien permettant de créer un **point de terminaison privé**. Vous pouvez le suivre pour créer un point de terminaison privé managé. *Vous pouvez également créer un point de terminaison privé managé en accédant directement à l’onglet Gérer et en appliquant les instructions de la [section suivante](#create-a-managed-private-endpoint).*
> [!NOTE]
> Il se peut que l’onglet Gérer ne soit pas disponible pour toutes les instances de fabrique de données. Si vous ne le voyez pas, vous pouvez toujours accéder aux points de terminaison privés en sélectionnant : onglet « **Créer** » --> « **Connexions** » --> « **Point de terminaison privé** ».
1. Gardez la boîte de dialogue ouverte, puis accédez au compte de stockage sélectionné précédemment.

1. Suivez les instructions de [cette section](#approval-of-a-private-link-in-storage-account) pour approuver la liaison privée.

1. Revenez à la boîte de dialogue. Sélectionnez de nouveau **Tester la connexion**, puis sélectionnez **Créer** pour déployer le service lié.

1. Après la création du service lié, la page **Définir les propriétés** s’affiche de nouveau. En regard de **Chemin d’accès du fichier**, sélectionnez **Parcourir**.

1. Accédez au dossier **adftutorial/input**, sélectionnez le fichier **emp.txt**, puis sélectionnez **OK**.

1. Sélectionnez **OK**. La page du pipeline s’affiche automatiquement. Sous l’onglet **Source**, vérifiez que **SourceBlobDataset** est sélectionné. Pour afficher un aperçu des données dans cette page, sélectionnez **Aperçu des données**.

    ![Jeu de données source](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Créer un point de terminaison privé managé

Si vous n’avez pas cliqué sur le lien hypertexte lors du test de la connexion ci-dessus, suivez le chemin suivant. Vous devez maintenant créer un point de terminaison privé managé que vous connecterez au service lié créé ci-dessus.

1. Accédez à l’onglet Gérer.
> [!NOTE]
> Il se peut que l’onglet Gérer ne soit pas disponible pour toutes les instances de fabrique de données. Si vous ne le voyez pas, vous pouvez toujours accéder aux points de terminaison privés en sélectionnant : onglet « **Créer** » --> « **Connexions** » --> « **Point de terminaison privé** ».

1. Accédez à la section Points de terminaison privés managés.

1. Sélectionnez **+ Nouveau** sous Points de terminaison privés managés.

    ![Nouveau point de terminaison privé managé](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Sélectionnez la vignette Stockage Blob Azure dans la liste, puis sélectionnez **Continuer**.

1. Entrez le nom du compte Stockage Azure créé ci-dessus.

1. Sélectionnez **Create** (Créer).

1. Après avoir attendu quelques secondes, vous devriez voir que la liaison privée créée nécessite une approbation.

1. Sélectionnez le point de terminaison privé que vous avez créé précédemment. Vous pouvez voir un lien hypertexte qui vous permet d’approuver le point de terminaison privé au niveau du compte de stockage.

    ![Point de terminaison privé managé](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Approbation d’une liaison privée dans le compte de stockage
1. Dans la section Paramètres du compte de stockage, accédez à **Connexions de point de terminaison privé**.

1. Cochez le point de terminaison privé que vous avez créé précédemment, puis sélectionnez **Approuver**.

    ![Approuver le point de terminaison privé](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Ajoutez une description et cliquez sur **Oui**.
1. Revenez à la section **Points de terminaison privés managés** de l’onglet **Gérer** dans Azure Data Factory.
1. L’approbation de votre point de terminaison privé apparaîtra dans l’interface utilisateur d’Azure Data Factory au bout d’environ 1 à 2 minutes.


### <a name="configure-sink"></a>Configurer le récepteur
>[!TIP]
>Dans ce tutoriel, vous utilisez l’*authentification SQL* comme type d’authentification pour votre magasin de données récepteur, mais vous pouvez choisir d’autres méthodes d’authentification prises en charge : un *principal de service* et une *identité managée*, si nécessaire. Pour plus d’informations, reportez-vous aux sections correspondantes de [cet article](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties).
>Pour stocker de manière sécurisée des secrets de magasins de données, il est également recommandé d’utiliser un coffre de clés Azure. Pour obtenir des illustrations détaillées, reportez-vous à [cet article](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault).

#### <a name="create-sink-dataset-and-linked-service"></a>Créer un jeu de données récepteur et un service lié
1. Accédez à l’onglet **Récepteur**, puis sélectionnez **+ Nouveau** pour créer un jeu de données récepteur.

1. Dans la boîte de dialogue **Nouveau jeu de données**, entrez « SQL » dans la zone de recherche pour filtrer les connecteurs, sélectionnez **Azure SQL Database**, puis **Continuer**. Dans ce didacticiel, vous copiez des données vers une base de données SQL.

1. Dans la boîte de dialogue **Définir les propriétés**, entrez **OutputSqlDataset** comme nom. Dans la liste déroulante **Service lié**, sélectionnez **+ Nouveau**. Un jeu de données doit être associé à un service lié. Le service lié comporte la chaîne de connexion utilisée par Data Factory pour établir la connexion à la base de données SQL lors de l’exécution. Le jeu de données spécifie le conteneur, le dossier et le fichier (facultatif) dans lequel les données sont copiées.

1. Dans la boîte de dialogue **Nouveau service lié (Azure SQL Database)** , effectuez les étapes suivantes :

    1. Sous **Nom**, entrez **AzureSqlDatabaseLinkedService**.
    1. Sous **Nom du serveur**, sélectionnez votre instance SQL Server.
    1. Veillez à activer **Création interactive**.
    1. Sous **Nom de la base de données**, sélectionnez votre base de données SQL.
    1. Sous **Nom d’utilisateur**, entrez le nom de l’utilisateur.
    1. Sous **Mot de passe**, entrez le mot de passe de l’utilisateur.
    1. Sélectionnez **Tester la connexion**. Celle-ci doit échouer, car le serveur SQL autorise l’accès uniquement à partir des « réseaux sélectionnés » et nécessite Azure Data Factory pour créer un point de terminaison privé (qui doit être approuvé avant son utilisation) vers le serveur. Dans le message d’erreur, vous devez voir un lien permettant de créer un **point de terminaison privé**. Vous pouvez le suivre pour créer un point de terminaison privé managé. *Vous pouvez également créer un point de terminaison privé managé en accédant directement à l’onglet Gérer et en appliquant les instructions de la section suivante.*
    1. Gardez la boîte de dialogue ouverte, puis accédez au serveur SQL sélectionné précédemment.    
    1. Suivez les instructions de [cette section](#approval-of-a-private-link-in-sql-server) pour approuver la liaison privée.
    1. Revenez à la boîte de dialogue. Sélectionnez de nouveau **Tester la connexion**, puis sélectionnez **Créer** pour déployer le service lié.

1. La boîte de dialogue **Définir les propriétés** s’affiche automatiquement. Sélectionnez **[dbo].[emp]** dans le champ **Table**. Sélectionnez ensuite **OK**.

1. Accédez à l’onglet avec le pipeline, puis dans **Jeu de données récepteur**, vérifiez que l’option **OutputSqlDataset** est sélectionnée.

    ![Onglet Pipeline](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)       

Si vous le souhaitez, vous pouvez mapper le schéma de la source au schéma correspondant de la destination en suivant les instructions mentionnées dans [Mappage de schéma dans l’activité de copie](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping).

#### <a name="create-a-managed-private-endpoint"></a>Créer un point de terminaison privé managé

Si vous n’avez pas cliqué sur le lien hypertexte lors du test de la connexion ci-dessus, suivez le chemin suivant. Vous devez maintenant créer un point de terminaison privé managé que vous connecterez au service lié créé ci-dessus.

1. Accédez à l’onglet Gérer.
1. Accédez à la section Points de terminaison privés managés.
1. Sélectionnez **+ Nouveau** sous Points de terminaison privés managés.

    ![Nouveau point de terminaison privé managé](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Sélectionnez la vignette Azure SQL Database dans la liste, puis sélectionnez **Continuer**.
1. Entrez le nom du serveur SQL que vous avez sélectionné précédemment.
1. Sélectionnez **Create** (Créer).
1. Après avoir attendu quelques secondes, vous devriez voir que la liaison privée créée nécessite une approbation.
1. Sélectionnez le point de terminaison privé que vous avez créé précédemment. Vous pouvez voir un lien hypertexte qui vous permet d’approuver le point de terminaison privé au niveau du serveur SQL.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Approbation d’une liaison privée dans le serveur SQL
1. Dans la section Paramètres du serveur SQL, accédez à **Connexions de point de terminaison privé**.
1. Cochez le point de terminaison privé que vous avez créé précédemment, puis sélectionnez **Approuver**.
1. Ajoutez une description et cliquez sur **Oui**.
1. Revenez à la section **Points de terminaison privés managés** de l’onglet **Gérer** dans Azure Data Factory.
1. L’approbation de votre point de terminaison privé doit prendre environ 1-2 minutes.

#### <a name="debug-and-publish-the-pipeline"></a>Déboguer et publier le pipeline

Vous pouvez déboguer un pipeline avant de publier des artefacts (services liés, jeux de données et pipeline) dans Data Factory ou votre propre référentiel Azure Repos Git.

1. Pour déboguer le pipeline, sélectionnez **Déboguer** dans la barre d’outils. L’état d’exécution du pipeline apparaît dans l’onglet **Sortie** au bas de la fenêtre.
2. Une fois que le pipeline peut s’exécuter correctement, sélectionnez **Publier tout** dans la barre d’outils supérieure. Cette action publie les entités (jeux de données et pipelines) que vous avez créées dans Data Factory.
3. Patientez jusqu’à voir le message **Publication réussie**. Pour voir les messages de notification, cliquez sur **Afficher les notifications** en haut à droite (bouton en forme de cloche).


#### <a name="summary"></a>Résumé
Dans cet exemple, le pipeline copie les données d’un stockage Blob vers une base de données Azure SQL en utilisant un point de terminaison privé dans un réseau virtuel managé. Vous avez appris à :

> * Créer une fabrique de données
> * Créer un pipeline avec une activité de copie

