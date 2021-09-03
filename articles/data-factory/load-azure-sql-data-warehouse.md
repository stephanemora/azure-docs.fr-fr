---
title: Charger des données dans Azure Synapse Analytics
titleSuffix: Azure Data Factory & Azure Synapse
description: Utilisez Azure Data Factory ou un pipeline Synapse pour copier des données dans Azure Synapse Analytics.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 07/28/2021
ms.openlocfilehash: 0beaad10d588de56bcdf2c458863893e1da8f896
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641555"
---
# <a name="load-data-into-azure-synapse-analytics-using-azure-data-factory-or-a-synapse-pipeline"></a>Charger des données dans Azure Synapse Analytics à l’aide d’Azure Data Factory ou un pipeline Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) est une base de données de scale-out basée sur le cloud capable de traiter de grands volumes de données relationnelles et non relationnelles. Azure Synapse Analytics repose sur une architecture MPP (massively parallel processing) optimisée pour les charges de travail d’entrepôt de données d’entreprise. Elle offre l’élasticité du cloud avec la flexibilité de mettre à l’échelle le stockage et d’exécuter le calcul indépendamment.

Bien démarrer avec Azure Synapse Analytics n’a jamais été aussi facile. Azure Data Factory et sa fonctionnalité de pipelines équivalente dans Azure Synapse lui-même fournissent un service d’intégration de données basé sur le cloud complètement managé. Vous pouvez utiliser le service pour remplir une instance Azure Synapse Analytics avec les données de votre système existant et gagner du temps lors de la création de vos solutions d’analyse.

Les pipelines Azure Data Factory et Synapse offrent les avantages suivants pour le chargement des données dans Azure Synapse Analytics :

* **Facilité de configuration** : assistant intuitif en 5 étapes. Aucun script nécessaire.
* **Prise en charge étendue du magasin de données** : prise en charge intégrée d’un ensemble complet de magasins de données locaux et dans le cloud. Pour une liste détaillée, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sécurité et conformité** : les données sont transférées via HTTPS ou ExpressRoute. La présence globale du service garantit que vos données ne quittent jamais les limites géographiques.
* **Performances sans précédent à l’aide de PolyBase** : PolyBase est le moyen le plus efficace de déplacer des données dans Azure Synapse Analytics. Utilisez la fonction blob intermédiaire pour atteindre des vitesses de charge élevées pour tous les types de magasins de données, y compris le stockage Blob Azure et Data Lake Store. (Polybase prend en charge le stockage Blob Azure et Azure Data Lake Store par défaut.) Pour en savoir plus, voir [Performances de l’activité de copie](copy-activity-performance.md).

Cet article explique comment utiliser l’outil de copie de données pour _charger des données d’Azure SQL Database dans Azure Synapse Analytics_. Vous pouvez procéder de même pour copier des données à partir d’autres types de banques de données.

> [!NOTE]
> Pour plus d’informations, consultez [Copier des données depuis/vers Azure Synapse Analytics](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Azure Synapse Analytics : l'entrepôt de données conserve les données copiées à partir de SQL Database. Si vous ne disposez pas d’Azure Synapse Analytics, consultez les instructions dans [Créer une instance Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md).
* Azure SQL Database : ce tutoriel copie les données de l’exemple de jeu de données Adventure Works LT dans Azure SQL Database. Vous pouvez créer cet échantillon de base de données dans SQL Database en suivant les instructions données dans [Création d’un échantillon de base de données dans Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md).
* Compte Azure Storage : Stockage Azure est utilisé comme objet blob _intermédiaire_ dans l’opération de copie en bloc. Si vous ne possédez pas de compte de stockage Azure, consultez les instructions dans [Créer un compte de stockage](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Créer une fabrique de données

> [!NOTE]
> Vous pouvez ignorer la création d’une nouvelle fabrique de données si vous souhaitez utiliser la fonctionnalité de pipelines dans votre espace de travail Synapse existant pour charger les données.  Azure Synapse incorpore les fonctionnalités d’Azure Data Factory dans sa fonctionnalité de pipelines.

1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Données + Analytique** > **Data Factory** :

2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs des éléments suivants :

    * **Name** : Entrez le nom *LoadSQLDWDemo*. Le nom de votre fabrique de données doit être un nom *global unique. Si l’erreur « Le nom de fabrique de données 'LoadSQLDWDemo' n’est pas disponible » apparaît, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Essayez à nouveau de créer la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
    * **Abonnement**: Sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. 
    * **Groupe de ressources** : Sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez l’option **Créer** et entrez le nom d’un groupe de ressources. Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).  
    * **Version** : Sélectionnez **V2**.
    * **Emplacement** : Sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements et régions. Ces magasins de données incluent Azure Data Lake Store, Stockage Azure, Azure SQL Database, etc.

3. Sélectionnez **Create** (Créer).
4. Une fois la création terminée, accédez à votre fabrique de données. La page d’accueil **Data Factory** devrait s’afficher comme dans l’image suivante :

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Page d’accueil d’Azure Data Factory, avec la mosaïque Ouvrir Azure Data Factory Studio.":::

   Sélectionnez **Ouvrir** dans la mosaïque **Ouvrir Azure Data Factory Studio** pour lancer l’application d’intégration de données dans un onglet distinct.

## <a name="load-data-into-azure-synapse-analytics"></a>Charger des données dans Azure Synapse Analytics

1. Sur la page d’accueil d’Azure Data Factory ou l’espace de travail Azure Synapse, sélectionnez la mosaïque **Ingérer** pour lancer l’outil Copier des données.  Vous pouvez opter pour la **Tâche de copie intégrée**.

2. Sur la page **Propriétés**, choisissez **Tâche de copie intégrée** sous **Type de tâche**, puis **Suivant**.

    ![Page Propriétés](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

3. Dans la page **Banque de données source**, effectuez les étapes suivantes :
    >[!TIP]
    >Dans ce tutoriel, vous utilisez l’*authentification SQL* comme type d’authentification pour votre magasin de données source, mais vous pouvez choisir d’autres méthodes d’authentification prises en charge : *Principal de service* et *Identité managée*, si nécessaire. Pour plus d’informations, reportez-vous aux sections correspondantes de [cet article](./connector-azure-sql-database.md#linked-service-properties).
    >Pour stocker de manière sécurisée des secrets de magasins de données, il est également recommandé d’utiliser un coffre de clés Azure. Pour obtenir des illustrations détaillées, reportez-vous à [cet article](./store-credentials-in-key-vault.md).

    1. Sélectionnez **+ Nouvelle connexion**.

    1. Sélectionnez **Azure SQL Database** dans la galerie, puis sélectionnez **Continuer**. Vous pouvez taper « SQL » dans la zone de recherche pour filtrer les connecteurs.

        ![Sélectionner Azure SQL DB](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)
    
    1. Dans la page **Nouvelle connexion (Azure SQL Database)** , sélectionnez le nom de votre serveur et le nom de votre base de données dans la liste déroulante, puis spécifiez le nom d’utilisateur et le mot de passe. Sélectionnez **Tester la connexion** pour valider les paramètres, puis sélectionnez **Créer**.

        ![Configurer Azure SQL DB](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)
    
    1. Sur la page **Magasin de données source**, sélectionnez la connexion nouvellement créée dans la section **Connexion**.

    1. Dans la section **Tables sources**, entrez **SalesLT** pour filtrer les tables. Activez la case **(Sélectionner tout)** pour utiliser toutes les tables pour la copie, puis cliquez sur **Suivant**.

    ![Capture d’écran montrant la configuration de la page « Magasin de données source ».](./media/load-azure-sql-data-warehouse/source-data-store-page.png)

4. Sur la page **Appliquer le filtre**, spécifiez vos paramètres ou sélectionnez **Suivant**. Vous pouvez prévisualiser les données et visualiser le schéma des données d’entrée en sélectionnant le bouton **Prévisualiser les données** dans cette page. 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/apply-filter.png" alt-text=" Capture d’écran montrant la page « Appliquer le filtre ».":::

5. Dans la page **Banque de données de destination**, effectuez les étapes suivantes :
    >[!TIP]
    >Dans ce tutoriel, vous utilisez l’*authentification SQL* comme type d’authentification pour votre magasin de données de destination, mais vous pouvez choisir d’autres méthodes d’authentification prises en charge : *Principal de service* et *Identité managée*, si nécessaire. Pour plus d’informations, reportez-vous aux sections correspondantes de [cet article](./connector-azure-sql-data-warehouse.md#linked-service-properties).
    >Pour stocker de manière sécurisée des secrets de magasins de données, il est également recommandé d’utiliser un coffre de clés Azure. Pour obtenir des illustrations détaillées, reportez-vous à [cet article](./store-credentials-in-key-vault.md).

    1. Sélectionnez **+ Nouvelle connexion** pour ajouter une connexion.

    1. Sélectionnez **Azure Synapse Analytics** dans la galerie, puis sélectionnez **Continuer**.

        ![Sélectionner Azure Synapse Analytics](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    1. Dans la page **Nouvelle connexion (Azure Synapse Analytics)** , sélectionnez le nom de votre serveur et le nom de votre base de données dans la liste déroulante, puis spécifiez le nom d’utilisateur et le mot de passe. Sélectionnez **Tester la connexion** pour valider les paramètres, puis sélectionnez **Créer**.

        ![Configurer Azure Synapse Analytics](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    1. Sur la page **Magasin de données de destination**, sélectionnez la connexion nouvellement créée dans la section **Connexion**.

6. Dans la page **Mappage de table**, consultez le contenu, puis sélectionnez **Suivant**. Un mappage de table intelligent s’affiche. Les tables source sont mappées sur les tables de destination en fonction des noms de tables. Si une table source n’existe pas dans la destination, le service crée une table de destination qui porte le même nom par défaut. Vous pouvez également mapper une table source sur une table de destination existante.

   ![Capture d’écran montrant la configuration de la page « Magasin de données de destination ».](./media/load-azure-sql-data-warehouse/destination-data-store-page.png)

1. Dans la page **Mappage de colonnes**, passez en revue le contenu, puis cliquez sur **Suivant**. Le mappage de table intelligent repose sur le nom de colonne. Si vous autorisez le service à créer automatiquement les tables, la conversion du type de données peut se produire en cas d’incompatibilités entre les magasins sources et de destination. Si la conversion du type de données n’est pas prise en charge entre la colonne de destination source et de destination, un message d’erreur s’affiche en regard de la table correspondante.

    ![Page de mappage de colonnes](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Dans la page **Paramètres**, effectuez les étapes suivantes :

    1. Spécifiez **CopyFromSQLToSQLDW** pour le champ **Nom de la tâche**.
    1. Dans **Paramètres de mise en lots**, sélectionnez **+ Nouveau** pour renouveler un stockage de mise en lots. Le stockage est utilisé pour les données en préproduction avant leur chargement dans Azure Synapse Analytics avec PolyBase. Une fois la copie terminée, les données temporaires dans Stockage Blob Azure sont nettoyées automatiquement.

    1. Sur la page **Nouveau service lié**, sélectionnez votre compte de stockage, puis choisissez **Créer** pour déployer le service lié.

    1. Désélectionnez l’option **Utiliser le type par défaut**, puis sélectionnez **Suivant**.

    ![Configurer PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

8. Dans la page **Résumé**, vérifiez les paramètres, puis cliquez sur **Suivant**.

9. Sur la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche). 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/deployment-complete-page.png" alt-text=" Capture d’écran montrant la page de déploiement.":::
 
10. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. Lorsque l’exécution du pipeline s’effectue correctement, sélectionnez le lien **CopyFromSQLToSQLDW** sous la colonne **Nom du pipeline** pour afficher les détails de l’exécution d'activité ou réexécuter le pipeline.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring.png" alt-text="Superviser les exécutions de pipelines":::    

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring-synapse.png" alt-text="Superviser les exécutions de pipelines":::   

--- 

12. Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Toutes les exécutions de pipelines** affiché en haut de la fenêtre. Sélectionnez **Actualiser** pour actualiser la liste.

    ![Surveiller des exécutions d’activités](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Pour analyser les détails d’exécution de chaque activité de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) sous **Nom de l’activité** dans l’affichage des exécutions d’activités. Vous pouvez suivre les informations détaillées comme le volume de données copiées à partir de la source dans le récepteur, le débit des données, les étapes d’exécution avec une durée correspondante et les configurations utilisées.

    ![Détails du suivi de l'exécution des activités en premier lieu](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Détails du suivi de l'exécution des activités en deuxième lieu](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Étapes suivantes

Lisez l’article suivant pour en savoir plus sur la prise en charge d’Azure Synapse Analytics :

> [!div class="nextstepaction"]
>[Connecteur Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)