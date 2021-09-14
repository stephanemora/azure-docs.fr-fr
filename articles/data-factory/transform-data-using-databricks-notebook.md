---
title: Exécuter une instance Databricks Notebook avec l’activité
description: Découvrez comment vous pouvez utiliser l’activité Databricks Notebook d’une fabrique de données Azure pour exécuter une instance Databricks Notebook sur un cluster de travaux Databricks.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 08/31/2021
ms.openlocfilehash: 15e08069be22cc71e228a1ad878e5f2b5424ce89
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541738"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Exécuter une instance Databricks Notebook avec l’activité Databricks Notebook dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dans ce tutoriel, vous allez utiliser le portail Azure pour créer un pipeline Azure Data Factory qui exécute une instance Databricks Notebook sur le cluster de travaux Databricks. Il transmet également les paramètres Azure Data Factory à l’instance Databricks Notebook pendant l’exécution.

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

  - Créer une fabrique de données.

  - Créer un pipeline qui utilise l’activité Databricks Notebook.

  - Déclencher une exécution du pipeline.

  - Surveiller l’exécution du pipeline.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

Pour une présentation de onze minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Prérequis

  - **Espace de travail Azure Databricks**. [Créez un espace de travail Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) ou utilisez-en un existant. Créez une instance Python Notebook dans votre espace de travail Azure Databricks. Exécutez ensuite l’instance Notebook et transmettez-lui les paramètres via Azure Data Factory.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.

1. Dans le menu du portail Azure, sélectionnez **Créer une ressource**, **Intégration**, puis **Data Factory**.

    :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Capture d’écran montrant la sélection de Data Factory dans le volet Nouveau.":::

1. Dans la page **Créer une fabrique de données**, sous l’onglet **De base**, sélectionnez l’**Abonnement** Azure dans lequel vous voulez créer la fabrique de données.

1. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
    
    1. Sélectionnez un groupe de ressources existant dans la liste déroulante.
    
    1. Sélectionnez **Créer**, puis entrez le nom d’un nouveau groupe de ressources.

    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).

1. Pour **Région**, sélectionnez l’emplacement de la fabrique de données.

    La liste n’affiche que les emplacements pris en charge par Data Factory et où vos métadonnées Azure Data Factory sont stockées. Les magasins de données associés (tels que Stockage Azure et Azure SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par Data Factory peuvent s’exécuter dans d’autres régions.

1. Pour **Nom**, entrez **ADFTutorialDataFactory**.
    
    Le nom de la fabrique de données Azure doit être un nom *global unique*. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, utilisez **&lt;votrenom&gt;ADFTutorialDataFactory**). Consultez l’article [Data Factory - Règles d’affectation des noms](./naming-rules.md) pour en savoir plus sur les règles d’affectation des noms d’artefacts Data Factory.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Capture d’écran montrant l’erreur qui se produit quand un nom n’est pas disponible.":::

1. Pour **Version**, sélectionnez **V2**.

1. Sélectionnez **Suivant : Configuration Git**, puis cochez la case **Configurer Git plus tard**.

1. Sélectionnez **Vérifier + créer**, puis sélectionnez **Créer** une fois la validation passée. 

1. Une fois la ressource créée, sélectionnez **Accéder à la ressource** pour ouvrir la page **Data Factory**. Sélectionnez la vignette **Ouvrir Azure Data Factory Studio** pour démarrer l’interface utilisateur d’Azure Data Factory sous un onglet de navigateur distinct.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Capture d’écran montrant la page d’accueil d’Azure Data Factory, avec la vignette Ouvrir Azure Data Factory Studio.":::

## <a name="create-linked-services"></a>Créez des services liés

Dans cette section, vous allez créer un service Databricks lié. Ce service lié contient les informations de connexion au cluster Databricks :

### <a name="create-an-azure-databricks-linked-service"></a>Créer un service Azure Databricks lié

1.  Dans la page d’accueil, basculez vers l’onglet **Gérer** dans le volet gauche.

    ![Capture d’écran montrant l’onglet Gérer.](media/doc-common-process/get-started-page-manage-button.png)

1.  Sélectionnez **Services liés** sous **Connexions**, puis **+ Nouveau**.
    
    ![Capture d’écran montrant comment créer une connexion.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-6.png)

1.  Dans la fenêtre **Nouveau service lié**, sélectionnez **Calcul** &gt; **Azure Databricks**, puis **Continuer**.
    
    ![Capture d’écran montrant comment spécifier un service lié Databricks.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-7.png)

1.  Dans la fenêtre **Nouveau service lié**, effectuez les étapes suivantes :
    
    1.  Entrez **_AzureDatabricks\_LinkedService_** comme **Nom**.
    
    1.  Sélectionnez l’**espace de travail Databricks** approprié dans lequel vous exécuterez votre notebook.

    1.  Dans **Sélectionner un cluster**, sélectionnez **Nouveau cluster de travail**.
    
    1.  Dans **URL de l’espace de travail Databrick**, les informations doivent être renseignées automatiquement.

    1.  Dans le champ **Jeton d’accès**, indiquez le jeton généré à partir de l’espace de travail Azure Databricks. Vous trouverez la procédure [ici](https://docs.databricks.com/api/latest/authentication.html#generate-token).

    1.  Dans **Version du cluster**, sélectionnez **4.2** (avec Apache Spark 2.3.1, Scala 2.11).

    1.  Dans **Cluster node type** (Type de nœud de cluster), sélectionnez **Standard\_D3\_v2** sous la catégorie **General Purpose (HDD)** (Usage général (HDD)) pour ce didacticiel. 
    
    1.  Dans **Rôles de travail**, entrez **2**.
    
    1.  Sélectionnez **Create** (Créer).

        ![Capture d’écran montrant la configuration du nouveau service lié Azure Databricks.](media/transform-data-using-databricks-notebook/new-databricks-linked-service.png)

## <a name="create-a-pipeline"></a>Créer un pipeline

1.  Cliquez sur le bouton **+** (plus), puis sélectionnez **Pipeline** dans le menu.

    ![Capture d’écran montrant les boutons permettant de créer un pipeline.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-9.png)

1.  Créez un **paramètre** à utiliser dans le **pipeline**. Vous pourrez ensuite le transmettre à l’activité Databricks Notebook. Dans le pipeline vide, sélectionnez l’onglet **Paramètres**, puis sélectionnez **+ Nouveau** et entrez « **name** » comme nom.

    ![Capture d’écran montrant comment créer un paramètre.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-10.png)

    ![Capture d’écran montrant comment créer le paramètre name.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-11.png)

1.  Dans la boîte à outils **Activités**, étendez **Databricks**. Faites glisser l’activité **Notebook** depuis la boîte à outils **Activités** vers la surface du concepteur de pipeline.

    ![Capture d’écran montrant comment faire glisser le notebook vers la surface du concepteur.](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  Dans les propriétés de la fenêtre d’activité **Databricks** **Notebook** en bas, effectuez les étapes suivantes :

    1. Basculez vers l’onglet **Azure Databricks**.

    1. Sélectionnez **AzureDatabricks\_LinkedService** (créé lors de la procédure précédente).

    1. Basculez vers l’onglet **Paramètres** .

    1. Parcourez pour sélectionner un chemin d’accès à **Notebook** Databricks. Créez une instance Notebook et spécifiez le chemin d’accès ici. Vous obtenez le chemin d’accès de l’instance Notebook en procédant comme suit.

       1. Lancez votre espace de travail Azure Databricks.

       1. Créez un **nouveau dossier** dans l’espace de travail et nommez-le **adftutorial**.

          ![Capture d’écran montrant comment créer un dossier.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Capture d’écran montrant comment créer un notebook.](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) Créez un notebook (Python) nommé **mynotebook** dans le dossier **adftutorial**. Cliquez sur **Créer**.

          ![Capture d’écran montrant comment créer un notebook.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Capture d’écran montrant comment définir les propriétés du nouveau notebook.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. Dans l’instance Notebook récemment créée, mynotebook, ajoutez le code suivant :

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           y = dbutils.widgets.get("input")
           print ("Param -\'input':")
           print (y)
           ```

           ![Capture d’écran montrant comment créer des widgets pour des paramètres.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. Le **chemin du notebook** dans ce cas est **/adftutorial/mynotebook**.

1.  Revenez à la **l’outil de création de l’interface utilisateur de la fabrique de données**. Accédez à l’onglet **Paramètres** sous l’activité **Notebook1**.

    a.  Ajoutez un **paramètre** à l’activité Notebook. Utilisez le même paramètre que celui ajouté précédemment au **pipeline**.

       ![Capture d’écran montrant comment ajouter un paramètre.](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Nommez le paramètre **input** et indiquez la valeur sous la forme de l’expression **\@pipeline().parameters.name**.

1.  Pour valider le pipeline, cliquez sur le bouton **Valider** dans la barre d’outils. Pour fermer la fenêtre de validation, sélectionnez le bouton **Fermer**.

    ![Capture d’écran montrant comment valider le pipeline.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-18.png)

1.  Sélectionnez **Tout publier**. L’interface utilisateur de Data Factory publie des entités (services liés et pipelines) sur le service Azure Data Factory.

    ![Capture d’écran montrant comment publier les nouvelles entités Data Factory.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-19.png)

## <a name="trigger-a-pipeline-run"></a>Déclencher une exécution du pipeline

Sélectionnez **Ajouter un déclencheur** dans la barre d’outils, puis **Déclencher maintenant**.

![Capture d’écran montrant comment sélectionner la commande « Déclencher maintenant ».](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-20.png)

La boîte de dialogue **Exécution de pipeline** invite à saisir le paramètre **name**. Utilisez ici **/path/filename** comme paramètre. Sélectionnez **OK**.

![Capture d’écran montrant comment fournir une valeur pour les paramètres name.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image-21.png)

## <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

1.  Basculez vers l’onglet **Surveiller**. Vérifiez qu’un pipeline est exécuté. Il faut compter environ 5 à 8 minutes pour créer un cluster de travaux Databricks, où s’exécute l’instance Notebook.

    ![Capture d’écran montrant comment superviser le pipeline.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Cliquez régulièrement sur **Actualiser** pour vérifier l’état de l’exécution des pipelines.

1.  Pour voir les exécutions d’activités associées à l’exécution du pipeline, cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions**.

    ![Capture d’écran montrant comment afficher les exécutions d’activités.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Vous pouvez basculer vers la vue des exécutions de pipelines en cliquant sur le lien **Pipelines** en haut.

## <a name="verify-the-output"></a>Vérifier la sortie

Vous pouvez vous connecter à l’**espace de travail Azure Databricks**, accéder à **Clusters** et voir le statut du **Travail** : *en attente d’exécution, en cours d’exécution ou terminé*.

![Capture d’écran montrant comment afficher le cluster de travail et le travail.](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Vous pouvez cliquer sur le **nom du travail** et naviguer pour afficher plus de détails. Une fois l’exécution réussie, vous pouvez valider les paramètres transmis et la sortie de l’instance Notebook Python.

![Capture d’écran montrant comment afficher les détails et la sortie de l’exécution.](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Étapes suivantes

Le pipeline dans cet exemple déclenche une activité Databricks Notebook et lui transmet un paramètre. Vous avez appris à :

  - Créer une fabrique de données.

  - Créer un pipeline qui utilise l’activité Databricks Notebook.

  - Déclencher une exécution du pipeline.

  - Surveiller l’exécution du pipeline.