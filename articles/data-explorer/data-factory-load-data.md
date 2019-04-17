---
title: Copier des données à partir d’Azure Data Factory à l’Explorateur de données Azure
description: Dans cette rubrique, vous allez recevoir des données de (charge) dans l’Explorateur de données Azure en utilisant l’outil de copie Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c3c5484d865c73a6f478ffc9ad4c3fc86c2c8170
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59619102"
---
# <a name="copy-data-from-azure-data-factory-to-azure-data-explorer"></a>Copier des données à partir d’Azure Data Factory à l’Explorateur de données Azure

Explorateur de données Azure est un service d’analytique de données entièrement géré pour l’analyse en temps réel sur de grands volumes de données de diffusion en continu à partir de nombreuses sources telles que des applications, des sites Web et des appareils IoT. Explorer les données de manière itérative et identifier des tendances et anomalies pour améliorer les produits, l’expérience des clients, analyser des périphériques et améliorer les opérations. Formulez de nouvelles questions et recevez des réponses en quelques minutes. Azure Data Factory est un service informatique d’intégration de données informatique intégralement managé. Vous pouvez utiliser le service pour remplir votre base de données de l’Explorateur de données Azure avec des données à partir de votre système existant et gagner du temps lors de la création de vos solutions d’analytique.

Azure Data Factory offre les avantages suivants pour charger des données dans l’Explorateur de données Azure :

* **Facilité de configuration** : Un Assistant intuitif en cinq étapes sans script nécessaire.
* **Prise en charge de magasin de données riches**: Prise en charge intégrée d’un ensemble complet d’en local et magasins de données basée sur le cloud. Pour une liste détaillée, consultez le tableau [Banques de données prises en charge](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Sécurité et conformité**: Données sont transférées via HTTPS ou ExpressRoute. La présence globale du service garantit que vos données ne quittent jamais les limites géographiques.
* **Hautes performances**: Jusqu'à la vitesse de chargement de données de 1 GBIT/s dans l’Explorateur de données Azure. Pour en savoir plus, voir [Performances de l’activité de copie](/azure/data-factory/copy-activity-performance).

Cet article vous montre comment utiliser l’outil de copie de données Data Factory pour charger des données à partir d’Amazon S3 dans l’Explorateur de données Azure. Vous pouvez suivre des étapes similaires pour copier des données à partir d’autres magasins de données tels que [stockage Blob Azure](/azure/data-factory/connector-azure-blob-storage), [base de données SQL Azure](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle), et [système de fichiers](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Conditions préalables

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Un cluster de l’Explorateur de données Azure et de la base de données](create-cluster-database-portal.md)
* Amazon S3.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Sélectionnez le **créer une ressource** bouton (+) dans le coin supérieur gauche du portail > **Analytique** > **Data Factory**.

   ![Créer une fabrique de données](media/data-factory-load-data/create-adf.png)

1. Dans le **nouvelle fabrique de données** page, fournir des valeurs pour les champs suivants, puis sélectionnez **créer**.

    ![Page Nouvelle fabrique de données](media/data-factory-load-data/my-new-data-factory.png)

    **Paramètre**  | **Description du champ**
    |---|---|
    | **Nom** | Entrez un nom global unique pour votre fabrique de données. Si vous recevez l’erreur *« nom de fabrique de données \"LoadADXDemo\" n’est pas disponible »*, entrez un nom différent pour la fabrique de données. Pour les règles d’affectation de noms d’artefacts Data Factory, consultez [les règles d’affectation de noms Data Factory](/azure/data-factory/naming-rules).|
    | **Abonnement** | Sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. |
    | **Groupe de ressources** | Sélectionnez **créer** et entrez le nom d’un nouveau groupe de ressources. Sélectionnez **utiliser l’existant**, si vous avez un groupe de ressources existant. |
    | **Version** | Sélectionnez **V2**. |
    | **Lieu** | Sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données qui sont utilisés par data factory peuvent être dans d’autres emplacements ou les régions. |
    | | |

1. Sélectionnez les Notifications sur la barre d’outils pour surveiller le processus de création. Une fois la création terminée, accédez à la fabrique de données que vous avez créé. Le **Data Factory** ouvre de la page d’accueil.

   ![Page d’accueil Data Factory](media/data-factory-load-data/data-factory-home-page.png)

1. Sélectionnez le **créer et surveiller** vignette pour lancer l’application dans un onglet séparé.

## <a name="load-data-into-azure-data-explorer"></a>Charger des données dans l’Explorateur de données Azure

Données peuvent être chargées à partir de nombreux types de [magasins de données](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) dans l’Explorateur de données Azure. Cette rubrique décrit en détail le chargement des données à partir d’Amazon S3.

Il existe deux façons de charger des données dans l’Explorateur de données Azure à l’aide d’Azure Data Factory :

* Interface utilisateur d’Azure Data Factory - [ **auteur** onglet](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory **copier des données** outil](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) utilisé dans cet article.

### <a name="copy-data-from-amazon-s3-source"></a>Copier des données à partir d’Amazon S3 (Source)

1. Dans le **prise en main** page, sélectionnez le **copier des données** vignette pour ouvrir l’outil Copier des données.

   ![Copier des données outil vignette](media/data-factory-load-data/copy-data-tool-tile.png)

1. Dans le **propriétés** , spécifiez **nom_tâche** et sélectionnez **suivant**.

    ![Copier à partir des propriétés de la source](media/data-factory-load-data/copy-from-source.png)

1. Dans le **magasin de données Source** , cliquez sur **+ créer une nouvelle connexion**.

    ![Source de données créer la connexion](media/data-factory-load-data/source-create-connection.png)

1. Sélectionnez **Amazon S3**, puis sélectionnez **continuer**

    ![Nouveau service lié](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Dans le **nouveau Service lié (Amazon S3)** , effectuez les étapes suivantes :

    ![Spécifiez le service lié Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Spécifiez **nom** de votre nouveau service lié.
    * Sélectionnez **se connecter via le runtime d’intégration** valeur dans la liste déroulante.
    * Spécifiez la valeur du champ **ID de clé d’accès**.
    * Spécifiez la valeur **Clé d’accès secrète**.
    * Sélectionnez **tester la connexion** pour tester la connexion de service lié que vous avez créé.
    * Sélectionnez **Terminer**.

1. Dans le **magasin de données Source** page, vous verrez votre nouvelle connexion AmazonS31. Sélectionnez **Suivant**.

   ![Connexion de banque de données source](media/data-factory-load-data/source-data-store-created-connection.png)

1. Dans le **choisir le fichier d’entrée ou le dossier** page :

    1. Recherchez le fichier/dossier que vous souhaitez copier. Sélectionnez le dossier/fichier.
    1. Sélectionnez le comportement de copie en fonction des besoins. Conserver **copie binaire** non contrôlé.
    1. Sélectionnez **Suivant**.

    ![Choisir le fichier ou le dossier d’entrée](media/data-factory-load-data/source-choose-input-file.png)

1. Dans le **paramètres des formats de fichier** page Sélectionnez les paramètres appropriés pour votre fichier cliquez sur **suivant**.

   ![Choisir le fichier ou le dossier d’entrée](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copier des données dans l’Explorateur de données Azure (Destination)

Azure Explorateur de données nouveau service lié est créé pour copier les données dans la table de destination de l’Explorateur de données Azure (récepteur) spécifiées ci-dessous.

1. Dans le **magasin de données de Destination** page, vous pouvez utiliser connexion à la banque de données existantes ou spécifier un nouveau magasin de données en cliquant sur **+ créer une nouvelle connexion**.

    ![Page Magasin de données de destination](media/data-factory-load-data/destination-create-connection.png)

1. Dans le **nouveau Service lié** page, sélectionnez **Explorateur de données Azure**, puis sélectionnez **continuer**

    ![Sélectionnez l’Explorateur de données Azure - nouveau service lié](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Dans le **nouveau Service lié (Explorateur de données Azure)** , effectuez les étapes suivantes :

    ![Service lié nouvelle ADX](media/data-factory-load-data/adx-new-linked-service.png)

   * Sélectionnez **nom** pour l’Explorateur de données Azure service lié.
   * Dans **méthode de sélection du compte**: 
        * Sélectionnez le **abonnement Azure à partir de** case d’option et sélectionnez votre **abonnement Azure** compte. Ensuite, sélectionnez votre **Cluster**. Notez la liste déroulante sera répertorier uniquement les clusters qui appartiennent à l’utilisateur.
        * Vous pouvez également sélectionner **entrer manuellement** case d’option et entrez votre **point de terminaison**.
    * Spécifiez le **locataire**.
    * Entrez **ID de principal du Service**.
    * Sélectionnez **clé principale du Service** bouton et entrez **clé du principal du Service**.
    * Sélectionnez votre **base de données** dans le menu déroulant. Vous pouvez également sélectionner **modifier** case à cocher et entrez le nom de votre base de données.
    * Sélectionnez **tester la connexion** pour tester la connexion de service lié que vous avez créé. Si vous pouvez vous connecter à votre installation, une coche verte **connexion réussie** s’affiche.
    * Sélectionnez **Terminer** pour terminer la création du service lié.

    > [!NOTE]
    > Le principal du service est utilisé par Azure Data Factory pour accéder au service de l’Explorateur de données Azure. Pour un principal de service, [créer un Azure Active Directory (Azure AD) principal de service](/azure/azure-stack/azure-stack-create-service-principals#manage-service-principal-for-azure-ad). N’utilisez pas le **Azure Key Vault** (méthode).

1. Le **magasin de données de Destination** s’ouvre. La connexion de données de l’Explorateur de données Azure que vous avez créé est disponible pour utilisation. Sélectionnez **suivant** pour configurer la connexion.

    ![Banque de données de destination ADX](media/data-factory-load-data/destination-data-store.png)

1. Dans **choisir le fichier de sortie ou le dossier**, entrez le nom de dossier de sortie, déterminer les paramètres, puis sélectionnez **suivant**.

    ![Spécification du dossier de sortie](media/data-factory-load-data/specify-path.png)

1. Dans **mappage de Table**, définissez le nom de table de destination, puis sélectionnez **suivant**.

    ![Mappage de table de jeu de données de destination](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Dans le **mappage de colonnes** page :
    * Le premier mappage est effectué par ADF en fonction de [mappage de schéma ADF](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Définir le **mappages de colonnes** pour la table de destination Azure Data Factory. Le mappage par défaut est affiché à partir de la source à la table de destination ADF.
        * Désélectionnez les colonnes que vous n’avez pas besoin de définir votre mappage de colonne.
    * Le deuxième mappage se produit lorsque ces données tabulaires sont ingérées dans l’Explorateur de données Azure. Le mappage est effectué en fonction de [les règles de mappage de volume partagé de cluster](/azure/kusto/management/mappings#csv-mapping). Notez que même si la source de données n’était pas au format CSV, ADF a converti les données dans un format tabulaire, par conséquent, le mappage de volume partagé de cluster est le mappage pertinentes uniquement à ce stade.
        * Sous **propriétés de récepteur de l’Explorateur de données Azure (Kusto)** ajouter pertinentes **nom de mappage d’Ingestion** (facultatif) ce mappage de colonne peut donc être utilisé.
        * Si **nom de mappage d’Ingestion** n’est pas spécifié, l’ordre de mappage « par « nom défini dans **mappages de colonnes** section se produira. En cas de mise en correspondance par « nom », l’Explorateur de données Azure tente de recevoir les données dans un ordre « position de colonne par » (cartes par position comme valeur par défaut).
    * Sélectionnez **suivant**

    ![Mappage de colonnes de jeu de données de destination](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Dans la page **Paramètres** :
    * Affectez à la **les paramètres de tolérance de pannes**.
    * **Les paramètres de performances**: Activer intermédiaire n’est pas applicable. **Paramètres avancés** incluent des considérations relatives aux coûts. Laissez la valeur actuelle si aucune spécifique a besoin.
    * Sélectionnez **Suivant**.

    ![Copier les paramètres de données](media/data-factory-load-data/copy-data-settings.png)

1. Dans **Résumé**, passez en revue les paramètres, puis sélectionnez **suivant**.

    ![Copier des données de synthèses](media/data-factory-load-data/copy-data-summary.png)

1. Dans le **page déploiement**:
    * Sélectionnez **moniteur** pour basculer vers le **moniteur** onglet et voir l’état du pipeline (flux de données, les erreurs et la progression).
    * Sélectionnez **modifier un Pipeline** pour modifier les services liés, jeux de données et pipelines.
    * Sélectionnez **Terminer** à la tâche de copie complète de données

    ![Page Déploiement](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la modification des services liés, jeux de données et pipelines dans le [interface utilisateur Data Factory](/azure/data-factory/quickstart-create-data-factory-portal)

* En savoir plus sur [requêtes de l’Explorateur de données Azure](/azure/data-explorer/web-query-data) pour l’interrogation de données.
