---
title: À l’aide d’Azure Data Factory pour copier les nouveaux fichiers de façon incrémentielle basées uniquement sur le nom du fichier partitionnée temps | Microsoft Docs
description: Créer une fabrique de données Azure, puis utilisez l’outil Copier des données à charger les nouveaux fichiers basées uniquement sur le nom de fichier partitionnée de temps de façon incrémentielle.
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
ms.openlocfilehash: df1542d6d20120a9b1e087fadf3743479ecebf07
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533831"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Copier les nouveaux fichiers basés sur le nom de fichier partitionnée de temps à l’aide de l’outil Copier des données de façon incrémentielle

Dans ce didacticiel, vous utilisez le portail Azure pour créer une fabrique de données. Ensuite, vous utilisez l’outil Copier des données pour créer un pipeline qui copie les nouveaux fichiers basés sur nom de fichier partitionnée de temps à partir du stockage d’objets Blob Azure vers stockage Blob Azure de façon incrémentielle. 

> [!NOTE]
> Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Dans ce tutoriel, vous effectuerez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>Conditions préalables

* **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte de stockage Azure** : Utilisation du stockage Blob en tant que le _source_ et _récepteur_ magasin de données. Si vous ne possédez pas de compte de stockage Azure, consultez les instructions dans [Créer un compte de stockage](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Créer deux conteneurs dans le stockage Blob

Préparez votre stockage d’objets Blob pour le didacticiel effectuant les étapes suivantes.

1. Créer un conteneur nommé **source**.  Créer un chemin d’accès de dossier en tant que **2019/02/26/14** dans votre conteneur. Créer un fichier texte vide et nommez-le **file1.txt**. Télécharger le file1.txt vers le chemin d’accès du dossier **source/2019/02/26/14** dans votre compte de stockage.  Vous pouvez utiliser différents outils pour effectuer ces tâches, comme l’[Explorateur Stockage Azure](https://storageexplorer.com/).
    
    ![Charger des fichiers](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Veuillez modifier le nom du dossier avec votre heure UTC.  Par exemple, si l’heure UTC actuelle est 14 h 03 le 26 février 2019, vous pouvez créer le chemin d’accès de dossier en tant que **source/2019/02/26/14/** par la règle de **source / {Year} / {Month} / {Day} / {Hour} /**.

2. Créer un conteneur nommé **destination**. Vous pouvez utiliser différents outils pour effectuer ces tâches, comme l’[Explorateur Stockage Azure](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu sur la gauche, sélectionnez **+ Nouveau** > **Données + Analytique** > **Data Factory** : 
   
   ![Création de la nouvelle fabrique de données](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**. 
      
    ![Nouvelle fabrique de données](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
    
    Le nom de votre fabrique de données doit être un _nom global unique_. Vous pouvez recevoir le message d’erreur suivant :
   
   ![Message d’erreur de nouvelle fabrique de données](./media/tutorial-copy-data-tool/name-not-available-error.png)
   
   Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Pour savoir comment nommer les artefacts Data Factory, consultez la rubrique [Data Factory - Règles d'affectation des noms](naming-rules.md).
3. Sélectionnez l’**abonnement** Azure dans lequel vous créez la nouvelle fabrique de données. 
4. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
     
    a. Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    b. Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).

5. Sous **Version**, sélectionnez **V2** pour la version.
6. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (tels que le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par votre fabrique de données peuvent se trouver dans d’autres emplacements et régions.
7. Sélectionnez **Épingler au tableau de bord**. 
8. Sélectionnez **Créer**.
9. Sur le tableau de bord, la vignette **Déploiement de Data Factory** affiche l’état du processus.

    ![Vignette Déploiement de Data Factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Une fois la création terminée, la page d’accueil **Data Factory** s’affiche.
   
    ![Page d’accueil Data Factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Pour lancer l’interface utilisateur d’Azure Data Factory dans un onglet séparé, cliquez sur la vignette **Créer et surveiller**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utiliser l’outil Copier les données pour créer un pipeline

1. Sur le **prise en main** page, sélectionnez le **copier des données** title pour lancer l’outil Copier des données. 

   ![Vignette de l’outil Copier les données](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Sur le **propriétés** page, procédez comme suit :

    a. Sous **nom_tâche**, entrez **DeltaCopyFromBlobPipeline**.

    b. Sous **cadence de tâche ou de la planification de la tâche**, sélectionnez **exécuter régulièrement selon planification**.

    c. Sous **déclencher type**, sélectionnez **fenêtre bascule**.
    
    d. Sous **périodicité**, entrez **1 heure (s)**. 
    
    e. Sélectionnez **Suivant**. 
    
    L’interface utilisateur de Data Factory crée un pipeline avec le nom spécifié. 

    ![Page Propriétés](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Sur la page **Banque de données source**, procédez comme suit :

    a. Cliquez sur **+ créer une nouvelle connexion**, pour ajouter une connexion.

    ![Page Magasin de données sources](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Sélectionnez **stockage Blob Azure** dans la galerie, puis cliquez sur **continuer**.

    ![Page Magasin de données sources](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. Sur le **nouveau Service lié** , sélectionnez votre compte de stockage à partir de la **nom de compte de stockage** liste, puis cliquez sur **Terminer**.
    
    ![Page Magasin de données sources](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Sélectionnez le service lié qui vient d’être créé, puis cliquez sur **suivant**. 
    
   ![Page Magasin de données sources](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit :
    
    a. Recherchez et sélectionnez le **source** conteneur, puis sélectionnez **choisir**.
    
    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. Sous **le comportement de chargement de fichier**, sélectionnez **chargement incrémentiel : les noms de fichier/dossier partitionnée de temps**.
    
    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Écrire le chemin d’accès de dossier dynamique en tant que **source / {year} / {month} / {day} / {hour} /**, puis modifiez le format comme suit :
    
    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Vérifiez **copie binaire** et cliquez sur **suivant**.
    
    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Sur le **magasin de données de Destination** page, sélectionnez le **AzureBlobStorage**, qui est le même stockage compte comme magasin de source de données, puis cliquez sur **suivant**.

    ![Page Magasin de données de destination](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Sur le **choisir le fichier de sortie ou le dossier** , effectuez les étapes suivantes :
    
    a. Recherchez et sélectionnez le **destination** dossier, puis cliquez sur **choisir**.
    
    ![Choisir le fichier ou le dossier de sortie](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Écrire le chemin d’accès de dossier dynamique en tant que **source / {year} / {month} / {day} / {hour} /**, puis modifiez le format comme suit :
    
    ![Choisir le fichier ou le dossier de sortie](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Cliquez sur **Suivant**.
    
    ![Choisir le fichier ou le dossier de sortie](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Sur la page **Paramètres**, cliquez sur **Suivant**. 

    ![Page Paramètres](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Sur la page **Résumé**, vérifiez les paramètres, puis sélectionnez **Suivant**.

    ![Page de résumé](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Sur la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche).
    ![Page de déploiement](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement.  Vous devez attendre l’exécution quand elle est déclenchée automatiquement du pipeline (sur après une heure).  Lorsqu’elle s’exécute, le **Actions** colonne inclut des liens pour afficher les détails de l’exécution d’activité et de réexécuter le pipeline. Sélectionnez **Actualiser** pour actualiser la liste, puis sélectionnez le **afficher les exécutions d’activité** lien dans le **Actions** colonne. 

    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Il n’y a qu’une seule activité (activité de copie) dans le pipeline ; vous ne voyez donc qu’une seule entrée. Vous pouvez voir le fichier source (file1.txt) a été copié à partir de **source/2019/02/26/14/** à **destination/2019/02/26/14/** portant le même nom de fichier.  

    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Vous pouvez également vérifier le même à l’aide de l’Explorateur de stockage Azure (https://storageexplorer.com/) pour analyser les fichiers.
    
    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Créer un autre fichier texte vide avec le nouveau nom en tant que **file2.txt**. Chargez le fichier de file2.txt dans le chemin d’accès du dossier **source/2019/02/26/15** dans votre compte de stockage.   Vous pouvez utiliser différents outils pour effectuer ces tâches, comme l’[Explorateur Stockage Azure](https://storageexplorer.com/).   
    
    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Vous pouvez être prenant en charge qu’un nouveau chemin d’accès du dossier est requis pour être créé. Veuillez modifier le nom du dossier avec votre heure UTC.  Par exemple, si l’heure UTC actuelle est de 3 h 20 26 février 2019, vous pouvez créer le chemin d’accès de dossier en tant que **source/2019/02/26/15/** par la règle de **{Year} / {Month} / {Day} / {Hour} /**.
    
13. Pour revenir à la **exécutions du Pipeline** afficher, sélectionnez **toutes les exécutions de Pipelines**et attendez que le même pipeline est déclenché à nouveau automatiquement après une heure un autre.  

    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Sélectionnez **vue activité exécutée** pour le deuxième pipeline exécuter quand il est fourni et faire de même pour consulter les détails.  

    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Vous pouvez voir le fichier source (file2.txt) a été copié à partir de **source/2019/02/26/15/** à **destination/2019/02/26/15/** portant le même nom de fichier.
    
    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Vous pouvez également vérifier le même à l’aide de l’Explorateur de stockage Azure (https://storageexplorer.com/) pour analyser les fichiers de **destination** conteneur
    
    ![Surveiller des exécutions de pipelines](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Étapes suivantes
Passez au tutoriel suivant pour en savoir plus sur la transformation des données en utilisant un cluster Spark sur Azure :

> [!div class="nextstepaction"]
>[Transformer des données en utilisant un cluster Spark dans le cloud](tutorial-transform-data-spark-portal.md)