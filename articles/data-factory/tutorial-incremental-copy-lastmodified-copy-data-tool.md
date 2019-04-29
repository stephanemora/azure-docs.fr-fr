---
title: Copier les fichiers nouveaux et modifiés selon LastModifiedDate & gt ; à l’aide de l’outil Copier des données de façon incrémentielle | Microsoft Docs
description: Créer une fabrique de données Azure, puis utilisez l’outil Copier des données à charger de façon incrémentielle selon LastModifiedDate & gt ; de nouveaux fichiers.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: 8308190e0e68365343fb50ca33f9bea75c3e4e66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61098740"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copier les fichiers nouveaux et modifiés selon LastModifiedDate & gt ; à l’aide de l’outil Copier des données de façon incrémentielle

Dans ce didacticiel, vous utiliserez le portail Azure pour créer une fabrique de données. Ensuite, vous allez utiliser l’outil Copier des données pour créer un pipeline qui copie incrémentielle les fichiers nouveaux et modifiés uniquement, selon leur **LastModifiedDate & lt ;** à partir du stockage Blob Azure vers stockage Blob Azure.

> [!NOTE]
> Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Dans ce didacticiel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>Conditions préalables

* **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte de stockage Azure** : Utilisation du stockage Blob en tant que le _source_ et _récepteur_ magasin de données. Si vous ne possédez pas de compte de stockage Azure, consultez les instructions dans [Créer un compte de stockage](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Créer deux conteneurs dans le stockage Blob

Préparez votre stockage d’objets Blob pour le didacticiel effectuant les étapes suivantes.

1. Créer un conteneur nommé **source**. Vous pouvez utiliser différents outils pour effectuer cette tâche, telles que [Azure Storage Explorer](https://storageexplorer.com/).

2. Créer un conteneur nommé **destination**. 

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Données + Analytique** > **Data Factory** : 
   
   ![Sélection Data Factory dans le volet « Nouveau »](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**. 
      
     ![Nouvelle fabrique de données](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Le nom de votre fabrique de données doit être un _nom global unique_. Vous pouvez recevoir le message d’erreur suivant :
   
   ![Message d’erreur de nouvelle fabrique de données](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
3. Sélectionnez Azure **abonnement** dans lequel vous allez créer la fabrique de données. 
4. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
     
    * Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    * Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).

5. Sous **version**, sélectionnez **V2**.
6. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (par exemple, le stockage Azure et SQL Database) et services de calcul (par exemple, Azure HDInsight) qui utilise votre fabrique de données peuvent être dans autres emplacements et régions.
7. Sélectionnez **Épingler au tableau de bord**. 
8. Sélectionnez **Créer**.
9. Sur le tableau de bord, reportez-vous à la **déploiement de Data Factory** vignette pour afficher l’état du processus.

    ![Déploiement de vignette de fabrique de données](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Une fois la création terminée, la page d’accueil **Data Factory** s’affiche.
   
    ![Page d’accueil Data Factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Pour ouvrir l’interface utilisateur de Azure Data Factory (IU) sur un onglet séparé, sélectionnez le **créer et surveiller** vignette. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utiliser l’outil Copier les données pour créer un pipeline

1. Sur le **prise en main** page, sélectionnez le **copier des données** titre pour ouvrir l’outil Copier des données. 

   ![Vignette de l’outil Copier les données](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Sur le **propriétés** page, procédez comme suit :

    a. Sous **nom_tâche**, entrez **DeltaCopyFromBlobPipeline**.

    b. Sous **cadence de tâche** ou **planification de la tâche**, sélectionnez **exécuter régulièrement selon planification**.

    c. Sous **Type de déclencheur**, sélectionnez **fenêtre bascule**.
    
    d. Sous **périodicité**, entrez **minute (s 15)**. 
    
    e. Sélectionnez **Suivant**. 
    
    L’interface utilisateur de Data Factory crée un pipeline avec le nom spécifié. 

    ![Page Propriétés](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Sur la page **Banque de données source**, procédez comme suit :

    a. Sélectionnez **+ créer une nouvelle connexion**, pour ajouter une connexion.
    
    ![Page Magasin de données sources](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Sélectionnez **stockage Blob Azure** à partir de la galerie, puis **continuer**.
    
    ![Page Magasin de données sources](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Sur le **nouveau Service lié** , sélectionnez votre compte de stockage à partir de la **nom de compte de stockage** liste, puis sélectionnez **Terminer**.
    
    ![Page Magasin de données sources](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Sélectionnez le service lié qui vient d’être créé, puis **suivant**. 
    
   ![Page Magasin de données sources](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit :
    
    a. Recherchez et sélectionnez le **source** dossier, puis sélectionnez **choisir**.
    
    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Sous **le comportement de chargement de fichier**, sélectionnez **chargement incrémentiel : LastModifiedDate & lt ;**.
    
    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Vérifiez **copie binaire** et sélectionnez **suivant**.
    
     ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Sur le **magasin de données de Destination** page, sélectionnez **AzureBlobStorage**. Il s’agit du même compte de stockage en tant que magasin de données source. Sélectionnez ensuite **Suivant**.

    ![Page Magasin de données de destination](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit :
    
    a. Recherchez et sélectionnez le **destination** dossier, puis sélectionnez **choisir**.
    
    ![Choisir le fichier ou le dossier de sortie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Sélectionnez **Suivant**.
    
     ![Choisir le fichier ou le dossier de sortie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Sur la page **Paramètres**, cliquez sur **Suivant**. 

    ![Page Paramètres](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Sur le **Résumé** page, passez en revue les paramètres, puis sélectionnez **suivant**.

    ![Page de résumé](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Sur la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche).

    ![Page Déploiement](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution d’activité et de réexécuter le pipeline. Sélectionnez **Actualiser** pour actualiser la liste, puis sélectionnez le **afficher les exécutions d’activité** lien dans le **Actions** colonne. 

    ![Actualiser la liste et sélectionnez Afficher les exécutions d’activité](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Il n’existe qu’une seule activité (activité de copie) dans le pipeline, vous voyez donc qu’une seule entrée. Pour plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. 

    ![Activité de copie est dans un pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Car il n’existe aucun fichier dans le **source** conteneur dans votre compte de stockage d’objets Blob, vous ne verrez pas de n’importe quel fichier copié dans le **destination** conteneur dans votre compte de stockage d’objets Blob.
    
    ![Aucun fichier dans le conteneur source ou un conteneur de destination](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Créez un fichier texte vide et nommez-la **file1.txt**. Téléchargez ce fichier texte vers le **source** conteneur dans votre compte de stockage. Vous pouvez utiliser différents outils pour effectuer ces tâches, comme l’[Explorateur Stockage Azure](https://storageexplorer.com/).   

    ![Créer des file1.txt et charger dans le conteneur source](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Pour revenir à la **exécutions du Pipeline** afficher, sélectionnez **toutes les exécutions de Pipeline**et attendez que le même pipeline soient déclenchées à nouveau automatiquement.  

    ![Sélectionnez toutes les exécutions de Pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Sélectionnez **vue activité exécutée** pour le deuxième pipeline exécuté lorsque vous le consultez. Ensuite, passez en revue les détails de la même façon que vous l’avez fait pour la première exécution de pipeline.  

    ![Sélectionnez la vue de l’exécution d’activité et passez en revue les détails](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Ce sujet, consultez un, vous allez fichier (file1.txt) a été copié à partir de la **source** conteneur pour le **destination** conteneur de votre compte de stockage d’objets Blob.
    
    ![File1.txt a été copié à partir du conteneur source vers le conteneur de destination](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Créez un autre fichier texte vide et nommez-la **file2.txt**. Téléchargez ce fichier texte vers le **source** conteneur dans votre compte de stockage d’objets Blob.   
    
16. Répétez les étapes 13 et 14 pour ce second fichier texte. Vous verrez que seul le nouveau fichier (file2.txt) a été copié à partir de la **source** conteneur pour le **destination** conteneur de votre compte de stockage dans la prochaine exécution du pipeline.  
    
    ![File2.txt a été copié à partir du conteneur source vers le conteneur de destination](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Vous pouvez également vérifier cela à l’aide de [Azure Storage Explorer](https://storageexplorer.com/) pour analyser les fichiers.
    
    ![Analyser les fichiers à l’aide de l’Explorateur de stockage Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Étapes suivantes
Passez au didacticiel suivant pour en savoir plus sur la transformation des données à l’aide d’un cluster Apache Spark sur Azure :

> [!div class="nextstepaction"]
>[Transformer des données dans le cloud à l’aide d’un cluster Apache Spark](tutorial-transform-data-spark-portal.md)