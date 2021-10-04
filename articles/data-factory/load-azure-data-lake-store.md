---
title: Charger des données dans Azure Data Lake Storage Gen1
description: Utiliser Azure Data Factory pour copier des données dans Azure Data Lake Storage Gen1
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 5a82f60bfbf97ebc0de6f1ff9f8214c995775bf2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819928"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Charger des données dans Azure Data Lake Storage Gen1 à l’aide d’Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (connu préalablement sous le nom Azure Data Lake Store) est un référentiel d’entreprise à très grande échelle pour les charges de travail d’analyse du Big Data. Data Lake Storage Gen1 vous permet de capturer des données de toute taille, de tout type et dont les vitesses d’ingestion sont variées. Les données sont capturées à un emplacement unique, à des fins d’analytique opérationnelle et exploratoire.

Azure Data Factory est un service informatique d’intégration de données informatique intégralement managé. Vous pouvez utiliser le service pour remplir le lac de données avec les données de votre système existant et gagner du temps lors de la création de vos solutions d’analyse.

Azure Data Factory offre les avantages suivants pour le chargement des données dans Data Lake Storage Gen1 :

* **Facilité de configuration** : assistant intuitif en 5 étapes. Aucun script nécessaire.
* **Prise en charge étendue du magasin de données** : prise en charge intégrée d’un ensemble complet de magasins de données locaux et dans le cloud. Pour une liste détaillée, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sécurité et conformité** : les données sont transférées via HTTPS ou ExpressRoute. La présence globale du service garantit que vos données ne quittent jamais les limites géographiques.
* **Hautes performances** : la vitesse de chargement des données dans Data Lake Storage Gen1 peut atteindre 1 Gbit/s. Pour en savoir plus, voir [Performances de l’activité de copie](copy-activity-performance.md).

Cet article explique comment utiliser l’outil de copie de données Data Factory pour _charger les données d’Amazon S3 dans Data Lake Storage Gen1_. Vous pouvez procéder de même pour copier des données à partir d’autres types de banques de données.

> [!NOTE]
> Pour en savoir plus, consultez [Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l’aide d’Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Compte Data Lake Storage Gen1 : si vous n’avez pas de compte Data Lake Storage Gen1, consultez les instructions décrites dans la section [Créer un compte Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3 : Cet article explique comment copier des données à partir d’Amazon S3. Vous pouvez utiliser d’autres magasins de données en procédant de la même façon.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Analytics** > **Data Factory** :
   
   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png" alt-text="Sélection de Data Factory dans le volet &quot;Nouveau&quot;":::

2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs des champs qui apparaissent dans l’image suivante : 
      
   :::image type="content" source="./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png" alt-text="Page Nouvelle fabrique de données":::
 
    * **Name** : Entrez un nom global unique pour votre fabrique de données Azure. Si l’erreur « Le nom de fabrique de données \"LoadADLSG1Demo\" n’est pas disponible » apparaît, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Essayez à nouveau de créer la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
    * **Abonnement**: Sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. 
    * **Groupe de ressources** : Sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez l’option **Créer** et entrez le nom d’un groupe de ressources. Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).  
    * **Version** : Sélectionnez **V2**.
    * **Emplacement** : Sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements et régions. Ces magasins de données incluent Azure Data Lake Storage Gen1, Stockage Azure, Azure SQL Database, etc.

3. Sélectionnez **Create** (Créer).
4. Une fois la création terminée, accédez à votre fabrique de données. La page d’accueil **Data Factory** devrait s’afficher comme dans l’image suivante : 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Page d’accueil d’Azure Data Factory, avec la mosaïque Ouvrir Azure Data Factory Studio.":::

   Sélectionnez **Ouvrir** dans la mosaïque **Ouvrir Azure Data Factory Studio** pour lancer l’application d’intégration de données dans un onglet distinct.

## <a name="load-data-into-data-lake-storage-gen1"></a>Charger des données dans Data Lake Storage Gen1

1. Sur la page d’accueil, sélectionnez la mosaïque **Ingérer** pour lancer l’outil Copier des données : 

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Capture d’écran montrant la page d’accueil ADF.":::
2. Dans la page **Propriétés**, spécifiez **CopyFromAmazonS3ToADLS** dans le champ **Nom de tâche**, puis cliquez sur **Suivant** :

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png" alt-text="Page Propriétés":::
3. Dans la page **Banques de données sources**, cliquez sur **+ Créer une connexion** :

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/source-data-store-page.png" alt-text="Page Magasin de données sources":::
    
    Sélectionnez **Amazon S3**, puis sélectionnez **Continuer**.
    
    :::image type="content" source="./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png" alt-text="Page Banque de données sources s3":::
    
4. Sur la page **Spécifier la connexion Amazon S3**, procédez comme suit : 
   1. Spécifiez la valeur du champ **ID de clé d’accès**.
   2. Spécifiez la valeur **Clé d’accès secrète**.
   3. Sélectionnez **Terminer**.
   
      :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png" alt-text="Capture d’écran montrant le volet Nouveau service lié dans lequel vous pouvez entrer des valeurs.":::
   
   4. Vous voyez une nouvelle connexion. Sélectionnez **Suivant**.
   
   :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png" alt-text="Capture d’écran montrant votre nouvelle connexion.":::
   
5. Sur la page de **sélection du fichier ou dossier d’entrée**, accédez au dossier et au fichier sur lesquels effectuer la copie. Sélectionnez le dossier ou le fichier ; cliquez sur **Choisir**, puis sur **Suivant** :

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/choose-input-folder.png" alt-text="Choisir le fichier ou le dossier d’entrée":::

6. Choisissez le comportement de copie en sélectionnant les options de **copie récursive des fichiers** et de **copie binaire** (copie des fichiers en l’état). Sélectionnez **Suivant** :

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-binary-copy.png" alt-text="Capture d’écran représentant la page Choisir le fichier ou le dossier d’entrée sur laquelle vous pouvez sélectionner Copier le fichier de façon récursive et Copie binaire.":::
    
7. Sur la page **Banque de données de destination**, cliquez sur **+ Créer une connexion**, puis sélectionnez **Azure Data Lake Storage Gen1** et sélectionnez **Continuer** :

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png" alt-text="Page Magasin de données de destination":::

8. Sur la page **New Linked Service (Azure Data Lake Storage Gen1)** (Nouveau service lié (Azure Data Lake Storage Gen1)), effectuez les étapes suivantes : 

   1. Sélectionnez votre compte Data Lake Storage Gen1 pour **Nom du compte Data Lake Store**.
   2. Spécifiez le **Locataire**, puis cliquez sur Terminer.
   3. Sélectionnez **Suivant**.
   
   > [!IMPORTANT]
   > Dans cette procédure pas à pas, vous utilisez une identité managée pour les ressources Azure, afin d’authentifier votre compte Data Lake Storage Gen1. Veillez à accorder à la fonctionnalité MSI les autorisations appropriées dans Data Lake Storage Gen1, en suivant [ces instructions](connector-azure-data-lake-store.md#managed-identity).
   
   :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-adls.png" alt-text="Spécifier le compte Data Lake Storage Gen1":::
9. Dans la page de **sélection du fichier ou dossier de sortie**, saisissez **copyfroms3** dans le champ du nom du dossier de sortie, puis sélectionnez **Suivant** : 

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/specify-adls-path.png" alt-text="Capture d’écran représentant le chemin d’accès au dossier que vous entrez.":::

10. Sur la page **Paramètres**, cliquez sur **Suivant** :

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/copy-settings.png" alt-text="Page de paramètres":::
11. Dans la page **Résumé**, vérifiez les paramètres, puis cliquez sur **Suivant** :

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/copy-summary.png" alt-text="Page de résumé":::
12. Dans la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche) :

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/deployment-page.png" alt-text="Page Déploiement":::
13. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution de l’activité et de réexécuter le pipeline :

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png" alt-text="Superviser les exécutions de pipelines":::
14. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Il n’y a qu’une seule activité (activité de copie) dans le pipeline ; vous ne voyez donc qu’une seule entrée. Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Pipelines** affiché en haut de la fenêtre. Sélectionnez **Actualiser** pour actualiser la liste. 

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png" alt-text="Surveiller des exécutions d’activités":::

15. Pour surveiller l’exécution de chaque activité de copie, cliquez sur le lien **Détails** sous **Actions** dans la page de surveillance des activités. Vous pouvez suivre les informations détaillées comme le volume de données copiées à partir de la source dans le récepteur, le débit des données, les étapes d’exécution avec une durée correspondante et les configurations utilisées :

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png" alt-text="Détails du suivi de l'exécution des activités":::

16. Vérifiez que les données sont copiées dans votre compte Data Lake Store Gen1 : 

    :::image type="content" source="./media/load-data-into-azure-data-lake-store/adls-copy-result.png" alt-text="Vérifier la sortie Data Lake Storage Gen1":::

## <a name="next-steps"></a>Étapes suivantes

Lisez l’article suivant pour en savoir plus sur la prise en charge de Data Lake Storage Gen1 : 

> [!div class="nextstepaction"]
>[Connecteur Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
