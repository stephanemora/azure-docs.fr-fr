---
title: Copier des données d’Azure Data Factory vers Azure Data Explorer
description: Cette décrit comment ingérer (charger) des données dans Azure Data Explorer à l’aide de l’outil de copie d’Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 2142fbf03daa6667b20db43f9212a2b5e6d7dd44
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657536"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Copier des données dans Azure Data Explorer à l’aide d’Azure Data Factory 

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par de nombreuses sources telles que des applications, des sites web ou des appareils IoT. Explorez les données de façon itérative et identifiez des tendances et anomalies afin d’améliorer les produits et les expériences client, surveiller des appareils et optimiser les opérations. Parcourez les nouvelles questions et recevez des réponses en quelques minutes. Azure Data Factory est un service informatique d’intégration de données informatique intégralement managé. Vous pouvez utiliser le service pour remplir votre base de données Azure Data Explorer avec les données de votre système existant, et gagner du temps lors de la création de vos solutions d’analyse.

Azure Data Factory offre les avantages suivants lors du chargement de données dans Azure Data Explorer :

* **Facilité de configuration** : assistant intuitif en cinq étapes ne nécessitant pas de script.
* **Prise en charge de magasin de données riche** : prise en charge intégrée d’un ensemble complet de magasins de données locaux et dans le cloud. Pour une liste détaillée, consultez le tableau [Banques de données prises en charge](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Sécurité et conformité** : les données sont transférées via HTTPS ou ExpressRoute. La présence globale du service garantit que vos données ne quittent jamais les limites géographiques.
* **Hautes performances** : la vitesse de chargement des données dans Azure Data Explorer peut atteindre 1 Gbit/s. Pour en savoir plus, voir [Performances de l’activité de copie](/azure/data-factory/copy-activity-performance).

Cet article explique comment utiliser l’outil de copie de données Data Factory pour charger les données d’Amazon S3 dans Azure Data Explorer. Vous pouvez procéder de la même façon pour copier des données à partir d’autres magasins de données tels que [Stockage Blob Azure](/azure/data-factory/connector-azure-blob-storage), [Azure SQL Database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle) et [Système de fichiers](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md)
* Source de données.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Cliquez sur le bouton **Créer une ressource** (+) dans l’angle supérieur gauche du portail > **Analytics** > **Data Factory**.

   ![Créer une fabrique de données](media/data-factory-load-data/create-adf.png)

1. Dans la page **Nouvelle fabrique de données**, fournir des valeurs pour les champs suivants, puis sélectionnez **Créer**.

    ![Page Nouvelle fabrique de données](media/data-factory-load-data/my-new-data-factory.png)

    **Paramètre**  | **Description du champ**
    |---|---|
    | **Nom** | Entrez un nom global unique pour votre fabrique de données. Si l’erreur *« Le nom de fabrique de données \"LoadADLSDemo\" n’est pas disponible »* apparaît, saisissez un autre nom pour la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, voir [Azure Data Factory – Règles d’affectation des noms](/azure/data-factory/naming-rules).|
    | **Abonnement** | Sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. |
    | **Groupe de ressources** | Sélectionnez **Créer**, puis entrez le nom d’un nouveau groupe de ressources. Sélectionnez **Utiliser l’existant** si vous avez un groupe de ressources existant. |
    | **Version** | Sélectionnez **V2**. |
    | **Lieu** | Sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements ou régions. |
    | | |

1. Pour superviser le processus de création, sélectionnez Notifications dans la barre d’outils. Une fois la création terminée, accédez à la fabrique de données que vous avez créée. Le page d’accueil de **Data Factory** s’ouvre.

   ![Page d’accueil Data Factory](media/data-factory-load-data/data-factory-home-page.png)

1. Sélectionnez la vignette **Créer et surveiller** pour lancer l’application dans un onglet séparé.

## <a name="load-data-into-azure-data-explorer"></a>Charger des données dans Azure Data Explorer

Vous pouvez charger des données à partir de nombreux types de [magasins de données](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) dans Azure Data Explorer. Cette rubrique décrit en détail le chargement des données à partir d’Amazon S3.

Vous pouvez charger des données dans Azure Data Explorer à l’aide d’Azure Data Factory de deux façons :

* Interface utilisateur d’Azure Data Factory – [Onglet **Créer**](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Outil **Copier des données** Azure Data Factory](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) utilisé dans cet article.

### <a name="copy-data-from-amazon-s3-source"></a>Copier des données à partir d’Amazon S3 (Source)

1. Dans la page **Prise en main**, sélectionnez la vignette **Copier des données** pour démarrer l’outil Copier des données.

   ![Vignette de l’outil Copier des données](media/data-factory-load-data/copy-data-tool-tile.png)

1. Dans la page **Propriétés**, spécifiez **Nom de la tâche**, puis sélectionnez **Suivant**.

    ![Copier à partir des propriétés de la source](media/data-factory-load-data/copy-from-source.png)

1. Dans la page **Banque de données sources**, cliquez sur **+ Créer une connexion**.

    ![Données sources – créer une connexion](media/data-factory-load-data/source-create-connection.png)

1. Sélectionnez **Amazon S3**, puis **Continuer**.

    ![Nouveau service lié](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Dans la page **Nouveau service lié (Amazon S3)** , procédez comme suit :

    ![Spécifier le service lié Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Spécifiez le **Nom** de votre nouveau service lié.
    * Dans la liste déroulante, sélectionnez **Connect via integration runtime** (Se connecter via le runtime d’intégration).
    * Spécifiez la valeur du champ **ID de clé d’accès**.
    * Spécifiez la valeur **Clé d’accès secrète**.
    * Sélectionnez **Test Connection** (Tester la connexion) pour tester la connexion au service lié que vous avez créée.
    * Sélectionnez **Terminer**.

1. Votre nouvelle connexion AmazonS31 s’affiche dans la page **Magasin de données sources**. Sélectionnez **Suivant**.

   ![Connexion de magasin de données sources créée](media/data-factory-load-data/source-data-store-created-connection.png)

1. Dans la page **Choose the input file or folder** (Choisir le fichier ou dossier d’entrée) :

    1. Recherchez le fichier/dossier que vous souhaitez copier. Sélectionnez le fichier/dossier.
    1. Sélectionnez le comportement de copie souhaité. Laissez l’option **Copie binaire** désactivée.
    1. Sélectionnez **Suivant**.

    ![Choisir le fichier ou le dossier d’entrée](media/data-factory-load-data/source-choose-input-file.png)

1. Dans la page des **paramètres de formats de fichier**, sélectionnez les paramètres appropriés pour votre fichier, puis cliquez sur **Suivant**.

   ![Choisir le fichier ou le dossier d’entrée](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Copier des données dans Azure Data Explorer (Destination)

Un nouveau service lié Azure Data Explorer est créé pour copier les données dans la table de destination Azure Data Explorer (récepteur) spécifiée ci-dessous.

1. Dans la page **Magasin de données de destination**, vous pouvez utiliser une connexion au magasin de données existant ou spécifier un nouveau magasin de données en cliquant sur **+ Créer une connexion**.

    ![Page Magasin de données de destination](media/data-factory-load-data/destination-create-connection.png)

1. Dans la page **Nouveau service lié**, sélectionnez **Azure Data Explorer**, puis sélectionnez **Continuer**.

    ![Sélectionner Azure Data Explorer – nouveau service lié](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Dans la page **New Linked Service (Azure Data Explorer)** (Nouveau service lié (Azure Data Explorer)), procédez comme suit :

    ![Nouveau service lié Azure Data Explorer](media/data-factory-load-data/adx-new-linked-service.png)

   * Sélectionnez un **Name** (Nom) pour le service lié Azure Data Explorer.
   * Dans **Account selection method** (Méthode de sélection du compte) : 
        * Sélectionnez la case d’option **From Azure subscription** (À partir d’un abonnement Azure), puis sélectionnez le compte de vitre **abonnement Azure**. Ensuite, sélectionnez votre **Cluster**. Notez que la liste déroulante ne répertorie que les clusters appartenant à l’utilisateur.
        * Vous pouvez également sélectionner la case d’option **Enter manually** (Entrer manuellement), puis entrer votre **Endpoint** (Point de terminaison).
    * Spécifiez le **Tenant** (Locataire).
    * Entrez le **Service principal ID** (ID du principal du service).
    * Sélectionnez le bouton **Service principal key** (Clé du principal du service), puis entrez la **clé du principal du service**.
    * Sélectionnez votre **Database** (Base de données) dans le menu déroulant. Vous pouvez également activer la case à cocher **Edit** (Modifier) et entrer le nom de votre base de données.
    * Sélectionnez **Test Connection** (Tester la connexion) pour tester la connexion au service lié que vous avez créée. Si vous pouvez vous connecter à votre configuration, une coche verte **Connection successful** (Connexion réussie) s’affiche.
    * Sélectionnez **Finish** (Terminer) pour achever la création du service lié.

    > [!NOTE]
    > Le principal du service est utilisé par Azure Data Factory pour accéder au service Azure Data Explorer. Pour le principal du service, [créez un principal du service Azure Active Directory (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). N’utilisez pas la méthode **Azure Key Vault**.

1. Le **Destination data store** (Magasin de données de destination) s’ouvre. La connexion de données Azure Data Explorer que vous avez créée est disponible pour utilisation. Sélectionnez **Next** (Suivant) pour configurer la connexion.

    ![Magasin de données de destination Azure Data Explorer](media/data-factory-load-data/destination-data-store.png)

1. Dans **Table mapping** (Mappage de table), définissez le nom de la table de destination, puis sélectionnez **Next** (Suivant).

    ![Mappage de table de jeu de données de destination](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Dans la page **Column mapping** (Mappage de colonnes) :
    * Le premier mappage est effectué par ADF conformément au [ADF schema mapping](/azure/data-factory/copy-activity-schema-and-type-mapping) (Mappage de schéma ADF)
        * Définissez les **Column mappings** (Mappages de colonnes) pour la table de destination Azure Data Factory. Le mappage par défaut est affiché de la source à la table de destination ADF.
        * Désélectionnez les colonnes dont vous n’avez pas besoin pour définir votre mappage de colonnes.
    * Le deuxième mappage se produit lorsque ces données tabulaires sont ingérées dans Azure Data Explorer. Le mappage est effectué conformément aux [CSV mapping rules](/azure/kusto/management/mappings#csv-mapping) (Règles de mappage CSV). Notez que, même si les données sources n’étaient pas au format CSV, ADF les a converties dans un format tabulaire. Par conséquent, le mappage CSV est le seul mappage pertinent à ce stade.
        * Sous **Azure Data Explorer (Kusto) sink properties** (Propriétés du récepteur Azure Data Explorer (Kusto)), ajoutez le **Ingestion mapping name** (Nom de mappage d’ingestion) pertinent (facultatif) afin que ce mappage de colonnes puisse être utilisé.
        * Si le **Ingestion mapping name** (Nom de mappage d’ingestion) n’est pas spécifié, l’ordre de mappage « par nom » défini dans la section **Column mappings** (Mappages de colonnes) est appliqué. Si le mappage « par nom » échoue, Azure Data Explorer tente d’ingérer les données dans un ordre « par position de colonne » (mappe par position par défaut).
    * Sélectionnez **Suivant**.

    ![Mappage de colonnes de jeu de données de destination](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Dans la page **Paramètres** :
    * Définissez les **Fault tolerance settings** (Paramètres de tolérance de panne) pertinents.
    * **Performance settings** (Paramètres de performance) : L’option Enable staging (Activer le mode de préproduction) n’est pas applicable. Les **Advanced settings** (Paramètres avancés) incluent des considérations relatives aux coûts. Conservez la valeur actuelle si vous n’avez pas de besoins spécifiques.
    * Sélectionnez **Suivant**.

    ![Copier les paramètres de données](media/data-factory-load-data/copy-data-settings.png)

1. Dans la page **Summary** (Résumé), vérifiez les paramètres, puis cliquez sur **Next** (Suivant).

    ![Copier le résumé des données](media/data-factory-load-data/copy-data-summary.png)

1. Dans la **page de déploiement** :
    * Sélectionnez **Monitor** (Moniteur) pour basculer vers l’onglet **Moniteur** et voir l’état du pipeline (progression, erreurs et flux de données).
    * Sélectionnez **Edit Pipeline** (Modifier le pipeline) pour modifier les services liés, les jeux de données et les pipelines.
    * Sélectionnez **Finish** (Terminer) pour achever la tâche de copie de données.

    ![Page Déploiement](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez le [connecteur Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) dans Azure Data Factory.

* Apprenez-en davantage sur la modification des services liés, jeux de données et pipelines dans l’[interface utilisateur de Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).

* Découvrez les [requêtes Azure Data Explorer](/azure/data-explorer/web-query-data) pour l’interrogation de données.
