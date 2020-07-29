---
title: Transformation des données avec un flux de données de mappage de réseau VNET managé Azure Data Factory
description: Ce tutoriel fournit des instructions détaillées sur l’utilisation d’Azure Data Factory pour transformer des données à l’aide d’un flux de données de mappage
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: 5515f6c4dfbc5d3c0e391373e763597d7fdc89ed
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528022"
---
# <a name="transform-data-securely-using-mapping-data-flows"></a>Transformation sécurisée des données avec des flux de données de mappage

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

Dans ce tutoriel, vous allez utiliser l’interface utilisateur Azure Data Factory pour créer un pipeline permettant de copier et de transformer des données **d’une source Azure Data Lake Storage (ADLS) Gen2 à un récepteur ADLS Gen2 (tous deux n’autorisant l’accès qu’aux réseaux sélectionnés)** à l’aide d’un flux de données de mappage sur le [réseau virtuel (VNET) managé Azure Data Factory](managed-virtual-network-private-endpoint.md). Le modèle de configuration utilisé dans ce tutoriel peut être étendu lors de la transformation de données avec le flux de données de mappage.

Dans ce tutoriel, vous effectuez les étapes suivantes :

> [!div class="checklist"]
>
> * Créer une fabrique de données.
> * Créer un pipeline avec une activité de flux de données.
> * Générer un flux de données de mappage avec quatre transformations.
> * Effectuer une série de tests sur le pipeline.
> * Superviser une activité de flux de données.

## <a name="prerequisites"></a>Prérequis
* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Azure Storage**. Vous utilisez le stockage ADLS comme magasins de données *source* et *récepteur*. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) pour découvrir comment en créer un. **Vérifiez que le compte de stockage autorise l’accès provenant des « réseaux sélectionnés » uniquement.** 

Le fichier que nous transformons dans ce tutoriel est MoviesDB.csv, qui se trouve [ici](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Pour récupérer le fichier à partir de GitHub, copiez le contenu dans l’éditeur de texte de votre choix pour l’enregistrer localement sous la forme d’un fichier .csv. Pour charger le fichier dans votre compte de stockage, consultez [Chargement d’objets blob avec le Portail Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Les exemples feront référence à un conteneur nommé « sample-data ».

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Au cours de cette étape, vous allez créer une fabrique de données et ouvrir l’interface utilisateur de Data Factory afin de créer un pipeline dans la fabrique de données.

1. Ouvrez **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Analytics** > **Data Factory**.
3. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.

   Le nom de la fabrique de données Azure doit être un nom *global unique*. Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. (par exemple, yournameADFTutorialDataFactory). Consultez l’article [Azure Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les règles Data Factory.

4. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données.
5. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :

    a. Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    b. Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md). 
6. Sous **Version**, sélectionnez **V2**.
7. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (comme le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

8. Sélectionnez **Create** (Créer).

9. Une fois la création terminée, vous voyez apparaître l’avis dans le centre de notifications. Sélectionnez **Accéder à la ressource** pour accéder à la page de la fabrique de données.
10. Sélectionnez **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Création d’un runtime d’intégration Azure sur le réseau virtuel managé ADF
À cette étape, vous allez créer un runtime d’intégration Azure et activer le réseau virtuel managé.

1. Sur le portail ADF, accédez à **Gérer le hub**, puis cliquez sur **Nouveau** pour créer un runtime d’intégration Azure.
   ![Création d’un runtime d’intégration Azure](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Choisissez de créer un runtime d’intégration **Azure**.
   ![Nouveau runtime d’intégration Azure](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Activez **Réseau virtuel**.
   ![Nouveau runtime d’intégration Azure](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Sélectionnez **Create** (Créer).

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Créer un pipeline avec une activité de flux de données

Au cours de cette étape, vous allez créer un pipeline qui contient une activité de flux de données.

1. Dans la page **Prise en main**, cliquez sur **Créer un pipeline**.

   ![Création d’un pipeline](./media/doc-common-process/get-started-page.png)

1. Sous le volet **Propriétés** du pipeline, entrez **TransformMovies** comme **Nom** du pipeline.
1. Dans la barre supérieure de la fabrique, faites glisser le curseur **Débogage du flux de données**. Le mode de débogage permet un test interactif de la logique de transformation sur un cluster Spark activé. Le préchauffage des clusters de flux de données nécessite 5 à 7 minutes et il est recommandé aux utilisateurs d’activer d’abord le débogage s’ils envisagent d’effectuer un développement de flux de données. Pour plus d’informations, consultez [Mode de débogage](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode).

    ![Débogage du flux de données](media/tutorial-data-flow-private/dataflow-debug.png)
1. Dans le volet **Activités**, développez la section **Déplacer et transformer**. Faites glisser et déposez l’activité **Flux de données** à partir du volet vers le canevas du pipeline.

1. Dans la fenêtre contextuelle **Ajout de flux de données**, sélectionnez **Créer un flux de données**, puis **Flux de données de mappage**. Une fois l’opération terminée, cliquez sur **OK**.

    ![Flux de données de mappage](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Nommez votre flux de données **TransformMovies** dans le volet Propriétés.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Générer une logique de transformation dans le canevas de flux de données

Une fois que vous avez créé votre flux de données, vous êtes automatiquement envoyé vers le canevas de flux de données. Au cours de cette étape, vous allez créer un flux de données qui prend le fichier MoviesDB.csv dans le stockage ADLS et qui agrège l’évaluation moyenne des comédies de 1910 à 2000. Vous réécrirez ensuite ce fichier dans le stockage ADLS.

### <a name="add-the-source-transformation"></a>Ajout de la transformation de la source

À cette étape, vous allez configurer Azure Data Lake Storage Gen2 comme source.

1. Dans le canevas de flux de données, ajoutez une source en cliquant sur la zone **Ajouter une source**.

1. Nommez votre source **MoviesDB**. Cliquez sur **Nouveau** pour créer un jeu de données source.

1. Choisissez **Azure Data Lake Storage Gen2**. Cliquez sur Continuer.

1. Choisissez **DelimitedText**. Cliquez sur Continuer.

1. Nommez votre jeu de données **MoviesDB**. Dans la liste déroulante des services liés, choisissez **Nouveau**.

1. Dans l’écran de création de service lié, nommez votre service lié ADLS Gen2 **ADLSGen2**, puis spécifiez votre méthode d’authentification. Entrez ensuite vos informations d’identification de connexion. Dans ce tutoriel, nous utilisons une clé de compte pour nous connecter à notre compte de stockage. 

1. Veillez à activer **Création interactive**. L’activation peut prendre environ 1 minute.

    ![Création interactive](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Sélectionnez **Tester la connexion**. Cela doit échouer, car le compte de stockage n’y autorise pas l’accès sans la création et l’approbation d’un point de terminaison privé. Le message d’erreur comporte un lien permettant de créer un **point de terminaison privé**. Vous pouvez le suivre pour créer un point de terminaison privé managé. *Autre possibilité pour le créer, vous pouvez accéder directement à l’onglet Gérer et suivre les instructions de [cette section](#create-a-managed-private-endpoint).*

1. Laissez la boîte de dialogue ouverte, puis accédez au compte de stockage sélectionné précédemment.

1. Suivez les instructions de [cette section](#approval-of-a-private-link-in-storage-account) pour approuver la liaison privée.

1. Revenez à la boîte de dialogue. Sélectionnez de nouveau **Tester la connexion**, puis sélectionnez **Créer** pour déployer le service lié.

1. Une fois de retour dans l’écran de création du jeu de données, entrez l’emplacement de votre fichier sous le champ **Chemin du fichier**. Dans ce tutoriel, le fichier MoviesDB.csv se trouve dans le conteneur sample-data. Étant donné que le fichier contient des en-têtes, cochez **Première ligne comme en-tête**. Sélectionnez **À partir de la connexion/du magasin** pour importer le schéma d’en-tête directement à partir du fichier situé dans le stockage. Une fois que vous avez terminé, cliquez sur OK.

    ![Chemin source](media/tutorial-data-flow-private/source-file-path.png)

1. Si votre cluster de débogage a démarré, accédez à l’onglet **Aperçu des données**  de la transformation de la source, puis cliquez sur **Actualiser** pour obtenir une capture instantanée des données. Vous pouvez utiliser l’aperçu des données pour vérifier que votre transformation est correctement configurée.

    ![Aperçu des données](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Créer un point de terminaison privé managé

Si vous n’avez pas cliqué sur le lien hypertexte lors du test de la connexion ci-dessus, suivez le chemin ci-dessous. Vous devez maintenant créer un point de terminaison privé managé que vous connecterez au service lié créé ci-dessus.

1. Accédez à l’onglet Gérer.
> [!NOTE]
> L’onglet Gérer n’est pas nécessairement disponible pour toutes les instances de fabrique de données. S’il n’apparaît pas, vous pouvez toujours accéder aux points de terminaison privés par l’onglet « **Créer** » --> « **Connexions** » --> « **Point de terminaison privé** ».
1. Accédez à la section Points de terminaison privés managés.
1. Sélectionnez **+ Nouveau** sous Points de terminaison privés managés.

    ![Nouveau point de terminaison privé managé](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Sélectionnez la vignette Azure Data Lake Storage Gen2 dans la liste, puis sélectionnez **Continuer**.
1. Entrez le nom du compte Stockage Azure créé ci-dessus.
1. Sélectionnez **Create** (Créer).
1. Après avoir attendu quelques secondes, vous devriez voir que la liaison privée créée nécessite une approbation.
1. Sélectionnez le point de terminaison privé que vous avez créé précédemment. Le lien hypertexte qui apparaît vous permet d’approuver le point de terminaison privé au niveau du compte de stockage.

    ![Point de terminaison privé managé](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Approbation d’une liaison privée dans le compte de stockage

1. Dans la section Paramètres du compte de stockage, accédez à **Connexions des points de terminaison privés**.

1. Cochez le point de terminaison privé que vous avez créé, puis sélectionnez **Approuver**.

    ![Approuver le point de terminaison privé](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Ajoutez une description et cliquez sur **Oui**.
1. Revenez à la section **Points de terminaison privés managés** de l’onglet **Gérer** dans Azure Data Factory.
1. L’approbation de votre point de terminaison privé doit prendre environ 1 minute.

### <a name="add-the-filter-transformation"></a>Ajout de la transformation de filtre

1. En regard de votre nœud source dans le canevas de flux de données, cliquez sur l’icône plus (+) pour ajouter une nouvelle transformation. La première transformation que vous ajoutez est un **Filtre**.

    ![Ajouter un filtre](media/tutorial-data-flow-private/add-filter.png)
1. Nommez votre transformation de filtre **FilterYears**. Cliquez sur la zone d’expression en regard de **Filtrer sur** pour ouvrir le générateur d’expressions. Ici, vous allez spécifier votre condition de filtrage.

    ![FilterYears](media/tutorial-data-flow-private/filter-years.png)
1. Le générateur d’expressions de flux de données vous permet de générer de manière interactive des expressions à utiliser dans diverses transformations. Les expressions peuvent inclure des fonctions intégrées, des colonnes du schéma d’entrée et des paramètres définis par l’utilisateur. Pour plus d’informations sur la génération d’expressions, consultez [Générateur d’expressions de flux de données](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    * Dans ce tutoriel, vous voulez filtrer les films du genre comédie qui sont sortis entre 1910 et 2000. Comme l’année est actuellement une chaîne, vous devez la convertir en un entier à l’aide de la fonction ```toInteger()```. Utilisez les opérateurs supérieur ou égal à (>=) et inférieur ou égal à (<=) pour effectuer une comparaison avec les valeurs d’années (year) littérales 1910 et 2000. Unissez ces expressions avec l’opérateur And (&&). L’expression se présente comme suit :

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Pour rechercher les films qui sont des comédies, vous pouvez utiliser la fonction ```rlike()``` pour rechercher le modèle « Comedy » dans la colonne genres. Unissez l’expression rlike avec la comparaison d’année (year) pour obtenir :

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Si vous avez un cluster de débogage actif, vous pouvez vérifier votre logique en cliquant sur **Actualiser** pour afficher la sortie de l’expression par rapport aux entrées utilisées. Il y a plusieurs réponses appropriées sur la façon dont vous pouvez accomplir cette logique à l’aide du langage d’expression de flux de données.

        ![Expression de filtre](media/tutorial-data-flow-private/filter-expression.png)

    * Cliquez sur **Enregistrer et terminer** une fois que vous en avez terminé avec votre expression.

1. Pour vérifier que le filtre fonctionne correctement, récupérez un **Aperçu des données**.

    ![Aperçu des données de filtrage](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Ajout de la transformation d’agrégation

1. La transformation suivante que vous allez ajouter est une transformation **Agrégation** sous **Modificateur de schéma**.

    ![Ajout de l’agrégation](media/tutorial-data-flow-private/add-aggregate.png)
1. Nommez votre transformation d’agrégation **AggregateComedyRatings**. Sous l’onglet **Grouper par**, sélectionnez **year** (année) dans la liste déroulante pour regrouper les agrégations par année de sortie des films.

    ![Agrégation de groupe](media/tutorial-data-flow-private/group-by-year.png)
1. Accédez à l’onglet **Agrégats**. Dans la zone de texte de gauche, nommez la colonne d’agrégation **AverageComedyRating**. Cliquez sur la zone d’expression de droite pour entrer l’expression d’agrégation par le biais du générateur d’expressions.

    ![Agrégation du nom de colonne](media/tutorial-data-flow-private/name-column.png)
1. Pour afficher la moyenne de la colonne **Évaluation**, utilisez la fonction d’agrégation ```avg()```. Comme **Évaluation** est une chaîne et que ```avg()``` prend une entrée numérique, nous devons convertir la valeur en nombre à l’aide de la fonction ```toInteger()```. Voici comment se présente l’expression :

    ```avg(toInteger(Rating))```

    Quand vous avez terminé, cliquez sur **Enregistrer et terminer**.

    ![Enregistrement de l’agrégation](media/tutorial-data-flow-private/save-aggregate.png)
1. Accédez à l’onglet **Aperçu des données** pour afficher la sortie de la transformation. Notez que seules deux colonnes sont affichées : **year** et **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Ajout de la transformation du récepteur

1. Ensuite, vous voulez ajouter une transformation **Récepteur** sous **Destination**.

    ![Ajout d’un récepteur](media/tutorial-data-flow-private/add-sink.png)
1. Nommez votre récepteur **Sink**. Cliquez sur **Nouveau** pour créer votre jeu de données récepteur.

    ![Création d’un récepteur](media/tutorial-data-flow-private/create-sink.png)
1. Sur la page Nouveau jeu de données, choisissez **Azure Data Lake Storage Gen2**. Cliquez sur Continuer.

1. Sur la page Sélectionner le format, choisissez **DelimitedText**. Cliquez sur Continuer.

1. Nommez votre jeu de données récepteur **MoviesSink**. Pour Service lié, choisissez le service lié ADLSGen2 que vous avez créé pour la transformation source. Entrez un dossier de sortie dans lequel écrire vos données. Dans ce tutoriel, nous écrivons dans le dossier « output » se trouvant dans le conteneur « sample-data ». Le dossier ne doit pas nécessairement exister au préalable et peut être créé de façon dynamique. Activez **Première ligne comme en-tête**, puis sélectionnez **Aucun** pour **Importer un schéma**. Cliquez sur OK.

    ![Chemin du récepteur](media/tutorial-data-flow-private/sink-file-path.png)

Vous avez terminé la génération de votre flux de données. Vous êtes prêt à l’exécuter dans votre pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Exécution et supervision du flux de données

Vous pouvez déboguer un pipeline avant de le publier. Au cours de cette étape, vous allez déclencher une exécution de débogage du pipeline de flux de données. Alors que l’aperçu des données n’écrit pas de données, une exécution de débogage écrit des données dans la destination de votre récepteur.

1. Accédez au canevas du pipeline. Cliquez sur **Déboguer** pour déclencher une exécution de débogage.

1. Le débogage de pipeline des activités de flux de données utilise le cluster de débogage actif, mais son initialisation prend toujours au moins une minute. Vous pouvez suivre la progression par le biais de l’onglet **Sortie**. Une fois l’exécution réussie, cliquez sur l’icône en forme de lunettes pour en afficher les détails.

1. La page Détails indique le nombre de lignes et le temps passé à chaque étape de transformation.

    ![Surveillance de la transformation](media/tutorial-data-flow-private/run-details.png)
1. Cliquez sur une transformation pour obtenir des informations détaillées sur les colonnes et le partitionnement des données.

Si vous avez suivi ce tutoriel correctement, vous devez avoir écrit 83 lignes et 2 colonnes dans votre dossier récepteur. Vous pouvez vérifier que les données sont correctes en vérifiant votre stockage d’objets blob.

## <a name="summary"></a>Résumé

Dans ce tutoriel, vous avez utilisé l’interface utilisateur Azure Data Factory pour créer un pipeline permettant de copier et de transformer des données **d’une source Azure Data Lake Storage (ADLS) Gen2 à un récepteur ADLS Gen2 (tous deux n’autorisant l’accès qu’aux réseaux sélectionnés)** à l’aide d’un flux de données de mappage sur le [réseau virtuel (VNET) managé Azure Data Factory](managed-virtual-network-private-endpoint.md).
