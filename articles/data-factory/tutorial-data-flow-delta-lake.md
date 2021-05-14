---
title: ETL de Delta Lake avec flux de données
description: Ce tutoriel fournit des instructions pas à pas pour l’utilisation de flux de données afin de transformer et d’analyser des données dans Delta Lake.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/16/2021
ms.openlocfilehash: 4a88ed2df74d3eebb96c42e2cdc87b14153419cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565370"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Transformer des données dans Delta Lake à l’aide de flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Dans ce tutoriel, vous allez utiliser le canevas de flux de données pour créer des flux de données qui vous permettent d’analyser et de transformer des données dans Azure Data Lake Storage (ADLS) Gen2 et de les stocker dans Delta Lake.

## <a name="prerequisites"></a>Prérequis
* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Azure Storage**. Vous utilisez le stockage ADLS comme magasins de données *source* et *récepteur*. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte de stockage Azure](../storage/common/storage-account-create.md) pour découvrir comment en créer un.

Le fichier que nous transformons dans ce tutoriel est MoviesDB.csv, qui se trouve [ici](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv). Pour récupérer le fichier à partir de GitHub, copiez le contenu dans l’éditeur de texte de votre choix pour l’enregistrer localement sous la forme d’un fichier .csv. Pour charger le fichier dans votre compte de stockage, consultez [Chargement d’objets blob avec le Portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Les exemples feront référence à un conteneur nommé « sample-data ».

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Au cours de cette étape, vous allez créer une fabrique de données et ouvrir l’interface utilisateur de Data Factory afin de créer un pipeline dans la fabrique de données.

1. Ouvrez **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Intégration** > **Data Factory**.
1. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.
1. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données.
1. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :

    a. Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    b. Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md). 
1. Sous **Version**, sélectionnez **V2**.
1. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (comme le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.
1. Sélectionnez **Create** (Créer).
1. Une fois la création terminée, vous voyez apparaître l’avis dans le centre de notifications. Sélectionnez **Accéder à la ressource** pour accéder à la page de la fabrique de données.
1. Sélectionnez **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Créer un pipeline avec une activité de flux de données

Au cours de cette étape, vous allez créer un pipeline qui contient une activité de flux de données.

1. Dans la page **Prise en main**, cliquez sur **Créer un pipeline**.

   ![Création d’un pipeline](./media/doc-common-process/get-started-page.png)

1. Dans l’onglet **Général** du pipeline, entrez **DeltaLake** pour le **nom** du pipeline.
1. Dans le volet **Activités**, développez la section **Déplacer et transformer**. Faites glisser et déposez l’activité **Flux de données** à partir du volet vers le canevas du pipeline.

    ![Capture d’écran montrant le canevas du pipeline dans lequel vous pouvez supprimer l’activité Flux de données.](media/tutorial-data-flow/activity1.png)
1. Dans la fenêtre contextuelle **Ajout de flux de données**, sélectionnez **Créer un flux de données**, puis nommez votre flux de données **DeltaLake**. Une fois que vous avez fini, cliquez sur Terminer.

    ![Capture d’écran montrant où nommer votre flux de données lorsque vous en créez un.](media/tutorial-data-flow/activity2.png)
1. Dans la barre supérieure du canevas du pipeline, faites glisser le curseur **Débogage du flux de données** pour l’activer. Le mode de débogage permet un test interactif de la logique de transformation sur un cluster Spark activé. Le préchauffage des clusters de flux de données nécessite 5 à 7 minutes et il est recommandé aux utilisateurs d’activer d’abord le débogage s’ils envisagent d’effectuer un développement de flux de données. Pour plus d’informations, consultez [Mode de débogage](concepts-data-flow-debug-mode.md).

    ![Capture d’écran montrant l’emplacement du curseur de débogage du flux de données.](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Générer une logique de transformation dans le canevas de flux de données

Vous allez générer deux flux de données dans ce tutoriel. Le premier flux de données est une source simple à réceptionner pour générer un nouveau Delta Lake à partir du fichier CSV des films ci-dessus. Enfin, vous allez créer le modèle de flux ci-dessous pour mettre à jour les données dans Delta Lake.

![Flux final](media/data-flow/data-flow-tutorial-6.png &quot;Flux final")

### <a name="tutorial-objectives"></a>Objectifs du tutoriel

1. Prendre la source du jeu de données MoviesCSV ci-dessus et former un nouveau Delta Lake à partir de celle-ci
1. Générer la logique pour mettre à jour les évaluations des films de 1988 sur « 1 »
1. Supprimer tous les films de 1950
1. Insérer de nouveaux films pour 2021 en dupliquant les films de 1960

### <a name="start-from-a-blank-data-flow-canvas"></a>Démarrer à partir d’un canevas de flux de données vide

1. Cliquez sur la transformation de la source.
1. Cliquez sur Nouveau à côté du jeu de données dans le volet inférieur 1 Créer un nouveau service lié pour ADLS Gen2.
1. Choisissez Texte délimité pour le type de jeu de données.
1. Nommez le jeu de données « MoviesCSV ». 
1. Pointez sur le fichier MoviesCSV que vous avez chargé dans le stockage ci-dessus.
1. Définissez-le sur l’option de délimitation par des virgules et incluez l’en-tête sur la première ligne. 
1. Accédez à l’onglet de projection de la source, puis cliquez sur « Détecter les types de données ».
1. Une fois que vous avez votre ensemble de projections, vous pouvez continuer. 
1. Ajoutez une transformation du récepteur.
1. Delta est un type de jeu de données inlined. Vous devez pointer vers votre compte de stockage ADLS Gen2.
   
   ![Inline dataset](media/data-flow/data-flow-tutorial-5.png "Jeu de données inlined")

1. Choisissez un nom de dossier dans votre conteneur de stockage où vous souhaitez qu’ADF crée l’instance de Delta Lake.
1. Revenez au concepteur de pipeline et cliquez sur Déboguer pour exécuter le pipeline en mode débogage avec uniquement cette activité de flux de données sur le canevas. Vous générez ainsi votre nouvelle instance de Delta Lake dans ADLS Gen2.
1. Dans Ressources de fabrique, cliquez sur Nouveau > Flux de données. 
1. Réutilisez MoviesCSV comme source et cliquez à nouveau sur « Détecter les types de données ».
1. Ajoutez une transformation de filtre à votre transformation source dans le graphique.
1. Autorisez uniquement les lignes de film qui correspondent aux trois années avec lesquelles vous allez travailler, à savoir 1950, 1988 et 1960.
1. Mettez à jour les évaluations pour chaque film de 1988 sur « 1 » en ajoutant maintenant une transformation de colonne dérivée à votre transformation de filtre.
1. Dans cette même colonne dérivée, créez des films pour 2021 en prenant une année existante et en attribuant la valeur 2021 à l’année. Prenons l’année 1960.
1. Voici à quoi ressembleront vos trois colonnes dérivées.

   ![Colonne dérivée](media/data-flow/data-flow-tutorial-2.png "Colonne dérivée")
   
1. Les stratégies ```Update, insert, delete, and upsert``` sont créées dans la transformation de modification de ligne. Ajoutez une transformation de modification de ligne après votre colonne dérivée.
1. Vos stratégies de modification de ligne doivent ressembler à ce qui suit.

   ![Modification de ligne](media/data-flow/data-flow-tutorial-3.png "Ligne Alter")
   
1. Maintenant que vous avez défini la stratégie appropriée pour chaque type de modification de ligne, vérifiez que les règles de mise à jour appropriées ont été définies sur la transformation du récepteur.

   ![Section sink](media/data-flow/data-flow-tutorial-4.png "Récepteur")
   
1. Ici, nous utilisons le récepteur Delta Lake pour votre lac de données ADLS Gen2 et autorisons les insertions, les mises à jour et les suppressions. 
1. Notez que Colonnes clés désigne une clé composite composée de la colonne clé primaire Movie et de la colonne Year. Cela est dû au fait que nous avons créé de faux films 2021 en dupliquant les lignes de 1960. Cela permet d’éviter les collisions lors de la recherche des lignes existantes en assurant l’unicité.

### <a name="download-completed-sample"></a>Télécharger l’exemple terminé
[Vous trouverez ici un exemple de solution pour le pipeline Delta avec un flux de données pour la mise à jour/suppression de lignes dans le lac.](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [langage d’expression de flux de données](data-flow-expression-functions.md).
