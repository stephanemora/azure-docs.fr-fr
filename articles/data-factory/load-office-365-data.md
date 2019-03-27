---
title: Charger des données d’Office 365 à l’aide d’Azure Data Factory | Microsoft Docs
description: Utiliser Azure Data Factory pour copier des données à partir d’Office 365
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: fe3a3b673f6512856f3640b3e103db8623570a88
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445767"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Charger des données d’Office 365 à l’aide d’Azure Data Factory

Cet article vous montre comment _charger des données d’Office 365 dans le stockage d’objets blob Azure_ à l’aide de Data Factory. Vous pouvez suivre une procédure similaire pour copier des données vers Azure Data Lake Gen1 ou Gen2. Reportez-vous à l’[article sur le connecteur Office 365](connector-office-365.md) traitant de la copie des données à partir d’Office 365 en général.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **créer une ressource** > **données + Analytique** > **Data Factory**: 
   
   ![Sélection Data Factory dans le volet « Nouveau »](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs des champs qui apparaissent dans l’image suivante :
      
   ![Page Nouvelle fabrique de données](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nom** : Entrez un nom global unique pour votre fabrique de données Azure. Si l’erreur « Le nom de fabrique de données \"LoadFromOffice365Demo\" n’est pas disponible » apparaît, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**LoadFromOffice365Demo**. Essayez à nouveau de créer la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
    * **Abonnement**: Sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. 
    * **Groupe de ressources** : Sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez l’option **Créer** et entrez le nom d’un groupe de ressources. Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Version** : Sélectionnez **V2**.
    * **Emplacement** : Sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements et régions. Ces magasins de données incluent Azure Data Lake Store, le Stockage Azure, Microsoft Azure SQL Database, etc.

3. Sélectionnez **Créer**.
4. Une fois la création terminée, accédez à votre fabrique de données. La page d’accueil **Data Factory** devrait s’afficher comme dans l’image suivante :
   
   ![Page d’accueil Data Factory](./media/load-office-365-data/data-factory-home-page.png)

5. Sélectionnez la vignette **Créer et surveiller** pour lancer l’application d’intégration de données dans un onglet séparé.

## <a name="create-a-pipeline"></a>Créer un pipeline

1. Sur la page « Prise en main », cliquez sur **Créer un pipeline**.
 
    ![Création d’un pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. Dans l’onglet **Général** du pipeline, entrez « CopyPipeline » pour le **nom** du pipeline.

3. Dans la boîte à outils Activités > catégorie Déplacer et transformer, faites glisser l’**activité Copier** de la boîte à outils vers la surface du concepteur de pipeline. Spécifiez « CopyFromOffice365ToBlob » comme nom d’activité.

### <a name="configure-source"></a>Configurer la source

1. Accédez au pipeline > **onglet Source**, puis cliquez sur **+ Nouveau** pour créer un jeu de données source. 

2. Dans la fenêtre Nouveau jeu de données, sélectionnez **Office 365**, puis **Terminer**.

    ![Nouveau jeu de données Office 365](./media/load-office-365-data/new-office-365-dataset.png)
 
3. Vous voyez un nouvel onglet ouvert pour le jeu de données Office 365. Dans l’onglet **Général** au bas de la fenêtre Propriétés, entrez « SourceOffice365Dataset » pour le nom.

    ![Configuration générale du jeu de données Office 365](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. Accédez à l’onglet **Connexion** dans la fenêtre Propriétés. Cliquez sur **+ Nouveau** en regard de la zone de texte Service lié.
 
    ![Configuration de la connexion du jeu de données Office 365](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. Dans la fenêtre Nouveau service lié, entrez « Office365LinkedService » comme nom, saisissez l’ID et la clé du principal de service, puis sélectionnez l’option Enregistrer pour déployer le service lié.

    ![Nouveau service lié Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. Une fois le service lié créé, vous revenez dans les paramètres du jeu de données. En regard de « Table », cliquez sur la flèche vers le bas pour développer la liste des jeux de données Office 365 disponibles, puis choisissez « BasicDataSet_v0.Contact_v0 » dans la liste déroulante :

    ![Configuration de la table du jeu de données Office 365](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. Accédez à l’onglet **Schéma** dans la fenêtre Propriétés, puis sélectionnez **Importer un schéma**.  Notez que les valeurs de schéma et des exemples du jeu de données Contact sont affichées.

    ![Configuration du schéma du jeu de données Office 365](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. À présent, revenez au pipeline > onglet Source, puis vérifiez que SourceBlobDataset est sélectionné.
 
### <a name="configure-sink"></a>Configurer le récepteur

1. Accédez au pipeline > onglet **Récepteur**, puis sélectionnez **+ Nouveau** pour créer un jeu de données récepteur.
 
2. Dans la fenêtre Nouveau jeu de données, notez que seule la destination prise en charge est sélectionnée lors de la copie à partir d’Office 365. Sélectionnez **Stockage Blob Azure**, puis **Terminer**.  Dans ce didacticiel, vous copiez des données Office 365 dans un stockage Blob Azure.

    ![Nouveau jeu de données Blob](./media/load-office-365-data/new-blob-dataset.png)

4. Dans l’onglet **Général** de la fenêtre Propriétés, sous Nom, entrez « OutputBlobDataset ».

5. Accédez à l’onglet **Connexion** dans la fenêtre Propriétés. En regard de la zone de texte Service lié, sélectionnez **+ Nouveau**.

    ![Configuration de la connexion de jeu de données Blob](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. Dans la fenêtre Nouveau service lié, entrez « AzureStorageLinkedService » comme nom, sélectionnez « Principal de service » dans la liste déroulante des méthodes d’authentification, renseignez le point de terminaison de service, l’ID de principal de service du client et la clé de principal de service, puis cliquez sur Enregistrer pour déployer le service lié.  Cliquez [ici](connector-azure-blob-storage.md#service-principal-authentication) pour savoir comment configurer l’authentification du principal de service pour le stockage Blob Azure.

    ![Nouveau service lié Blob](./media/load-office-365-data/new-blob-linked-service.png)

7. Une fois le service lié créé, vous revenez dans les paramètres du jeu de données. En regard du chemin d’accès du fichier, sélectionnez **Parcourir** pour choisir le dossier de sortie dans lequel les données Office 365 seront extraites.  Sous « Paramètres de format de fichier », en regard de Format de fichier, choisissez « **Format JSON** ». En regard de Modèle de fichier, choisissez « **Ensemble d’objets** ».

    ![Configuration du chemin d’accès et du format du jeu de données Blob](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. À présent, accédez au pipeline > onglet Source, puis vérifiez que OutputBlobDataset est sélectionné.

## <a name="validate-the-pipeline"></a>Valider le pipeline

Sélectionnez **Valider** dans la barre d’outils pour valider les paramètres du pipeline.

Vous pouvez également obtenir le code JSON associé au pipeline en cliquant sur Code dans le coin supérieur droit.

## <a name="publish-the-pipeline"></a>Publier le pipeline

Dans la barre d’outils supérieure, sélectionnez **Publier tout**. Cette action publie les entités (jeux de données et pipelines) que vous avez créées dans Data Factory.

![Publier les modifications](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Déclencher le pipeline manuellement

Sélectionnez **Déclencher** dans la barre d’outils, puis **Déclencher maintenant**. Sur la page Exécution du pipeline, sélectionnez **Terminer**. 

## <a name="monitor-the-pipeline"></a>Surveiller le pipeline

Accédez à l’onglet **Surveiller** sur la gauche. Vous voyez un pipeline qui est déclenché par un déclencheur manuel. Vous pouvez utiliser les liens de la colonne **Actions** pour afficher les détails de l’activité et réexécuter le pipeline.

![Surveillance d’un pipeline](./media/load-office-365-data/pipeline-monitoring.png) 

Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne Actions. Dans cet exemple, il n’y a qu’une seule activité, vous ne voyez donc qu’une seule entrée dans la liste. Pour plus de détails sur l’opération de copie, cliquez sur le lien **Détails (icône en forme de lunettes)** dans la colonne Actions.

![Analyser l’activité](./media/load-office-365-data/activity-monitoring.png) 

Si c’est la première fois que vous demandez des données pour ce contexte (une combinaison de la table de données consultée, du compte de destination dans lequel les données sont chargées et de l’identité de l’utilisateur à l’origine de la demande d’accès aux données), vous verrez l’activité de copie avec l’état « **En cours d’exécution** », et l’état ne devient « **RequesetingConsent** » que lorsque vous cliquez sur le lien « Détails » sous Actions.  Un membre du groupe d’approbateurs pour l’accès aux données doit approuver la requête dans Privileged Access Management pour permettre l’extraction de données.

_État lors de la demande de consentement :_
![Détails de l’exécution de l’activité - Demande de consentement](./media/load-office-365-data/activity-details-request-consent.png) 

_État lors de l’extraction des données :_

![Détails de l’exécution de l’activité - Extraire les données](./media/load-office-365-data/activity-details-extract-data.png) 

Une fois le consentement fourni, l’extraction de données peut continuer et, après un certain temps, l’exécution du pipeline apparaît comme étant terminée.

![Surveiller le pipeline - Succès](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Maintenant, accédez au stockage Blob Azure de destination et vérifiez que les données Office 365 ont été extraites au format JSON.

## <a name="next-steps"></a>Étapes suivantes

Lisez l’article suivant pour en savoir plus sur la prise en charge d’Azure SQL Data Warehouse : 

> [!div class="nextstepaction"]
>[Connecteur Office 365](connector-office-365.md)