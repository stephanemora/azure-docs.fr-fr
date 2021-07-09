---
title: Copier des données avec l’outil d’Azure Copier des données
description: Créez une fabrique de données Azure, puis utilisez l’outil Copier des données pour copier les données d’un emplacement situé dans le Stockage Blob Azure vers un autre emplacement.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: a66af648abc0ae48c0a4fcc7eb33ab7c1351c41b
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111371815"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Démarrage rapide : Utiliser l’outil Copier des données pour copier des données

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version actuelle](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dans ce guide de démarrage rapide, vous utilisez le portail Azure pour créer une fabrique de données. Vous utilisez ensuite l’outil Copier des données pour créer un pipeline qui copie des données depuis un dossier dans un stockage d’objets Blob Azure vers un autre dossier. 

> [!NOTE]
> Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md) avant de commencer ce guide de démarrage rapide. 

[!INCLUDE [data-factory-quickstart-prerequisites](includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
1. Accédez au [portail Azure](https://portal.azure.com). 
1. Dans le menu du portail Azure, sélectionnez **Créer une ressource** > **Intégration** > **Data Factory** :

    ![Création d’une fabrique de données](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
 
   Le nom de la fabrique de données Azure doit être un nom *global unique*. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, **&lt;votrenom&gt;ADFTutorialDataFactory**), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour en savoir plus sur les règles d’affectation des noms d’artefacts Data Factory.
  
   ![Erreur quand le nom n’est pas disponible](./media/doc-common-process/name-not-available-error.png)
1. Pour **Abonnement**, sélectionnez l’abonnement Azure dans lequel vous voulez créer la fabrique de données. 
1. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
     
   - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste. 
   - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
   Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).  
1. Pour **Version**, sélectionnez **V2**.
1. Pour **Emplacement**, sélectionnez l’emplacement de la fabrique de données.

   La liste n’affiche que les emplacements pris en charge par Data Factory et où vos métadonnées Azure Data Factory sont stockées. Les magasins de données associés (tels que Stockage Azure et Azure SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par Data Factory peuvent s’exécuter dans d’autres régions.

1. Sélectionnez **Create** (Créer).

1. Une fois la création terminée, la page **Data Factory** s’affiche. Sélectionnez la vignette **Créer et surveiller** pour démarrer l’application d’interface utilisateur (IU) d’Azure Data Factory dans un onglet séparé.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Page d’accueil d’Azure Data Factory, avec la vignette Créer et superviser.":::
    
## <a name="start-the-copy-data-tool"></a>Démarrer l’outil Copier des données

1. Sur la page **Prise en main**, sélectionnez la vignette **Copier des données** pour démarrer l’outil Copier des données.

   ![Vignette « Copier des données »](./media/doc-common-process/get-started-page.png)

1. Sur la page **Propriétés** de l’outil Copier des données, choisissez **Tâche de copie intégrée** sous **Type de tâche**, puis **Suivant**.

   ![Page « Propriétés »](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)

1. Sur la page **Banque de données source**, procédez comme suit :

    1. Cliquez sur **+Créer une connexion** pour ajouter une connexion.

    1. Sélectionnez le type de service lié à créer pour la connexion source. Dans ce tutoriel, nous utilisons le **Stockage Blob Azure**. Sélectionnez-le dans la galerie, puis sélectionnez **Continuer**.
    
       ![Sélectionner l’objet blob](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    1. Sur la page **Nouvelle connexion (Stockage Blob Azure)** , donnez un nom à votre connexion. Sélectionnez votre abonnement Azure dans la liste **Abonnement Azure** et votre compte de stockage dans la liste **Nom du compte de stockage**, testez la connexion, puis sélectionnez **Créer**. 

       ![Configurer le compte de stockage Blob Azure](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    1. Sélectionnez la nouvelle connexion dans le bloc **Connexion**.
    1. Dans la section **Fichier ou dossier**, sélectionnez **Parcourir** pour accéder au dossier **adftutorial/input**, sélectionnez le fichier **emp.txt**, puis cliquez sur **OK**.
    1. Cochez la case **Copie binaire** pour copier le fichier tel quel, puis sélectionnez **Suivant**.

       :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/source-data-store.png" alt-text="Capture d’écran montrant la page Magasin de données source.":::

1. Sur la page **Magasin de données de destination**, procédez comme suit :
    1. Sélectionnez la connexion **AzureBlobStorage** que vous avez créée dans le bloc **Connexion**.

    1. Dans la section **Chemin du dossier**, entrez **adftutorial/output**.

       :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/destination-data-store.png" alt-text="Capture d’écran montrant la page magasin de données de destination.":::

    1. Conservez les autres paramètres par défaut, puis sélectionnez **Suivant**.

1. Sur la page **Paramètres**, donnez un nom au pipeline, ajoutez sa description, puis sélectionnez **Suivant** pour utiliser d’autres configurations par défaut. 

    :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/settings.png" alt-text="Capture d’écran de la page Paramètres.":::

1. Dans la page **Résumé**, passez en revue tous les paramètres, puis cliquez sur **Suivant**. 

1. Sur la page **Déploiement terminé**, sélectionnez **Surveiller** pour surveiller le pipeline que vous avez créé. 

    ![Page « Déploiement terminé »](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. L’application bascule vers l’onglet **Surveiller**. Vous voyez l’état du pipeline dans cet onglet. Sélectionnez **Actualiser** pour actualiser la liste. Cliquez sur le lien situé sous **Nom du pipeline** pour afficher les détails de l’exécution d’activité ou réexécuter le pipeline. 
   
    ![Actualiser le pipeline](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Sur la page Exécutions d’activité, sélectionnez le lien **Détails** (icône en forme de lunettes) sous la colonne **Nom de l’activité** pour plus d’informations sur l’opération de copie. Pour plus d’informations sur les propriétés, consultez [Vue d’ensemble de l’activité de copie](copy-activity-overview.md). 

1. Pour revenir à l’affichage Exécutions de pipeline, sélectionnez le lien **Toutes les exécutions de pipeline** dans le menu de navigation. Sélectionnez **Actualiser** pour actualiser l’affichage. 

1. Vérifiez que le fichier **emp.txt** est créé dans le dossier **de sortie** du conteneur **adftutorial**. Si le dossier de sortie n’existe pas, le service Data Factory le crée automatiquement. 

1. Passez sur l’onglet **Auteur** au-dessus de l’onglet **Surveiller** du volet gauche afin de pouvoir modifier les services liés, jeux de données et pipelines. Pour apprendre à les modifier dans l’interface utilisateur de Data Factory, consultez [Créer une fabrique de données à l’aide du portail Azure](quickstart-create-data-factory-portal.md).

    ![Sélectionner l’onglet Auteur](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios, consultez les [didacticiels](tutorial-copy-data-portal.md). 
