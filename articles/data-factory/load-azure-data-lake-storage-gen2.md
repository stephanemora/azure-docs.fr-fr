---
title: Charger des données dans Azure Data Lake Storage Gen2
description: Utiliser Azure Data Factory pour copier des données dans Azure Data Lake Storage Gen2
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/22/2021
ms.openlocfilehash: 4ed42f5ea6aa5acda8234aab31b26c99057ed6b9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819993"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Charger des données dans Azure Data Lake Storage Gen2 avec Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 est un ensemble de fonctionnalités dédiées à l'analytique du Big Data et intégrées au service [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md). Il vous permet d’interagir avec vos données selon les deux paradigmes que sont le système de fichiers et le stockage d’objets.

Azure Data Factory (ADF) est un service d’intégration de données informatiques complètement managé. Vous pouvez utiliser le service pour remplir le lac avec des données provenant d’un ensemble étendu de banques de données locales et cloud lors de la création de vos solutions d’analytique. Pour une liste détaillée des connecteurs pris en charge, consultez le tableau de [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre une solution de déplacement des données managées qui est évolutive. En raison de l’architecture évolutive d’Azure Data Factory elle peut ingérer des données à un débit élevé. Pour en savoir plus, voir [Performances de l’activité de copie](copy-activity-performance.md).

Cet article vous explique comment utiliser l’outil de copie de données de Data Factory pour charger des données depuis le _service ’Amazon Web Services S3_ dans _Azure Data Lake Store Gen2_. Vous pouvez procéder de même pour copier des données à partir d’autres types de banques de données.

>[!TIP]
>Pour copier des données à partir d’Azure Data Lake Storage Gen1 dans Gen2, reportez-vous à [cette procédure pas à pas spécifique](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Compte de stockage Azure avec Data Lake Storage Gen2 activé : Si vous n’avez pas de compte de stockage, [créez-en un](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Compte AWS avec un compartiment S3 qui contient des données : Cet article explique comment copier des données à partir d’Amazon S3. Vous pouvez utiliser d’autres magasins de données en procédant de la même façon.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Intégration** > **Data Factory** :
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Sélection de Data Factory dans le volet &quot;Nouveau&quot;":::

2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs des champs suivants :
 
    * **Name** : Entrez un nom global unique pour votre fabrique de données Azure. Si l’erreur « Le nom de fabrique de données *NomDeVotreFabriqueDeDonnées* n’est pas disponible » apparaît, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Essayez à nouveau de créer la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
    * **Abonnement**: Sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. 
    * **Groupe de ressources** : Sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez l’option **Créer** et entrez le nom d’un groupe de ressources. Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).  
    * **Version** : Sélectionnez **V2**.
    * **Emplacement** : Sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements et régions. 

3. Sélectionnez **Create** (Créer).

4. Une fois la création terminée, accédez à votre fabrique de données. La page d’accueil **Data Factory** devrait s’afficher comme dans l’image suivante : 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Page d’accueil d’Azure Data Factory, avec la mosaïque Ouvrir Azure Data Factory Studio.":::

   Sélectionnez **Ouvrir** sur la vignette **Ouvrir Azure Data Factory Studio** pour lancer l’application d’intégration de données dans un onglet distinct.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Charger des données dans Azure Data Lake Storage Gen2

1. Dans la page d’accueil d’Azure Data Factory, sélectionnez la vignette **Ingérer** pour lancer l’outil Copier des données.

2. Dans la page **Propriétés**, choisissez **Tâche de copie intégrée** sous **Type de tâche**, et **Exécuter une fois maintenant** sous **Cadence ou planification des tâches**, puis sélectionnez **Suivant**.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png" alt-text="Page Propriétés":::
3. Dans la page **Banque de données source**, effectuez les étapes suivantes :
    1. Sélectionnez **+ Nouvelle connexion**. Sélectionnez **Amazon S3** dans la galerie des connecteurs, puis sélectionnez **Continuer**.
    
        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png" alt-text="Page Banque de données sources s3":::
    
    1. Dans la page **Nouvelle connexion (Amazon S3)** , procédez comme suit :

        1. Spécifiez la valeur du champ **ID de clé d’accès**.
        1. Spécifiez la valeur **Clé d’accès secrète**.
        1. Sélectionnez **Tester la connexion** pour valider les paramètres, puis sélectionnez **Créer**.
    
          :::image type="content" source="./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png" alt-text="Spécification du compte Amazon S3":::

    1. Sur la page **Banque de données sources**, assurez-vous que la connexion Amazon S3 nouvellement créée est sélectionnée dans le bloc **Connexion**. 
    1. Dans la section **Fichier ou dossier**, accédez au dossier ou au fichier que vous voulez copier. Sélectionnez le dossier ou le fichier, puis sélectionnez **OK**.
    1. Spécifiez le comportement de copie en cochant les options **Copier les fichiers de façon récursive** et **Copie binaire**. Sélectionnez **Suivant**.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/source-data-store.png" alt-text="Capture d’écran montrant la page Banque de données source.":::
    
4. Dans la page **Banque de données de destination**, suivez les étapes suivantes.
    1. Sélectionnez **+ Nouvelle connexion**, **Azure Data Lake Storage Gen2**, puis **Continuer**.

        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png" alt-text="Page Magasin de données de destination":::
    
    1. Dans la page **Nouvelle connexion (Azure Data Lake Storage Gen2)** , sélectionnez votre compte Data Lake Storage Gen2 dans la liste déroulante « Nom du compte de stockage », puis sélectionnez **Créer** pour créer la connexion. 

        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png" alt-text="Indiquer un compte Azure Data Lake Storage Gen2":::

    1. Dans la page **Magasin de données de destination**, sélectionnez la connexion nouvellement créée dans le bloc **Connexion**. Ensuite, sous **Chemin d’accès du dossier**, entrez **copyfroms3** comme nom du dossier de sortie, puis sélectionnez **Suivant**. ADF crée le système de fichiers ADLS Gen2 et les sous-dossiers correspondants pendant la copie s’ils n’existent pas.

        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/destiantion-data-store.png" alt-text="Capture d’écran montrant la page Magasin de données de destination.":::   
    
5. Dans la page **Paramètres**, spécifiez **CopyFromAmazonS3ToADLS** pour le champ **Nom de tâche**, puis sélectionnez **Suivant** pour utiliser les paramètres par défaut.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/copy-settings.png" alt-text="Page de paramètres":::

6. Dans la page **Résumé**, vérifiez les paramètres, puis cliquez sur **Suivant**.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/copy-summary.png" alt-text="Page de résumé":::

7. Sur la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche). 
 
8. Lorsque l’exécution du pipeline se termine avec succès, vous voyez une exécution de pipeline qui est déclenchée par un déclencheur manuel. Vous pouvez utiliser les liens sous la colonne **Nom du pipeline** pour voir les détails de l’activité et réexécuter le pipeline.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png" alt-text="Superviser les exécutions de pipelines":::

9. Pour voir les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **CopyFromAmazonS3ToADLS** sous la colonne **Nom du pipeline**. Pour plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Nom de l’activité**. Vous pouvez suivre les informations détaillées comme le volume de données copiées à partir de la source dans le récepteur, le débit des données, les étapes d’exécution avec une durée correspondante et la configuration utilisée.
 
    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png" alt-text="Surveiller des exécutions d’activités":::
    
    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png" alt-text="Détails du suivi de l'exécution des activités":::

10. Sélectionnez **Actualiser** pour actualiser l’affichage. Sélectionnez **Toutes les exécutions de pipelines** en haut pour revenir à l’affichage « Exécutions de pipeline ».

11. Vérifiez que les données sont copiées dans votre compte Azure Data Lake Store Gen2 :

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble des activités de copie](copy-activity-overview.md)
* [Connecteur Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
