---
title: Copier les nouveaux fichiers de façon incrémentielle en fonction du nom de fichier partitionné
description: Créez une fabrique de données Azure, puis utilisez l’outil Copier des données afin de charger de nouveaux fichiers de façon incrémentielle uniquement sur la base du nom de fichier partitionné dans le temps.
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
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: 746b5cbcc58f6c722623446227417e6c94dd0a80
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217445"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Copier de nouveaux fichiers de façon incrémentielle sur la base du nom de fichier partitionné dans le temps à l’aide de l’outil Copier des données

Dans ce didacticiel, vous utilisez le portail Azure pour créer une fabrique de données. Ensuite, vous utilisez l’outil Copier des données pour créer un pipeline qui copie de façon incrémentielle de nouveaux fichiers sur la base du nom de fichier partitionné dans le temps de Stockage Blob Azure vers Stockage Blob Azure. 

> [!NOTE]
> Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Dans ce tutoriel, vous effectuerez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte de stockage Azure** : Utilisez le stockage d’objets blob comme magasins de données _source_ et _récepteur_. Si vous ne possédez pas de compte de stockage Azure, consultez les instructions dans [Créer un compte de stockage](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Créer deux conteneurs dans le stockage d’objets blob

Préparez votre stockage d’objets blob pour ce tutoriel en effectuant les étapes suivantes.

1. Créez un conteneur nommé **source**.  Créez un chemin de dossier **2019/02/26/14** dans votre conteneur. Créez un fichier texte vide et nommez-le **file1.txt**. Chargez le fichier file1.txt vers le chemin de dossier **source/2019/02/26/14** dans votre compte de stockage.  Vous pouvez utiliser différents outils pour effectuer ces tâches, comme l’[Explorateur Stockage Azure](https://storageexplorer.com/).
    
    ![Charger des fichiers](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Ajustez le nom du dossier en fonction de votre heure UTC.  Par exemple, si l’heure UTC actuelle est 14h03 le 26 février 2019, vous pouvez créer le chemin de dossier **source/2019/02/26/14/** d’après la règle **source/{Année}/{Mois}/{Jour}/{Heure}/** .

2. Créez un conteneur nommé **destination**. Vous pouvez utiliser différents outils pour effectuer ces tâches, comme l’[Explorateur Stockage Azure](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Données + Analytique** > **Data Factory** : 
   
   ![Sélection Data Factory dans le volet « Nouveau »](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**. 
    
    Le nom de votre fabrique de données doit être un _nom global unique_. Vous pouvez recevoir le message d’erreur suivant :
   
   ![Message d’erreur de nouvelle fabrique de données](./media/doc-common-process/name-not-available-error.png)
   
   Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_ **ADFTutorialDataFactory**. Pour savoir comment nommer les artefacts Data Factory, consultez la rubrique [Data Factory - Règles d'affectation des noms](naming-rules.md).
3. Sélectionnez l’**abonnement** Azure dans lequel vous créez la nouvelle fabrique de données. 
4. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
     
    a. Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    b. Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).

5. Sous **Version**, sélectionnez **V2** pour la version.
6. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (tels que le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par votre fabrique de données peuvent se trouver dans d’autres emplacements et régions.
7. Sélectionnez **Épingler au tableau de bord**. 
8. Sélectionnez **Create** (Créer).
9. Sur le tableau de bord, la vignette **Déploiement de Data Factory** affiche l’état du processus.

    ![Vignette Déploiement de Data Factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Une fois la création terminée, la page d’accueil **Data Factory** s’affiche.
   
    ![Page d’accueil Data Factory](./media/doc-common-process/data-factory-home-page.png)
11. Pour lancer l’interface utilisateur d’Azure Data Factory dans un onglet séparé, cliquez sur la vignette **Créer et surveiller**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utiliser l’outil Copier les données pour créer un pipeline

1. Dans la page **Prise en main**, sélectionnez la vignette **Copier des données** pour démarrer l’outil Copier des données. 

   ![Vignette de l’outil Copier les données](./media/doc-common-process/get-started-page.png)
   
2. Dans la page **Propriétés**, effectuez les opérations suivantes :

    a. Sous **Nom de la tâche**, entrez **DeltaCopyFromBlobPipeline**.

    b. Sous **Cadence ou planification des tâches**, sélectionnez **Exécuter régulièrement selon la planification**.

    c. Sous **Type de déclencheur**, sélectionnez **Fenêtre bascule**.
    
    d. Sous **Périodicité**, entrez **1 Heure(s)** . 
    
    e. Sélectionnez **Suivant**. 
    
    L’interface utilisateur de Data Factory crée un pipeline avec le nom spécifié. 

    ![Page Propriétés](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Sur la page **Banque de données source**, procédez comme suit :

    a. Cliquez sur **+ Créer une connexion** pour ajouter une connexion.

    ![Page Magasin de données sources](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Sélectionnez **Stockage Blob Azure** à partir de la galerie, puis cliquez sur **Continuer**.

    ![Page Magasin de données sources](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. Dans la page **Nouveau service lié**, sélectionnez votre compte de stockage dans la liste **Nom du compte de stockage**, puis cliquez sur **Terminer**.
    
    ![Page Magasin de données sources](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Sélectionnez le nouveau service lié, puis cliquez sur **Suivant**. 
    
   ![Page Magasin de données sources](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit :
    
    a. Recherchez et sélectionnez le conteneur **source**, puis sélectionnez **Choisir**.
    
    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. Sous **Comportement de chargement de fichier**, sélectionnez **Chargement incrémentiel : noms de fichiers/dossiers partitionnés dans le temps**.
    
    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Écrivez le chemin de dossier dynamique **source/{Année}/{Mois}/{Jour}/{Heure}/** , puis modifiez le format comme suit :
    
    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Cochez la case **Copie binaire** et cliquez sur **Suivant**.
    
    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Dans la page **Magasin de données de destination**, sélectionnez **AzureBlobStorage**, qui est le même compte de stockage que le magasin de source de données, puis cliquez sur **Suivant**.

    ![Page Magasin de données de destination](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Dans la page **Choisir le fichier ou le dossier de sortie**, effectuez les étapes suivantes :
    
    a. Recherchez et sélectionnez le dossier **destination**, puis cliquez sur **Choisir**.
    
    ![Choisir le fichier ou le dossier de sortie](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Écrivez le chemin de dossier dynamique **source/{Année}/{Mois}/{Jour}/{Heure}/** , puis modifiez le format comme suit :
    
    ![Choisir le fichier ou le dossier de sortie](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Cliquez sur **Suivant**.
    
    ![Choisir le fichier ou le dossier de sortie](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Sur la page **Paramètres**, cliquez sur **Suivant**. 

    ![Page Paramètres](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Sur la page **Résumé**, vérifiez les paramètres, puis sélectionnez **Suivant**.

    ![Page de résumé](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Sur la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche).
    ![Page Déploiement](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement.  Vous devez attendre l’exécution du pipeline quand elle est déclenchée automatiquement (après environ une heure).  Quand il s’exécute, la colonne **Actions** comprend des liens permettant d’afficher les détails de l’exécution de l’activité et de réexécuter le pipeline : Sélectionnez **Actualiser** pour actualiser la liste, puis sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. 

    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Il n’y a qu’une seule activité (activité de copie) dans le pipeline ; vous ne voyez donc qu’une seule entrée. Vous pouvez constater que le fichier source (file1.txt) a été copié de **source/2019/02/26/14/** vers **destination/2019/02/26/14/** avec le même nom de fichier.  

    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Vous pouvez également vérifier cela à l’aide de l’Explorateur Stockage Azure (https://storageexplorer.com/) pour analyser les fichiers.
    
    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Créez un autre fichier texte vide nommé **file2.txt**. Chargez le fichier file2.txt vers le chemin de dossier **source/2019/02/26/15** dans votre compte de stockage.   Vous pouvez utiliser différents outils pour effectuer ces tâches, comme l’[Explorateur Stockage Azure](https://storageexplorer.com/).   
    
    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Vous serez peut-être informé qu’un nouveau chemin de dossier doit être créé. Ajustez le nom du dossier en fonction de votre heure UTC.  Par exemple, si l’heure UTC actuelle est 15h20 le 26 février 2019, vous pouvez créer le chemin de dossier **source/2019/02/26/15/** d’après la règle **{Année}/{Mois}/{Jour}/{Heure}/** .
    
13. Pour revenir à la vue **Exécutions de pipelines**, sélectionnez **Toutes les exécutions de pipelines** et attendez que le même pipeline soit redéclenché automatiquement après une heure.  

    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Sélectionnez **Afficher l’exécution d’activité** pour la deuxième exécution d’activité quand elle est apparaît, puis appliquez la même procédure pour examiner les détails.  

    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Vous pouvez constater que le fichier source (file2.txt) a été copié de **source/2019/02/26/15/** vers **destination/2019/02/26/15/** avec le même nom de fichier.
    
    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Vous pouvez également vérifier cela à l’aide de l’Explorateur Stockage Azure (https://storageexplorer.com/) pour analyser les fichiers dans le conteneur **destination**.
    
    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Étapes suivantes
Passez au tutoriel suivant pour en savoir plus sur la transformation des données en utilisant un cluster Spark sur Azure :

> [!div class="nextstepaction"]
>[Transformer des données en utilisant un cluster Spark dans le cloud](tutorial-transform-data-spark-portal.md)