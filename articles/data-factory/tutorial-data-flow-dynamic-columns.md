---
title: Définir dynamiquement des noms de colonnes dans les flux de données
description: Ce tutoriel explique comment définir dynamiquement des noms de colonnes dans les flux de données
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/17/2021
ms.openlocfilehash: 96143f39811658c2794b46f3504a1a604264ab13
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112542802"
---
# <a name="dynamically-set-column-names-in-data-flows"></a>Définir dynamiquement des noms de colonnes dans les flux de données

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Souvent, lors du traitement des données pour les tâches ETL, vous devez modifier les noms des colonnes avant d’écrire les résultats. Il est parfois nécessaire d’aligner les noms de colonnes sur un schéma cible connu. Dans d’autres cas, vous devrez peut-être définir des noms de colonne au moment de l’exécution en fonction de schémas évolutifs. Dans ce tutoriel, vous allez découvrir comment utiliser des flux de données afin de définir dynamiquement des noms de colonne pour vos fichiers de destination et tables de base de données, à l’aide de paramètres et fichiers de configuration externes.

Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

## <a name="prerequisites"></a>Prérequis
* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Azure Storage**. Vous utilisez le stockage ADLS comme magasins de données *source* et *récepteur*. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte de stockage Azure](../storage/common/storage-account-create.md) pour découvrir comment en créer un.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Au cours de cette étape, vous allez créer une fabrique de données et ouvrir l’interface utilisateur de Data Factory afin de créer un pipeline dans la fabrique de données.

1. Ouvrez **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Intégration** > **Data Factory**.
1. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.
1. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données.
1. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
    * Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.
    * Sélectionnez **Créer**, puis entrez le nom d'un groupe de ressources. Pour en savoir plus sur les groupes de ressources, consultez [Utiliser des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).    
1. Sous **Version**, sélectionnez **V2**.
1. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (comme le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.
1. Sélectionnez **Create** (Créer).
1. Une fois la création terminée, vous voyez apparaître l’avis dans le centre de notifications. Sélectionnez **Accéder à la ressource** pour accéder à la page de la fabrique de données.
1. Sélectionnez **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Créer un pipeline avec une activité de flux de données

Au cours de cette étape, vous allez créer un pipeline qui contient une activité de flux de données.

1. Dans la page d’accueil ADF, sélectionnez **Créer un pipeline**.
1. Dans l’onglet **Général** du pipeline, entrez **DeltaLake** pour le **nom** du pipeline.
1. Dans la barre supérieure de la fabrique, faites glisser le curseur **Débogage du flux de données**. Le mode de débogage permet un test interactif de la logique de transformation sur un cluster Spark activé. Le préchauffage des clusters de flux de données nécessite 5 à 7 minutes et il est recommandé aux utilisateurs d’activer d’abord le débogage s’ils envisagent d’effectuer un développement de flux de données. Pour plus d’informations, consultez [Mode de débogage](concepts-data-flow-debug-mode.md).

    ![Activité de flux de données](media/tutorial-data-flow/dataflow1.png)
1. Dans le volet **Activités**, développez la section **Déplacer et transformer**. Faites glisser et déposez l’activité **Flux de données** à partir du volet vers le canevas du pipeline.

    ![Capture d’écran montrant le canevas du pipeline dans lequel vous pouvez supprimer l’activité Flux de données.](media/tutorial-data-flow/activity1.png)
1. Dans la fenêtre contextuelle **Ajout de flux de données**, sélectionnez **Créer un flux de données**, puis nommez votre flux de données **DynaCols**. Une fois que vous avez fini, cliquez sur Terminer.    

## <a name="build-dynamic-column-mapping-in-data-flows"></a>Créer un mappage de colonnes dynamique dans les flux de données

Pour ce tutoriel, nous allons utiliser un exemple de fichier de classement de films et renommer certains champs de la source en un nouvel ensemble de colonnes cibles susceptibles de changer au fil du temps. Les jeux de données que vous allez créer ci-dessous doivent pointer vers ce fichier CSV de films dans votre Stockage Blob ou compte de stockage ADLS Gen2. [Téléchargez le fichier de films ici](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB.csv) et stockez le fichier dans votre compte de stockage Azure.

![Flux final](media/data-flow/dynacols-1.png "Flux final")

### <a name="tutorial-objectives"></a>Objectifs du tutoriel

Vous allez découvrir comment définir dynamiquement des noms de colonnes à l’aide d’un flux de données

1. Créez un jeu de données source pour le fichier CSV de films.
1. Créez un jeu de données de recherche pour un fichier de configuration JSON de mappage de champs.
1. Convertissez les colonnes de la source en noms de colonnes cibles.

### <a name="start-from-a-blank-data-flow-canvas"></a>Démarrer à partir d’un canevas de flux de données vide

Commençons par configurer l’environnement de flux de données correspondant à chacun des mécanismes décrits ci-dessous pour les données déposées dans ADLS Gen2.

1. Cliquez sur la transformation de la source et appelez-la ```movies1```.
1. Cliquez sur le nouveau bouton en regard du jeu de données dans le panneau inférieur.
1. Choisissez Blob ou ADLS Gen2 selon l’emplacement où vous avez stocké le fichier moviesDB.csv cité ci-dessus.
1. Ajoutez une deuxième source, que nous allons utiliser pour la source du fichier JSON de configuration pour rechercher les mappages de champs.
1. Appelez-le comme ```columnmappings```.
1. Pour le jeu de données, pointez vers un nouveau fichier JSON qui stocke une configuration pour le mappage de colonnes. Vous pouvez les coller dans le fichier JSON pour cet exemple de tutoriel :
    ```
    [
    {"prevcolumn":"title","newcolumn":"movietitle"},
    {"prevcolumn":"year","newcolumn":"releaseyear"}
    ]
    ```

1. Définissez les paramètres de la source sur ```array of documents```.
1. Ajoutez une troisième source et appelez-la ```movies2```. Configurez ce paramètre exactement comme ```movies1```.
   
### <a name="parameterized-column-mapping"></a>Mappage de colonnes paramétrable

Dans ce premier scénario, vous allez définir des noms de colonne de sortie dans votre flux de données en définissant le mappage de colonnes en fonction des champs entrants correspondants avec un paramètre qui est un tableau de chaînes de colonnes et qui correspond à chaque index de tableau avec la position ordinale de la colonne entrante. Lors de l’exécution de ce flux de données à partir d’un pipeline, vous pouvez définir des noms de colonnes différents sur chaque exécution de pipeline en envoyant dans ce paramètre de tableau de chaînes l’activité de flux de données.

![Paramètres](media/data-flow/dynacols-3.png "Paramètres")

1. Revenez au concepteur de flux de données et modifiez le flux de données créé ci-dessus.
1. Cliquez sur l’onglet Paramètres
1. Créer un paramètre et choisir un type de données de tableau de chaînes
1. Pour la valeur par défaut, entrez ```['a','b','c']```
1. Utiliser la source principale ```movies1``` pour modifier les noms de colonnes à mapper à ces valeurs de tableau
1. Ajoutez une transformation de sélection. La transformation de sélection est utilisée pour mapper les colonnes entrantes aux nouveaux noms de colonnes pour la sortie.
1. Nous allons remplacer les trois premiers noms de colonne par les nouveaux noms définis dans le paramètre
1. Pour cela, ajoutez trois entrées de mappage basé sur des règles dans le volet inférieur
1. Pour la première colonne, la règle de correspondance est ```position==1``` et le nom est ```$parameter1[1]```
1. Suivre le même modèle pour les colonnes 2 et 3
 
    ![Transformation de sélection](media/data-flow/dynacols-4.png "Transformation de sélection")

1. Cliquez sur les onglets Inspection et Aperçu des données de la Transformation de sélection pour afficher les nouvelles valeurs de nom de colonne ```(a,b,c)``` qui remplacent les noms de colonnes de film, titre et genres d’origine
   
### <a name="create-a-cached-lookup-of-external-column-mappings"></a>Créer une recherche mise en cache de mappages de colonnes externes

Nous allons ensuite créer un récepteur mis en cache pour une recherche ultérieure. Le cache lit un fichier de configuration JSON externe qui peut être utilisé pour renommer des colonnes dynamiquement sur chaque exécution du pipeline de votre flux de données.

1. Revenez au concepteur de flux de données et modifiez le flux de données créé ci-dessus. Ajoutez une transformation du récepteur à la source ```columnmappings```.
1. Définissez le type de récepteur sur ```Cache```.
1. Sous Paramètres, choisissez ```prevcolumn``` comme colonne clé.

### <a name="lookup-columns-names-from-cached-sink"></a>Rechercher les noms des colonnes du récepteur mis en cache

Maintenant que vous avez stocké le contenu du fichier de configuration en mémoire, vous pouvez mapper dynamiquement les noms des colonnes entrantes aux nouveaux noms de colonnes sortantes.

1. Revenez au concepteur de flux de données et modifiez le flux de données créé ci-dessus. Cliquez sur la transformation de la source ```movies2```.
1. Ajoutez une transformation de sélection. Cette fois, nous allons utiliser la Transformation de sélection pour renommer les noms de colonnes en fonction du nom de la cible dans le fichier de configuration JSON qui est stocké dans le récepteur mis en cache.
1. Ajouter un mappage basé sur des règles. Pour la condition de correspondance, utilisez cette formule : ```!isNull(cachedSink#lookup(name).prevcolumn)```.
1. Pour le nom de la colonne de sortie, utilisez cette formule : ```cachedSink#lookup($$).newcolumn```.
1. Ce que nous avons fait, c’est trouver tous les noms de colonnes qui correspondent à la propriété ```prevcolumn``` du fichier de configuration JSON externe et renommer chaque correspondance avec le nouveau nom ```newcolumn```.
1. Cliquez sur les onglets Aperçu des données et Inspecter dans la Transformation de sélection. Vous devez maintenant voir les nouveaux noms de colonne du fichier de mappage externe.

![Source 2](media/data-flow/dynacols-2.png "Source 2")

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez télécharger le pipeline terminé de ce tutoriel [ici](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DynaColsPipe.zip)
* Découvrez-en plus sur les [récepteurs de flux de données](data-flow-sink.md).
