---
title: Outil de données pour copier de façon incrémentielle les fichiers nouveaux et mis à jour
description: Créez une fabrique de données Azure, puis utilisez l’outil Copier des données afin de charger de nouveaux fichiers de façon incrémentielle sur la base de LastModifiedDate.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 743f9dd8f7998178a75d716f4da22efee2b3bc79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131040"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copier les fichiers nouveaux et modifiés selon LastModifiedDate à l’aide de l’outil Copier des données de façon incrémentielle

Dans ce didacticiel, vous utilisez le portail Azure pour créer une fabrique de données. Ensuite, vous utilisez l’outil Copier des données pour créer un pipeline qui copie de façon incrémentielle des nouveaux fichiers et des fichiers modifiés sur la base de leur **LastModifiedDate** du Stockage Blob Azure vers Stockage Blob Azure.

Ce faisant, ADF analyse tous les fichiers du magasin source, applique le filtre de fichier par date de dernière modification et copie le nouveau fichier mis à jour uniquement depuis son dernier emplacement dans le magasin de destination.  Notez que si vous laissez ADF analyser d'importants volumes de fichiers, mais ne copiez que quelques fichiers dans la destination, cette opération prendra un certain temps car l'analyse de fichiers est chronophage.   

> [!NOTE]
> Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Dans ce didacticiel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte de stockage Azure** : Utilisez le stockage d’objets blob comme magasins de données _source_ et _récepteur_. Si vous ne possédez pas de compte de stockage Azure, consultez les instructions dans [Créer un compte de stockage](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Créer deux conteneurs dans le stockage d’objets blob

Préparez votre stockage d’objets blob pour ce tutoriel en effectuant les étapes suivantes.

1. Créez un conteneur nommé **source**. Vous pouvez utiliser différents outils pour effectuer cette tâche, comme l’[Explorateur Stockage Azure](https://storageexplorer.com/).

2. Créez un conteneur nommé **destination**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Données + Analytique** > **Data Factory** :

   ![Sélection Data Factory dans le volet « Nouveau »](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.

   Le nom de votre fabrique de données doit être un _nom global unique_. Vous pouvez recevoir le message d’erreur suivant :

   ![Message d’erreur de nouvelle fabrique de données](./media/doc-common-process/name-not-available-error.png)

   Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_ **ADFTutorialDataFactory**. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
3. Sélectionnez l’**abonnement** Azure dans lequel vous créez la nouvelle fabrique de données.
4. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :

    * Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    * Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).

5. Sous **Version**, sélectionnez **V2**.
6. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (tels que le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) qu’utilise votre fabrique de données peuvent se trouver dans d’autres emplacements et régions.
8. Sélectionnez **Create** (Créer).
9. Une fois la création terminée, la page d’accueil **Data Factory** s’affiche.
10. Pour ouvrir l’interface utilisateur d’Azure Data Factory dans un onglet séparé, cliquez sur la vignette **Créer et surveiller**.

    ![Page d’accueil Data Factory](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utiliser l’outil Copier les données pour créer un pipeline

1. Dans la page **Prise en main**, sélectionnez la vignette **Copier des données** pour ouvrir l’outil Copier des données.

   ![Vignette de l’outil Copier les données](./media/doc-common-process/get-started-page.png)

2. Dans la page **Propriétés**, effectuez les opérations suivantes :

    a. Sous **Nom de la tâche**, entrez **DeltaCopyFromBlobPipeline**.

    b. Sous **Cadence des tâches** ou **Planification des tâches**, sélectionnez **Exécuter régulièrement selon la planification**.

    c. Sous **Type de déclencheur**, sélectionnez **Fenêtre bascule**.

    d. Sous **Périodicité**, entrez **15 Minute(s)** .

    e. Sélectionnez **Suivant**.

    L’interface utilisateur de Data Factory crée un pipeline avec le nom spécifié.

    ![Page Propriétés](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Sur la page **Banque de données source**, procédez comme suit :

    a. Sélectionnez **+ Créer une connexion** pour ajouter une connexion.

    b. Sélectionnez **Stockage Blob Azure** à partir de la galerie, puis sélectionnez **Continuer**.

    ![Page Magasin de données sources](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Dans la page **Nouveau service lié (Stockage Blob Azure)** , sélectionnez votre compte de stockage dans la liste **Nom du compte de stockage**. Testez la connexion, puis sélectionnez **Créer**.

    d. Sélectionnez le service lié nouvellement créé, puis sélectionnez **Suivant**.

   ![Page Magasin de données sources](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit :

    a. Recherchez et sélectionnez le dossier **source**, puis sélectionnez **Choisir**.

    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Sous **Comportement du chargement de fichier**, sélectionnez **Chargement incrémentiel : LastModifiedDate**.

    c. Cochez la case **Copie binaire** et sélectionnez **Suivant**.

     ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Sur la page **Banque de données de destination**, sélectionnez le stockage **AzureBlobStorage** que vous avez créé. Il s’agit du même compte de stockage que la banque de données source. Sélectionnez ensuite **Suivant**.

6. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit :

    a. Recherchez et sélectionnez le dossier **destination**, puis sélectionnez **Choisir**.

    ![Choisir le fichier ou le dossier de sortie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Sélectionnez **Suivant**.

7. Sur la page **Paramètres**, cliquez sur **Suivant**.

8. Sur la page **Résumé**, vérifiez les paramètres, puis sélectionnez **Suivant**.

    ![Page de résumé](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Sur la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche).

    ![Page Déploiement](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. L’application bascule vers l’onglet **Surveiller**. Vous voyez l’état du pipeline. Sélectionnez **Actualiser** pour actualiser la liste. Cliquez sur le lien en dessous de **Nom du pipeline** pour afficher les détails de l’exécution de l’activité ou réexécuter le pipeline. 

    ![Actualiser la liste et sélectionner Afficher les exécutions d'activités](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Il n’y a qu’une seule activité (l’activité de copie) dans le pipeline ; vous ne voyez donc qu’une seule entrée. Pour plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **NOM DE L’ACTIVITÉ**. Pour plus d’informations sur les propriétés, consultez [Vue d’ensemble de l’activité de copie](copy-activity-overview.md). 

    ![L’activité de copie se trouve dans le pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Étant donné qu’il n’existe aucun fichier dans le conteneur **source** de votre compte de stockage Blob, vous ne trouverez aucun fichier copié dans le conteneur **destination** de votre compte de stockage Blob.

    ![Aucun fichier dans le conteneur source ou destination](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Créez un fichier texte vide et nommez-le **file1.txt**. Chargez le fichier texte dans le conteneur **source** de votre compte de stockage. Vous pouvez utiliser différents outils pour effectuer ces tâches, comme l’[Explorateur Stockage Azure](https://storageexplorer.com/).

    ![Créer le fichier texte file1.txt et le charger dans le conteneur source](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Pour revenir à la vue **Exécutions de pipelines**, sélectionnez **Toutes les exécutions de pipelines** et attendez que le même pipeline soit redéclenché automatiquement.  

    ![Sélectionner Toutes les exécutions de pipelines](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Une fois la deuxième exécution du pipeline terminée, suivez les étapes mentionnées ci-dessus pour passer en revue les détails de l’exécution de l’activité.  

    Vous pouvez constater qu’un fichier texte (file1.txt) a été copié du conteneur **source** vers le conteneur **destination** de votre compte de stockage d’objets blob.

    ![Fichier file1.txt copié depuis le conteneur source vers le conteneur destination](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Créez un autre fichier texte vide et nommez-le **file2.txt**. Chargez le fichier texte dans le conteneur **source** de votre compte de stockage Blob.

16. Répérez les étapes 13 et 14 pour ce second fichier texte. Vous constatez que seul le nouveau fichier texte (file2.txt) a été copié depuis le conteneur **source** vers le conteneur **destination** de votre compte de stockage dans l’exécution du pipeline suivant.  

    Vous pouvez également vérifier cela à l’aide de l’[Explorateur Stockage Azure](https://storageexplorer.com/) pour analyser les fichiers.

    ![Analyser les fichiers avec l’Explorateur Stockage Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Étapes suivantes
Passez au tutoriel suivant pour en savoir plus sur la transformation des données en utilisant un cluster Apache Spark sur Azure :

> [!div class="nextstepaction"]
>[Transformer des données dans le cloud en utilisant un cluster Apache Spark](tutorial-transform-data-spark-portal.md)
