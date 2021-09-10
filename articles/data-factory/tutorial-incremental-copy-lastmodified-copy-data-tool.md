---
title: Outil de données pour copier de façon incrémentielle les fichiers nouveaux et mis à jour
description: Créez une fabrique de données Azure, puis utilisez l’outil Copier des données afin de charger de nouveaux fichiers de façon incrémentielle sur la base de LastModifiedDate.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/12/2021
ms.openlocfilehash: 0c2cfe51e84931b90ab80588b16fe126aadaf4c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641466"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copier les fichiers nouveaux et modifiés selon LastModifiedDate à l’aide de l’outil Copier des données de façon incrémentielle

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dans ce didacticiel, vous utilisez le portail Azure pour créer une fabrique de données. Vous vous servez ensuite de l’outil Copier des données pour créer un pipeline qui copie uniquement et de façon incrémentielle les nouveaux fichiers et les fichiers modifiés du Stockage Blob Azure vers le Stockage Blob Azure. Il utilise `LastModifiedDate` pour déterminer les fichiers à copier.

Une fois que vous avez suivi les différentes étapes, Azure Data Factory analyse tous les fichiers du magasin source, applique le filtre de fichiers par `LastModifiedDate`et copie uniquement les fichiers qui ont été créés ou mis à jour depuis la dernière fois dans le magasin de destination. Sachez que, si Data Factory analyse un grand nombre de fichiers, les durées n’en restent pas moins longues. L’analyse des fichiers prend beaucoup de temps, même lorsque la quantité de données copiées est réduite.

> [!NOTE]
> Si vous débutez avec Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Dans ce tutoriel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Stockage Azure** : Utilisez le Stockage Blob comme magasin de données source et récepteur. Si vous n’avez pas de compte stockage Azure, suivez les instructions indiquées dans [Créer un compte de stockage](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Créer deux conteneurs dans le stockage d’objets blob

Préparez votre Stockage Blob pour le tutoriel :

1. Créez un conteneur nommé **source**. Vous pouvez utiliser différents outils pour effectuer cette tâche, comme [l’Explorateur Stockage Azure](https://storageexplorer.com/).

2. Créez un conteneur nommé **destination**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le volet de gauche, sélectionnez **Créer une ressource**. Sélectionnez **Intégration** > **Data Factory** :

   ![Sélection de Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.

   Le nom de votre fabrique de données doit être un nom global unique. Il est possible que vous receviez ce message d’erreur :

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nouveau message d’erreur de la fabrique de données pour le nom dupliqué.":::

   Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
3. Sous **Abonnement**, sélectionnez l’abonnement Azure dans lequel vous allez créer la fabrique de données.
4. Sous **Groupe de ressources**, vous avez deux possibilités :

    * Sélectionnez **Utiliser existant**, puis sélectionnez un groupe de ressources existant dans la liste.

    * Sélectionnez **Créer**, puis entrez un nom pour le groupe de ressources.
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).

5. Sous **Version**, sélectionnez **V2**.
6. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge apparaissent dans la liste. Les magasins de données (par exemple, Stockage Azure et SQL Database) et les services de calcul (par exemple, Azure HDInsight) utilisés par votre fabrique de données peuvent se trouver dans d’autres emplacements et régions.
8. Sélectionnez **Create** (Créer).
9. Une fois la fabrique de données créée, la page d’accueil de Data Factory apparaît.
10. Pour ouvrir l’interface utilisateur d’Azure Data Factory dans un onglet séparé, dans la vignette **Ouvrir Azure Data Factory Studio**, sélectionnez **Ouvrir** :

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Page d’accueil d’Azure Data Factory, avec la mosaïque Ouvrir Azure Data Factory Studio.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utiliser l’outil Copier les données pour créer un pipeline

1. Sur la page d’accueil d’Azure Data Factory, sélectionnez le titre **Ingérer** pour lancer l’outil Copier des données :

   ![Capture d’écran montrant la page d’accueil ADF.](./media/doc-common-process/get-started-page.png)

2. Dans la page **Propriétés**, effectuez les opérations suivantes :

    1. Sous **Type de tâche**, sélectionnez **Tâche de copie intégrée**.

    1. Sous **Cadence des tâches ou calendrier des tâches**, sélectionnez **Fenêtre bascule**.

    1. Sous **Périodicité**, entrez **15 Minute(s)** .

    1. Sélectionnez **Suivant**.

    ![Page de propriétés Copier des données](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Sur la page **Magasin de données source**, procédez de la façon suivante :

    1. Sélectionnez **+ Nouvelle connexion** pour ajouter une connexion.

    1. Sélectionnez **Stockage Blob Azure** dans la galerie, puis **Continuer** :

        ![Sélection du Stockage Blob Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    1. Dans la page **Nouvelle connexion (Stockage Blob Azure)** , sélectionnez votre abonnement Azure dans la liste **Abonnement Azure** et votre compte de stockage dans la liste **Nom du compte de stockage**. Testez la connexion, puis sélectionnez **Créer**.

    1. Sélectionnez la nouvelle connexion dans le bloc **Connexion**.

    1. Dans la section **Fichier ou dossier**, sélectionnez **Parcourir**, puis le dossier **source**, puis **OK**.

    1. Sous **Comportement de chargement de fichier**, sélectionnez **Chargement incrémentiel : LastModifiedDate**, puis **Copie binaire**.
    
    1. Sélectionnez **Suivant**.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png" alt-text="Capture d’écran montrant la page Magasin de données source.":::

4. Sur la page **Magasin de données de destination**, procédez comme suit :
    1. Sélectionnez la connexion **AzureBlobStorage** que vous avez créée. Il s’agit du même compte de stockage que la banque de données source.

    1. Dans la section **Chemin d’accès du dossier**, recherchez et sélectionnez le dossier de **destination**, puis sélectionnez **OK**.

    1. Sélectionnez **Suivant**.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page.png" alt-text="Capture d’écran montrant la page Magasin de données de destination.":::

5. Sur la page **Paramètres**, sous **Nom de la tâche**, saisissez **DeltaCopyFromBlobPipeline**, puis sélectionnez **Suivant**. Data Factory crée un pipeline portant le nom de tâche spécifié.

    :::image type="content" source="./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png" alt-text="Capture d’écran montrant la page Paramètres.":::

6. Sur la page **Résumé**, vérifiez les paramètres, puis sélectionnez **Suivant**.

    ![Page de résumé](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

7. Sur la page **Déploiement**, sélectionnez **Analyse** pour analyser le pipeline (tâche).

    ![Page Déploiement](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

8. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. L’application bascule vers l’onglet **Surveiller**. Vous voyez l’état du pipeline. Sélectionnez **Actualiser** pour actualiser la liste. Sélectionnez le lien qui se trouve sous **Nom du pipeline** pour afficher les détails de l’exécution d’activité ou réexécuter le pipeline.

    ![Actualisation de la liste et affichage des détails de l’exécution d’activité](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

9. Il n’y a qu’une seule activité (l’activité de copie) dans le pipeline ; vous ne voyez donc qu’une seule entrée. Pour obtenir des détails sur l’opération de copie, dans la page **Exécutions d’activités**, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Nom de l’activité**. Pour plus d’informations sur les propriétés, consultez [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

    ![Activité de copie dans le pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Étant donné qu’il n’existe aucun fichier dans le conteneur source de votre compte de Stockage Blob, vous ne trouverez aucun fichier copié dans le conteneur de destination du compte :

    ![Aucun fichier dans le conteneur source ou le conteneur de destination](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

10. Créez un fichier texte vide et nommez-le **file1.txt**. Chargez ce fichier texte dans le conteneur source de votre compte de stockage. Vous pouvez utiliser différents outils pour effectuer ces tâches, comme [l’Explorateur Stockage Azure](https://storageexplorer.com/).

    ![Création du fichier file1.txt et chargement dans le conteneur source](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

11. Pour revenir à la vue **Exécutions de pipelines**, dans la page **Exécutions d’activités**, dans le menu de navigation, sélectionnez le lien **Toutes les exécutions de pipelines**, puis attendez que le même pipeline soit à nouveau déclenché automatiquement.  

12. Une fois la deuxième exécution du pipeline terminée, suivez à nouveau la procédure précédente pour consulter les détails de l’exécution d’activité.  

    Vous constaterez qu’un fichier (file1.txt) a été copié du conteneur source vers le conteneur de destination de votre compte de Stockage Blob :

    ![file1.txt copié du conteneur source vers le conteneur de destination](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

13. Créez un autre fichier texte vide et nommez-le **file2.txt**. Chargez ce fichier texte dans le conteneur source de votre compte de Stockage Blob.

14. Répétez les étapes 11 et 12 pour le deuxième fichier texte. Vous constaterez que seul le nouveau fichier (file2.txt) a été copié du conteneur source vers le conteneur de destination de votre compte de stockage lors de cette exécution de pipeline.  

    Vous pourrez également vérifier qu’un seul fichier a été copié en analysant les fichiers avec [l’Explorateur Stockage Azure](https://storageexplorer.com/) :

    ![Analyse des fichiers avec l’Explorateur Stockage Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

## <a name="next-steps"></a>Étapes suivantes
Passez au tutoriel suivant pour apprendre à transformer les données avec un cluster Apache Spark sur Azure :

> [!div class="nextstepaction"]
>[Transformer des données dans le cloud en utilisant un cluster Apache Spark](tutorial-transform-data-spark-portal.md)
