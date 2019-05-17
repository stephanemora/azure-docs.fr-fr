---
title: Copier les données depuis Azure Data Lake Storage Gen1 vers Gen2 avec Azure Data Factory
description: Utiliser Azure Data Factory pour copier des données depuis Azure Data Lake Storage Gen1 vers Gen2
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d6e09ec1f070f9ee0f4162524e4bd80d1f81adc3
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560653"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Copier les données depuis Azure Data Lake Storage Gen1 vers Gen2 avec Azure Data Factory

Azure Data Lake Storage Gen2 est un ensemble de fonctionnalités dédiées à l'analytique du Big Data et intégrées au service [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md). Il vous permet d’interagir avec vos données selon les deux paradigmes que sont le système de fichiers et le stockage d’objets.

Si vous utilisez actuellement Azure Data Lake Storage Gen1, vous pouvez évaluer les nouvelles capacités de Gen2 en copiant les données à partir de Data Lake Storage Gen1 vers Gen2 avec Azure Data Factory.

Azure Data Factory est un service informatique d’intégration de données intégralement géré. Vous pouvez utiliser le service pour remplir le lac avec des données provenant d’un ensemble étendu de banques de données locales et cloud lors de la création de vos solutions d’analytique. Pour une liste détaillée des connecteurs pris en charge, consultez le tableau de [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre une solution de déplacement des données managées qui est évolutive. En raison de l’architecture évolutive d’Azure Data Factory elle peut ingérer des données à un débit élevé. Pour plus d’informations, consultez [Performances de l’activité de copie](copy-activity-performance.md).

Cet article vous explique comment utiliser l’outil de copie de données de Data Factory pour copier des données depuis _Azure Data Lake Storage Gen1_ vers _Azure Data Lake Store Gen2_. Vous pouvez procéder de même pour copier des données à partir d’autres types de magasins de données.

## <a name="prerequisites"></a>Conditions préalables

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Compte Azure Data Lake Storage Gen1 contenant des données.
* Compte de stockage Azure avec Data Lake Storage Gen2 activé : Si vous n’avez pas un compte de stockage [créer un compte](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Données + Analytique** > **Data Factory** :
   
   ![Sélection Data Factory dans le volet « Nouveau »](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs des champs qui apparaissent dans l’image suivante : 
      
   ![Page Nouvelle fabrique de données](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Nom** : Entrez un nom global unique pour votre fabrique de données Azure. Si l’erreur « Le nom de fabrique de données \"LoadADLSDemo\" n’est pas disponible » apparaît, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Essayez à nouveau de créer la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
    * **Abonnement**: Sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. 
    * **Groupe de ressources** : Sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez l’option **Créer** et entrez le nom d’un groupe de ressources. Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Version** : Sélectionnez **V2**.
    * **Emplacement** : Sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements et régions. 

3. Sélectionnez **Créer**.
4. Une fois la création terminée, accédez à votre fabrique de données. La page d’accueil **Data Factory** devrait s’afficher comme dans l’image suivante : 
   
   ![Page d’accueil Data Factory](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   Sélectionnez la vignette **Créer et surveiller** pour lancer l’application d’intégration de données dans un onglet séparé.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Charger des données dans Azure Data Lake Storage Gen2

1. Dans la page **Prise en main**, sélectionnez la vignette **Copier les données** pour démarrer l’outil Copier les données : 

   ![Vignette de l’outil Copier les données](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Dans la page **Propriétés**, spécifiez **CopyFromADLSGen1ToGen2** dans le champ **Nom de tâche** et sélectionnez **Suivant** :

    ![Page Propriétés](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Dans la page **Banques de données sources**, cliquez sur **+ Créer une connexion** :

    ![Page Magasin de données sources](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
    Sélectionnez **Azure Data Lake Storage Gen1** dans la galerie des connecteurs, puis sélectionnez **Continuer**.
    
    ![Page de la banque de données sources d’Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
4. Dans la page **Spécifier la connexion Data Lake Storage Gen1**, effectuez les étapes suivantes :
   1. Sélectionnez votre Data Lake Storage Gen1 pour le nom du compte, puis spécifiez ou confirmez le **locataire**.
   2. Cliquez sur **Tester la connexion** pour vérifier les paramètres, puis sélectionnez **Terminer**.
   3. Vous voyez qu’une nouvelle connexion est créée. Sélectionnez **Suivant**.
   
   > [!IMPORTANT]
   > Dans cette procédure pas à pas, vous utilisez une identité managée pour des ressources Azure afin d’authentifier votre solution Data Lake Storage Gen1. Veillez à accorder au MSI les autorisations appropriées dans Azure Data Lake Storage Gen1, en suivant [ces instructions](connector-azure-data-lake-store.md#managed-identity).
   
   ![Indiquer un compte Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
5. Sur la page de **sélection du fichier ou dossier d’entrée**, accédez au dossier et au fichier sur lesquels effectuer la copie. Sélectionnez le dossier/fichier, puis sélectionnez **Choisir** :

    ![Choisir le fichier ou le dossier d’entrée](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. Spécifiez le comportement de copie en cochant les options **Copier les fichiers de façon récursive** et **Copie binaire**. Sélectionnez **Suivant** :

    ![Spécification du dossier de sortie](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. Sur la page **Banque de données de destination**, cliquez sur **+ Créer une connexion**, puis sélectionnez **Azure Data Lake Storage Gen2** et sélectionnez **Continuer** :

    ![Page Magasin de données de destination](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. Dans la page **Spécifier la connexion Data Lake Storage Gen2**, effectuez les étapes suivantes :

   1. Sélectionnez votre compte activé pour Data Lake Storage Gen2 dans la liste déroulante « Nom du compte de stockage ».
   2. Sélectionnez **Terminer** pour créer la connexion. Sélectionnez ensuite **Suivant**.
   
   ![Indiquer un compte Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. Dans le **choisir le fichier de sortie ou le dossier** , entrez **copyfromadlsgen1** en tant que le nom de dossier de sortie, puis sélectionnez **suivant**. ADF créera le système de fichiers ADLS Gen2 correspondante et les sous-dossiers pendant la copie s’il n’existe pas.

    ![Spécification du dossier de sortie](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. Dans la page **Paramètres**, sélectionnez **Suivant** pour utiliser les paramètres par défaut.

11. Dans la page **Résumé**, vérifiez les paramètres, puis cliquez sur **Suivant** :

    ![Page de résumé](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. Dans la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline :

    ![Page Déploiement](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution de l’activité et de réexécuter le pipeline :

    ![Surveiller des exécutions de pipelines](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Il n’y a qu’une seule activité (activité de copie) dans le pipeline ; vous ne voyez donc qu’une seule entrée. Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Pipelines** affiché en haut de la fenêtre. Sélectionnez **Actualiser** pour actualiser la liste. 

    ![Surveiller des exécutions d’activités](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. Pour surveiller les détails d’exécution de chaque activité de copie, sélectionnez le lien **Détails** (image de lunettes) sous **Actions** dans la vue de surveillance des activités. Vous pouvez suivre les informations détaillées comme le volume de données copiées à partir de la source dans le récepteur, le débit des données, les étapes d’exécution avec une durée correspondante et les configurations utilisées :

    ![Détails du suivi de l'exécution des activités](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. Vérifiez que les données sont copiées dans votre compte Azure Data Lake Store Gen2 :

## <a name="best-practices"></a>Bonnes pratiques

Pour évaluer la mise à niveau à partir d’Azure Data Lake Storage (ADLS) Gen1 vers Gen2 en général, reportez-vous à [mise à niveau de vos solutions d’analytique de big data à partir d’Azure Data Lake Storage Gen1 vers Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md). Les sections suivantes présentent les meilleures pratiques de l’utilisation d’ADF pour la mise à niveau des données à partir de la génération 1 vers Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Partition de données pour la copie des données historiques

- Si la taille totale des données dans ADLS Gen1 est inférieure à **30 to** et le nombre de fichiers est inférieure à **1 million**, vous pouvez copier toutes les données dans l’exécution d’activité copie unique.
- Si vous disposez de plus grande taille de données à copier, ou vous souhaitez que la possibilité de gérer la migration de données par lots et vérifiez chacun d’eux effectuée dans une fenêtre de temps spécifiques, il est recommandé de partitionner les données, auquel cas elle peut également réduire le risque de n’importe quel iss inattendue UE.

Nous vous recommandons d’utiliser une preuve de concept (preuve de Concept) afin de vérifier si la solution de bout en bout et de tester le débit de copie dans votre environnement. Étapes majeures de faire preuve de concept : 

1. Créer un pipeline ADF avec une seule activité de copie pour copier plusieurs téraoctets de données à partir de la génération 1 ADLS vers Gen2 ADLS pour obtenir un référentiel de performances de copie, en commençant par [unités d’intégration de données (DIUs)](copy-activity-performance.md#data-integration-units) 128. 
2. En fonction du débit de copie que vous obtenez à l’étape #1, calculer le temps estimé nécessaire pour la migration de données entier. 
3. (Facultatif) Créer une table de contrôle et définir le filtre de fichier pour partitionner les fichiers à migrer. La façon de partitionner les fichiers comme suit : 

    - Partitionnée par nom de dossier ou nom de dossier avec le filtre de caractères génériques (recommandé) 
    - Partitionnée par heure de la dernière modification du fichier 

### <a name="network-bandwidth-and-storage-io"></a>E/s du stockage et de la bande passante réseau 

Vous pouvez contrôler l’accès concurrentiel des travaux de copie ADF lire les données d’ADLS Gen1 et écrire des données dans ADLS Gen2, afin que vous puissiez gérer l’utilisation sur le stockage d’e/s pour ne pas d’impact sur le travail de l’activité normale sur ADLS Gen1 pendant la migration.

### <a name="permissions"></a>Autorisations 

Dans Data Factory, [ADLS Gen1 connecteur](connector-azure-data-lake-store.md) prend en charge de principal du Service et l’identité gérée pour les authentifications de ressources Azure ; [ADLS Gen2 connecteur](connector-azure-data-lake-storage.md) prend en charge la clé, de compte principal du Service et MSI pour les authentifications de ressources Azure. Pour rendre en mesure d’accéder à Data Factory et copie tous les fichiers/ACL que vous avez besoin, veillez à qu'accorder haut assez d’autorisations pour le compte vous fournissez en accès/lecture/écriture à tous les fichiers et définissez des ACL si vous le souhaitez. Suggérer pour lui accorder en tant que rôle de super utilisateur/propriétaire pendant la période de migration. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Conserver les ACL à partir de la génération 1 de Data Lake Storage

Si vous souhaitez répliquer les ACL, ainsi que les fichiers de données lors de la mise à niveau à partir de Data Lake Storage Gen1 vers Gen2, reportez-vous à [conserver les ACL à partir de Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>Copie incrémentielle 

Plusieurs approches peuvent être utilisées pour charger uniquement les fichiers nouveaux ou mis à jour à partir de la génération 1 ADLS :

- Charger des fichiers nouveaux ou mis à jour en temps partitionné dossier ou nom de fichier, par exemple, / 2019/05/13 / * ;
- Charger les fichiers nouveaux ou mis à jour par LastModifiedDate & gt ;
- Identifier les fichiers nouveaux ou mis à jour par n’importe quel outil/solution tierce 3e, puis passez le nom de fichier ou dossier au pipeline ADF via un paramètre ou un table/le fichier.  

La fréquence appropriée pour effectuer le chargement incrémentiel varie selon le nombre total de fichiers dans ADLS Gen1 et le volume du fichier nouveau ou mis à jour pour être chargé chaque fois.  

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vue d’ensemble de l’activité copie](copy-activity-overview.md)
> [Gen1 de stockage Azure Data Lake connecteur](connector-azure-data-lake-store.md)
> [connecteur d’Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)