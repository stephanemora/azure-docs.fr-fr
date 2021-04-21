---
title: Meilleures pratiques en matière d'écriture de fichiers dans un lac de données à l'aide de flux de données
description: Ce tutoriel présente les meilleures pratiques en matière d'écriture de fichiers dans un lac de données à l'aide de flux de données
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/01/2021
ms.openlocfilehash: 8010f3f95c9358714b659df5821a375bd8488ad8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581996"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>Meilleures pratiques en matière d'écriture de fichiers dans un lac de données à l'aide de flux de données

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Ce tutoriel présente les meilleures pratiques à suivre lors de l'écriture de fichiers dans ADLS Gen2 ou Stockage Blob Azure à l'aide de flux de données. Vous devez avoir accès à un compte Stockage Blob Azure ou Azure Data Lake Store Gen2 pour lire un fichier Parquet, puis stocker les résultats dans des dossiers.

## <a name="prerequisites"></a>Prérequis
* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Azure Storage**. Vous utilisez le stockage ADLS comme magasins de données *source* et *récepteur*. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte de stockage Azure](../storage/common/storage-account-create.md) pour découvrir comment en créer un.

Les étapes de ce tutoriel supposent que vous avez 

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Au cours de cette étape, vous allez créer une fabrique de données et ouvrir l’interface utilisateur de Data Factory afin de créer un pipeline dans la fabrique de données.

1. Ouvrez **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Intégration** > **Data Factory**.
1. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**.
1. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données.
1. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :

    a. Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.
    
    b. Sélectionnez **Créer**, puis entrez le nom d'un groupe de ressources. Pour en savoir plus sur les groupes de ressources, consultez [Utiliser des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).
    
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
1. Dans la barre supérieure de la fabrique, faites glisser le curseur **Débogage du flux de données**. Le mode de débogage permet un test interactif de la logique de transformation sur un cluster Spark activé. Le préchauffage des clusters de flux de données nécessite 5 à 7 minutes et il est recommandé aux utilisateurs d’activer d’abord le débogage s’ils envisagent d’effectuer un développement de flux de données. Pour plus d’informations, consultez [Mode de débogage](concepts-data-flow-debug-mode.md).

    ![Activité de flux de données](media/tutorial-data-flow/dataflow1.png)
1. Dans le volet **Activités**, développez la section **Déplacer et transformer**. Faites glisser et déposez l’activité **Flux de données** à partir du volet vers le canevas du pipeline.

    ![Capture d’écran montrant le canevas du pipeline dans lequel vous pouvez supprimer l’activité Flux de données.](media/tutorial-data-flow/activity1.png)
1. Dans la fenêtre contextuelle **Ajout de flux de données**, sélectionnez **Créer un flux de données**, puis nommez votre flux de données **DeltaLake**. Une fois que vous avez fini, cliquez sur Terminer.

    ![Capture d’écran montrant où nommer votre flux de données lorsque vous en créez un.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Générer une logique de transformation dans le canevas de flux de données

Vous prendrez n'importe quelle donnée source (dans ce tutoriel, nous utiliserons un fichier Parquet) et utiliserez une transformation du récepteur pour déposer les données au format Parquet en utilisant les mécanismes les plus efficaces pour le processus ETL du lac de données.

![Flux final](media/data-flow/parts-final.png "Flux final")

### <a name="tutorial-objectives"></a>Objectifs du tutoriel

1. Choisir un de vos jeux de données sources dans un nouveau flux de données
1. Utiliser des flux de données pour partitionner efficacement votre jeu de données de récepteur
1. Déposer vos données partitionnées dans les dossiers du lac ADLS Gen2

### <a name="start-from-a-blank-data-flow-canvas"></a>Démarrer à partir d’un canevas de flux de données vide

Commençons par configurer l'environnement de flux de données correspondant à chacun des mécanismes décrits ci-dessous pour les données déposées dans ADLS Gen2.

1. Cliquez sur la transformation de la source.
1. Cliquez sur le nouveau bouton en regard du jeu de données dans le panneau inférieur.
1. Choisissez un jeu de données ou créez-en un nouveau. Pour cette démonstration, nous allons utiliser un jeu de données Parquet nommé Données utilisateur.
1. Ajoutez une transformation de colonne dérivée. Nous l'utiliserons pour définir dynamiquement les noms de dossier de votre choix.
1. Ajoutez une transformation du récepteur.
   
### <a name="hierarchical-folder-output"></a>Sortie de dossiers hiérarchiques

Il est très courant d'utiliser des valeurs uniques disponibles dans les données pour créer des hiérarchies de dossiers et partitionner les données au sein du lac. Il s'agit d'un moyen optimal d'organiser et de traiter les données au sein du lac et dans Spark (le moteur de calcul qui est derrière les flux de données). Cela dit, ce type d'organisation de votre production aura un léger coût en termes de performances. Attendez-vous à une légère diminution des performances globales du pipeline si vous utilisez ce mécanisme dans le récepteur.

1. Revenez au concepteur de flux de données et modifiez le flux de données créé ci-dessus. Cliquez sur la transformation du récepteur.
1. Cliquez sur Optimiser > Définir le partitionnement > Clé.
1. Sélectionnez la ou les colonnes que vous souhaitez utiliser pour définir votre structure hiérarchique de dossiers.
1. Notez que l'exemple ci-dessous utilise l'année et le mois comme colonnes pour la dénomination des dossiers. Les résultats seront des dossiers au format ```releaseyear=1990/month=8```.
1. Lorsque vous accédez aux partitions de données au sein d'une source de flux de données, vous pointez uniquement vers le dossier de niveau supérieur situé au dessus de ```releaseyear``` et utilisez un modèle de caractère générique pour chaque dossier suivant, par exemple : ```**/**/*.parquet```
1. Pour manipuler les valeurs des données, ou même si vous devez générer des valeurs synthétiques pour les noms de dossier, utilisez la transformation de colonne dérivée afin de créer les valeurs que vous souhaitez utiliser dans vos noms de dossier.

![Partitionnement de clés](media/data-flow/key-parts.png "Partitionnement de clés")
   
### <a name="name-folder-as-data-values"></a>Nommer le dossier d'après les valeurs des données

```Name folder as column data``` est une technique de récepteur légèrement plus performante pour les données de lac utilisant ADLS Gen2, mais elle n'offre pas le même avantage que le partitionnement clé/valeur. Alors que le style de partitionnement des clés de la structure hiérarchique vous permettra de traiter plus facilement les tranches de données, cette technique est une structure de dossiers aplatis qui permet d'écrire les données plus rapidement.

1. Revenez au concepteur de flux de données et modifiez le flux de données créé ci-dessus. Cliquez sur la transformation du récepteur.
1. Cliquez sur Optimiser > Définir le partitionnement > Utiliser le partitionnement actuel.
1. Cliquez sur Paramètres > Nommer le dossier d'après les données de la colonne.
1. Sélectionnez la colonne que vous souhaitez utiliser pour générer les noms de dossier.
1. Pour manipuler les valeurs des données, ou même si vous devez générer des valeurs synthétiques pour les noms de dossier, utilisez la transformation de colonne dérivée afin de créer les valeurs que vous souhaitez utiliser dans vos noms de dossier.

![Option de dossier](media/data-flow/folders.png "Dossiers")

### <a name="name-file-as-data-values"></a>Nommer le fichier d'après les valeurs des données

Les techniques répertoriées dans les tutoriels ci-dessus sont de bons cas d'usage pour créer des catégories de dossiers dans votre lac de données. Le schéma de dénomination de fichier par défaut utilisé par ces techniques consiste à utiliser l'ID de travail de l'exécuteur Spark. Parfois, vous pouvez souhaiter définir le nom du fichier de sortie dans un récepteur de texte de flux de données. Cette technique est uniquement suggérée pour les petits fichiers. Le processus de fusion des fichiers de partition en un seul fichier de sortie est un processus de longue haleine.

1. Revenez au concepteur de flux de données et modifiez le flux de données créé ci-dessus. Cliquez sur la transformation du récepteur.
1. Cliquez sur Optimiser > Définir le partitionnement > Partition unique. Cette exigence de partition unique crée un goulot d'étranglement dans le processus d'exécution lors de la fusion des fichiers. Cette option est uniquement recommandée pour les petits fichiers.
1. Cliquez sur Paramètres > Nommer le fichier d'après les données de la colonne.
1. Sélectionnez la colonne que vous souhaitez utiliser pour générer les noms de fichier.
1. Pour manipuler les valeurs des données, ou même si vous devez générer des valeurs synthétiques pour les noms de fichier, utilisez la transformation de colonne dérivée afin de créer les valeurs que vous souhaitez utiliser dans vos noms de fichier.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les [récepteurs de flux de données](data-flow-sink.md).
