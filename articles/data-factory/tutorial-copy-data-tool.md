---
title: Copier des données du stockage Blob Azure dans SQL à l’aide de l’outil Copier des données
description: Créez une fabrique de données Azure, puis utilisez l’outil Copier les données pour copier des données depuis Stockage Blob Azure vers une base de données SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 09/11/2018
ms.openlocfilehash: 6335fce717772e268f711c2e6e5050fa8c17d573
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977331"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Copier des données à partir du Stockage Blob Azure vers une base de données SQL en utilisant l’outil Copier les données

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version actuelle](tutorial-copy-data-tool.md)

Dans ce didacticiel, vous utilisez le portail Azure pour créer une fabrique de données. Vous utilisez ensuite l’outil Copier les données pour créer un pipeline qui copie des données depuis le stockage Blob Azure vers une base de données SQL.

> [!NOTE]
> Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Dans ce tutoriel, vous effectuerez les étapes suivantes :
> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>Conditions préalables requises

* **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte de stockage Azure** : Utilisez le stockage blob comme magasin de données _source_. Si vous ne possédez pas de compte de stockage Azure, consultez les instructions dans [Créer un compte de stockage](../storage/common/storage-account-create.md).
* **Azure SQL Database** : Utilisez une base de données SQL comme magasin de données _récepteur_. Si vous n’avez pas de base de données SQL, consultez les instructions dans [Créer une base de données SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Créer un objet blob et une table SQL

Préparez votre stockage d'objets blob et votre base de données SQL pour ce didacticiel effectuant les étapes suivantes.

#### <a name="create-a-source-blob"></a>Créer un objet blob source

1. Lancez le **Bloc-notes**. Copiez le texte suivant et enregistrez-le dans un fichier nommé **inputEmp.txt** sur votre disque :

    ```
    John|Doe
    Jane|Doe
    ```

1. Créez un conteneur nommé **adfv2tutorial** et chargez le fichier inputEmp.txt dedans. Vous pouvez utiliser le portail Azure ou différents outils, comme l’[Explorateur Stockage Azure](https://storageexplorer.com/) pour effectuer ces tâches.

#### <a name="create-a-sink-sql-table"></a>Créer une table SQL de récepteur

1. Utilisez le script SQL suivant pour créer une table nommée **dbo.emp** dans votre base de données SQL :

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

2. Autorisez les services Azure à accéder au serveur SQL. Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur exécutant SQL Database. Ce paramètre permet à Data Factory d’écrire des données dans votre instance de base de données. Pour vérifier et activer ce paramètre, accédez à votre serveur SQL Azure > Vue d’ensemble > Définir le pare-feu serveur > définissez l’option **Autoriser l’accès aux services Azure** sur **Activé**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Analytics** > **Data Factory** :

    ![Création d’une fabrique de données](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.

    Le nom de votre fabrique de données doit être un _nom global unique_. Vous pouvez recevoir le message d’erreur suivant :

    ![Message d’erreur de nouvelle fabrique de données](./media/doc-common-process/name-not-available-error.png)

    Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_ **ADFTutorialDataFactory**. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
1. Sélectionnez l’**abonnement** Azure dans lequel vous créez la nouvelle fabrique de données.
1. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :

    a. Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    b. Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.
    
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).

1. Sous **Version**, sélectionnez **V2** pour la version.
1. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (tels que le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par votre fabrique de données peuvent se trouver dans d’autres emplacements et régions.
1. Sélectionnez **Create** (Créer).

1. Une fois la création terminée, la page d’accueil **Data Factory** s’affiche.

    ![Page d’accueil Data Factory](./media/doc-common-process/data-factory-home-page.png)
1. Pour lancer l’interface utilisateur d’Azure Data Factory dans un onglet séparé, cliquez sur la vignette **Créer et surveiller**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utiliser l’outil Copier les données pour créer un pipeline

1. Sur la page **Prise en main**, sélectionnez la vignette **Copier des données** pour démarrer l’outil Copier des données.

    ![Vignette de l’outil Copier les données](./media/doc-common-process/get-started-page.png)
1. Sur la page **Propriétés**, sous **Nom de la tâche**, saisissez **CopyFromBlobToSqlPipeline**. Sélectionnez ensuite **Suivant**. L’interface utilisateur de Data Factory crée un pipeline avec le nom spécifié.

1. Sur la page **Banque de données source**, procédez comme suit :

    a. Cliquez sur **+ Créer une connexion** pour ajouter une connexion.

    b. Sélectionnez **Stockage Blob Azure** à partir de la galerie, puis sélectionnez **Continuer**.

    c. Sur la page **Nouveau service lié**, sélectionnez votre compte de stockage dans la liste **Nom du compte de stockage**, puis cliquez sur **Terminer**.

    d. Sélectionnez le service lié récemment créé comme source, puis cliquez sur **Suivant**.

    ![Sélectionner un service lié source](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit :

    a. Cliquez sur **Parcourir** pour accéder au dossier **adfv2tutorial/input**, sélectionnez le fichier **inputEmp.txt**, puis cliquez sur **Choisir**.

    b. Cliquez sur **Suivant** pour passer à l’étape suivante.

1. Sur la page **Paramètres de format de fichier**, notez que l’outil détecte automatiquement les séparateurs de ligne et de colonne. Sélectionnez **Suivant**. Vous pouvez également afficher un aperçu des données et un schéma des données d’entrée sur cette page.

    ![Paramètres de format de fichier](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Sur la page **Banque de données de destination**, procédez comme suit :

    a. Cliquez sur **+ Créer une connexion** pour ajouter une connexion.

    b. Sélectionnez **Azure SQL Database** dans la galerie, puis sélectionnez **Continuer**.

    c. Sur la page **Nouveau service lié**, sélectionnez votre nom de serveur et votre nom de base de données dans la liste déroulante, spécifiez le nom d’utilisateur et le mot de passe, puis sélectionnez **Terminer**.

    ![Configurer Azure SQL DB](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Sélectionnez le service lié nouvellement créé comme récepteur, puis cliquez sur **Suivant**.

    ![Sélectionner le service lié récepteur](./media/tutorial-copy-data-tool/select-sink-linked-service.png)

1. Sur la page **Mappage de table**, sélectionnez la table **[dbo].[emp]** , puis sélectionnez **Suivant**.

1. Sur la page **Mappage de schéma**, notez que les deux premières colonnes dans le fichier d’entrée sont mappées aux colonnes **FirstName** et **LastName** de la table **emp**. Sélectionnez **Suivant**.

    ![Page Mappage de schéma](./media/tutorial-copy-data-tool/schema-mapping.png)
1. Sur la page **Paramètres**, cliquez sur **Suivant**.
1. Sur la page **Résumé**, vérifiez les paramètres, puis sélectionnez **Suivant**.
1. Sur la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche).
1. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution d’activité et de réexécuter le pipeline. Sélectionnez **Actualiser** pour actualiser la liste.

1. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Pour plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Exécutions de pipeline** en haut. Sélectionnez **Actualiser** pour actualiser l’affichage.

    ![Surveiller des exécutions d’activités](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Vérifiez que les données sont insérées dans la table **emp** dans votre base de données SQL.


1. Sélectionnez l’onglet **Auteur** sur la gauche pour basculer en mode éditeur. Vous pouvez mettre à jour les services, jeux de données et pipelines liés créés par l’outil à l’aide de l’éditeur. Pour plus de détails sur la modification de ces entités dans l’interface utilisateur de Data Factory, consultez [la version du portail Azure de ce didacticiel](tutorial-copy-data-portal.md).

## <a name="next-steps"></a>Étapes suivantes
Le pipeline dans cet exemple copie des données d’un stockage d’objets blob vers une base de données SQL. Vous avez appris à :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

Passez au didacticiel suivant pour en savoir plus sur la copie des données locales vers le cloud :

>[!div class="nextstepaction"]
>[Copier des données locales vers le cloud](tutorial-hybrid-copy-data-tool.md)
