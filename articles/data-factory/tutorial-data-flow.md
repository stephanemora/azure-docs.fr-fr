---
title: Transformer des données avec un flux de données de mappage
description: Ce tutoriel fournit des instructions détaillées sur l’utilisation d’Azure Data Factory pour transformer des données à l’aide d’un flux de données de mappage
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/04/2021
ms.openlocfilehash: 5ff1ea92056b4fe5f090442df72e7c39d53368b9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626226"
---
# <a name="transform-data-using-mapping-data-flows"></a>Transformer des données avec des flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Dans ce tutoriel, vous allez utiliser l’interface utilisateur Azure Data Factory pour créer un pipeline qui copie et transforme des données d’une source Azure Data Lake Storage (ADLS) Gen2 en récepteur ADLS Gen2 à l’aide d’un flux de données de mappage. Le modèle de configuration utilisé dans ce tutoriel peut être développé lors d’une transformation de données à l’aide d’un flux de données de mappage.

 >[!NOTE]
   >Ce didacticiel est conçu pour le mappage des flux de données en général. Les flux de données sont disponibles à la fois dans Azure Data Factory et Synapse Pipelines. Si vous ne connaissez pas les flux de données dans Azure Synapse Pipelines, consultez [Flux de données avec Azure Synapse Pipelines](../synapse-analytics/concepts-data-flow-overview.md) 
   
Dans ce tutoriel, vous effectuez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un pipeline avec une activité de flux de données.
> * Générer un flux de données de mappage avec quatre transformations.
> * Effectuer une série de tests sur le pipeline.
> * Superviser une activité de flux de données.

## <a name="prerequisites"></a>Prérequis
* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Azure Storage**. Vous utilisez le stockage ADLS comme magasins de données *source* et *récepteur*. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte de stockage Azure](../storage/common/storage-account-create.md) pour découvrir comment en créer un.

Le fichier que nous transformons dans ce tutoriel est MoviesDB.csv, qui se trouve [ici](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Pour récupérer le fichier à partir de GitHub, copiez le contenu dans l’éditeur de texte de votre choix pour l’enregistrer localement sous la forme d’un fichier .csv. Pour charger le fichier dans votre compte de stockage, consultez [Chargement d’objets blob avec le Portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Les exemples feront référence à un conteneur nommé « sample-data ».

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Au cours de cette étape, vous allez créer une fabrique de données et ouvrir l’interface utilisateur de Data Factory afin de créer un pipeline dans la fabrique de données.

1. Ouvrez **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Intégration** > **Data Factory** :

   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Sélection de Data Factory dans le volet &quot;Nouveau&quot;":::

3. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.

   Le nom de la fabrique de données Azure doit être un nom *global unique*. Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. (par exemple, yournameADFTutorialDataFactory). Consultez l’article [Azure Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les règles Data Factory.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nouveau message d’erreur de la fabrique de données pour le nom dupliqué.":::
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

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Créer un pipeline avec une activité de flux de données

Au cours de cette étape, vous allez créer un pipeline qui contient une activité de flux de données.

1. Dans la page d’accueil Azure Data Factory, sélectionnez **Orchestrer**.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Capture d’écran montrant la page d’accueil ADF.":::

1. Sous l’onglet **Général** du pipeline, entrez **TransformMovies** comme **Nom** du pipeline.
1. Dans le volet **Activités**, développez la section **Déplacer et transformer**. Faites glisser et déposez l’activité **Flux de données** à partir du volet vers le canevas du pipeline.

    :::image type="content" source="media/tutorial-data-flow/activity1.png" alt-text="Capture d’écran montrant le canevas du pipeline dans lequel vous pouvez supprimer l’activité Flux de données.":::
1. Dans la fenêtre contextuelle **Ajout de flux de données**, sélectionnez **Créer un flux de données**, puis nommez votre flux de données **TransformMovies**. Une fois que vous avez fini, cliquez sur Terminer.

    :::image type="content" source="media/tutorial-data-flow/activity2.png" alt-text="Capture d’écran montrant où nommer votre flux de données lorsque vous en créez un.":::
1. Dans la barre supérieure du canevas du pipeline, faites glisser le curseur **Débogage du flux de données** pour l’activer. Le mode de débogage permet un test interactif de la logique de transformation sur un cluster Spark activé. Le préchauffage des clusters de flux de données nécessite 5 à 7 minutes et il est recommandé aux utilisateurs d’activer d’abord le débogage s’ils envisagent d’effectuer un développement de flux de données. Pour plus d’informations, consultez [Mode de débogage](concepts-data-flow-debug-mode.md).

    :::image type="content" source="media/tutorial-data-flow/dataflow1.png" alt-text="Activité de flux de données":::

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Générer une logique de transformation dans le canevas de flux de données

Une fois que vous avez créé votre flux de données, vous êtes automatiquement envoyé vers le canevas de flux de données. Au cours de cette étape, vous allez créer un flux de données qui prend le fichier MoviesDB.csv dans le stockage ADLS et qui agrège l’évaluation moyenne des comédies de 1910 à 2000. Vous réécrirez ensuite ce fichier dans le stockage ADLS.

1. Dans le canevas de flux de données, ajoutez une source en cliquant sur la zone **Ajouter une source**.

    :::image type="content" source="media/tutorial-data-flow/dataflow2.png" alt-text="Capture d’écran montrant la zone Ajouter une source.":::
1. Nommez votre source **MoviesDB**. Cliquez sur **Nouveau** pour créer un jeu de données source.

    :::image type="content" source="media/tutorial-data-flow/dataflow3.png" alt-text="Capture d’écran montrant où sélectionner Nouveau après avoir nommé votre source.":::
1. Choisissez **Azure Data Lake Storage Gen2**. Cliquez sur Continuer.

    :::image type="content" source="media/tutorial-data-flow/dataset1.png" alt-text="Capture d’écran montrant l’emplacement de la vignette Azure Data Lake Storage Gen2.":::
1. Choisissez **DelimitedText**. Cliquez sur Continuer.

    :::image type="content" source="media/tutorial-data-flow/dataset2.png" alt-text="Capture d’écran montrant la vignette DelimitedText.":::
1. Nommez votre jeu de données **MoviesDB**. Dans la liste déroulante des services liés, choisissez **Nouveau**.

    :::image type="content" source="media/tutorial-data-flow/dataset3.png" alt-text="Capture d’écran montrant la liste déroulante Service lié.":::
1. Dans l’écran de création de service lié, nommez votre service lié ADLS Gen2 **ADLSGen2**, puis spécifiez votre méthode d’authentification. Entrez ensuite vos informations d’identification de connexion. Dans ce tutoriel, nous utilisons une clé de compte pour nous connecter à notre compte de stockage. Vous pouvez cliquer sur **Tester la connexion** pour vérifier que vos informations d’identification ont été entrées correctement. Quand vous avez terminé, cliquez sur Créer.

    :::image type="content" source="media/tutorial-data-flow/ls1.png" alt-text="Service lié":::
1. Une fois de retour dans l’écran de création du jeu de données, entrez l’emplacement de votre fichier sous le champ **Chemin du fichier**. Dans ce tutoriel, le fichier MoviesDB.csv se trouve dans le conteneur sample-data. Étant donné que le fichier contient des en-têtes, cochez **Première ligne comme en-tête**. Sélectionnez **À partir de la connexion/du magasin** pour importer le schéma d’en-tête directement à partir du fichier situé dans le stockage. Une fois que vous avez terminé, cliquez sur OK.

    :::image type="content" source="media/tutorial-data-flow/dataset4.png" alt-text="Groupes de données":::
1. Si votre cluster de débogage a démarré, accédez à l’onglet **Aperçu des données**  de la transformation de la source, puis cliquez sur **Actualiser** pour obtenir une capture instantanée des données. Vous pouvez utiliser l’aperçu des données pour vérifier que votre transformation est correctement configurée.

    :::image type="content" source="media/tutorial-data-flow/dataflow4.png" alt-text="Capture d’écran montrant où afficher un aperçu de vos données pour vérifier que votre transformation est correctement configurée.":::
1. En regard de votre nœud source dans le canevas de flux de données, cliquez sur l’icône plus (+) pour ajouter une nouvelle transformation. La première transformation que vous ajoutez est un **Filtre**.

    :::image type="content" source="media/tutorial-data-flow/dataflow5.png" alt-text="Canevas de flux de données":::
1. Nommez votre transformation de filtre **FilterYears**. Cliquez sur la zone d’expression en regard de **Filtrer sur** pour ouvrir le générateur d’expressions. Ici, vous allez spécifier votre condition de filtrage.

    :::image type="content" source="media/tutorial-data-flow/filter1.png" alt-text="Capture d’écran montrant la zone d’expression Filtrer sur.":::
1. Le générateur d’expressions de flux de données vous permet de générer de manière interactive des expressions à utiliser dans diverses transformations. Les expressions peuvent inclure des fonctions intégrées, des colonnes du schéma d’entrée et des paramètres définis par l’utilisateur. Pour plus d’informations sur la génération d’expressions, consultez [Générateur d’expressions de flux de données](concepts-data-flow-expression-builder.md).

    Dans ce tutoriel, vous voulez filtrer les films du genre comédie qui sont sortis entre 1910 et 2000. Comme l’année est actuellement une chaîne, vous devez la convertir en un entier à l’aide de la fonction `toInteger()`. Utilisez les opérateurs supérieur ou égal à (>=) et inférieur ou égal à (<=) pour effectuer une comparaison avec les valeurs d’années (year) littérales 1910 et 2000. Unissez ces expressions avec l’opérateur And (&&). L’expression se présente comme suit :

    `toInteger(year) >= 1910 && toInteger(year) <= 2000`

    Pour rechercher les films qui sont des comédies, vous pouvez utiliser la fonction `rlike()` pour rechercher le modèle « Comedy » dans la colonne genres. Unissez l’expression `rlike` avec la comparaison d’année (year) pour obtenir :

    `toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')`

    Si vous avez un cluster de débogage actif, vous pouvez vérifier votre logique en cliquant sur **Actualiser** pour afficher la sortie de l’expression par rapport aux entrées utilisées. Il y a plusieurs réponses appropriées sur la façon dont vous pouvez accomplir cette logique à l’aide du langage d’expression de flux de données.

    :::image type="content" source="media/tutorial-data-flow/filter2.png" alt-text="Filter":::

    Cliquez sur **Enregistrer et terminer** une fois que vous en avez terminé avec votre expression.

1. Pour vérifier que le filtre fonctionne correctement, récupérez un **Aperçu des données**.

    :::image type="content" source="media/tutorial-data-flow/filter3.png" alt-text="Capture d’écran montrant l’aperçu des données que vous avez récupéré (fetch).":::
1. La transformation suivante que vous allez ajouter est une transformation **Agrégation** sous **Modificateur de schéma**.

    :::image type="content" source="media/tutorial-data-flow/agg1.png" alt-text="Capture d’écran montrant le modificateur de schéma Agrégation.":::
1. Nommez votre transformation d’agrégation **AggregateComedyRatings**. Sous l’onglet **Grouper par**, sélectionnez **year** (année) dans la liste déroulante pour regrouper les agrégations par année de sortie des films.

    :::image type="content" source="media/tutorial-data-flow/agg2.png" alt-text="Capture d’écran montrant l’option Year (Année) sous l’onglet Grouper par dans les paramètres d’agrégation.":::
1. Accédez à l’onglet **Agrégats**. Dans la zone de texte de gauche, nommez la colonne d’agrégation **AverageComedyRating**. Cliquez sur la zone d’expression de droite pour entrer l’expression d’agrégation par le biais du générateur d’expressions.

    :::image type="content" source="media/tutorial-data-flow/agg3.png" alt-text="Capture d’écran montrant l’option Year (Année) sous l’onglet Agrégats dans les paramètres d’agrégation.":::
1. Pour afficher la moyenne de la colonne **Évaluation**, utilisez la fonction d’agrégation ```avg()```. Comme **Évaluation** est une chaîne et que ```avg()``` prend une entrée numérique, nous devons convertir la valeur en nombre à l’aide de la fonction ```toInteger()```. Voici comment se présente l’expression :

    `avg(toInteger(Rating))`

    Quand vous avez terminé, cliquez sur **Enregistrer et terminer**.

    :::image type="content" source="media/tutorial-data-flow/agg4.png" alt-text="Capture d’écran montrant l’expression enregistrée.":::
1. Accédez à l’onglet **Aperçu des données** pour afficher la sortie de la transformation. Notez que seules deux colonnes sont affichées : **year** et **AverageComedyRating**.

    :::image type="content" source="media/tutorial-data-flow/agg3.png" alt-text="Agrégat":::
1. Ensuite, vous voulez ajouter une transformation **Récepteur** sous **Destination**.

    :::image type="content" source="media/tutorial-data-flow/sink1.png" alt-text="Capture d’écran montrant où ajouter une transformation de récepteur sous Destination.":::
1. Nommez votre récepteur **Sink**. Cliquez sur **Nouveau** pour créer votre jeu de données récepteur.

    :::image type="content" source="media/tutorial-data-flow/sink2.png" alt-text="Capture d’écran montrant où nommer votre récepteur et créer un jeu de données récepteur.":::
1. Choisissez **Azure Data Lake Storage Gen2**. Cliquez sur Continuer.

    :::image type="content" source="media/tutorial-data-flow/dataset1.png" alt-text="Capture d’écran montrant la vignette Azure Data Lake Storage Gen2 que vous pouvez sélectionner.":::
1. Choisissez **DelimitedText**. Cliquez sur Continuer.

    :::image type="content" source="media/tutorial-data-flow/dataset2.png" alt-text="Dataset":::
1. Nommez votre jeu de données récepteur **MoviesSink**. Pour le service lié, choisissez le service lié ADLS Gen2 que vous avez créé à l’étape 6. Entrez un dossier de sortie dans lequel écrire vos données. Dans ce tutoriel, nous écrivons dans le dossier « output » se trouvant dans le conteneur « sample-data ». Le dossier ne doit pas nécessairement exister au préalable et peut être créé de façon dynamique. Activez **Première ligne comme en-tête**, puis sélectionnez **Aucun** pour **Importer un schéma**. Cliquez sur Finish.

    :::image type="content" source="media/tutorial-data-flow/sink3.png" alt-text="Section sink":::

Vous avez terminé la génération de votre flux de données. Vous êtes prêt à l’exécuter dans votre pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Exécution et supervision du flux de données

Vous pouvez déboguer un pipeline avant de le publier. Au cours de cette étape, vous allez déclencher une exécution de débogage du pipeline de flux de données. Alors que l’aperçu des données n’écrit pas de données, une exécution de débogage écrit des données dans la destination de votre récepteur.

1. Accédez au canevas du pipeline. Cliquez sur **Déboguer** pour déclencher une exécution de débogage.

    :::image type="content" source="media/tutorial-data-flow/pipeline1.png" alt-text="Capture d’écran montrant le canevas du pipeline avec l’option Déboguer mise en évidence.":::
1. Le débogage de pipeline des activités de flux de données utilise le cluster de débogage actif, mais son initialisation prend toujours au moins une minute. Vous pouvez suivre la progression par le biais de l’onglet **Sortie**. Une fois l’exécution réussie, cliquez sur l’icône en forme de lunettes pour ouvrir le volet de supervision.

    :::image type="content" source="media/tutorial-data-flow/pipeline2.png" alt-text="Pipeline":::
1. Dans le volet de supervision, vous pouvez voir le nombre de lignes et de temps passé dans chaque étape de transformation.

    :::image type="content" source="media/tutorial-data-flow/pipeline3.png" alt-text="Capture d’écran montrant le volet de supervision dans lequel vous pouvez voir le nombre de lignes et le temps passé à chaque étape de transformation.":::
1. Cliquez sur une transformation pour obtenir des informations détaillées sur les colonnes et le partitionnement des données.

    :::image type="content" source="media/tutorial-data-flow/pipeline4.png" alt-text="Surveillance":::

Si vous avez suivi ce tutoriel correctement, vous devez avoir écrit 83 lignes et 2 colonnes dans votre dossier récepteur. Vous pouvez vérifier que les données sont correctes en vérifiant votre stockage d’objets blob.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, le pipeline exécute un flux de données qui agrège l’évaluation moyenne des comédies de 1910 à 2000 et qui écrit les données dans ADLS. Vous avez appris à :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un pipeline avec une activité de flux de données.
> * Générer un flux de données de mappage avec quatre transformations.
> * Effectuer une série de tests sur le pipeline.
> * Superviser une activité de flux de données.

En savoir plus sur le [langage d’expression de flux de données](data-flow-expression-functions.md).
