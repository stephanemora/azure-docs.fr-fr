---
title: Utiliser des flux de données pour traiter des données à partir de modèles Machine Learning automatisé (AutoML)
description: Découvrez comment utiliser les flux de données Azure Data Factory pour traiter les données à partir de modèles Machine Learning automatisé (AutoML).
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595377"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Traiter des données à partir de modèles Machine Learning automatisé à l’aide de flux de données

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Le Machine Learning automatisé (AutoML) est adopté par les projets de Machine Learning pour former, régler et obtenir automatiquement les meilleurs modèles en utilisant la métrique cible que vous spécifiez pour la classification, la régression et la prévision des séries chronologiques.

L’un des défis d’AutoML réside dans le fait que les données brutes d’un entrepôt de données ou d’une base de données transactionnelle constitueraient un jeu de données énorme, pouvant atteindre 10 Go. Un jeu de données volumineux nécessitant plus de temps pour effectuer l’apprentissage des modèles, nous vous recommandons d’optimiser le traitement des données avant d’effectuer l’apprentissage de vos modèles Azure Machine Learning. Ce tutoriel explique comment utiliser Azure Data Factory pour partitionner un jeu de données en fichiers AutoML pour un jeu de données Machine Learning.

Le projet AutoML comprend les trois scénarios de traitement de données suivants :

* Partitionner les données volumineuses en fichiers AutoML avant d’effectuer l’apprentissage des modèles.

     La [trame de données Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) est couramment utilisée pour traiter les données avant d’effectuer l'apprentissage de vos modèles. La trame de données Pandas fonctionne bien pour les données dont la taille est inférieure à 1 Go, mais si les données sont supérieures à 1 Go, une trame de données Pandas ralentit le traitement des données. Parfois, vous pouvez même recevoir un message d’erreur de mémoire insuffisante. Nous vous recommandons d’utiliser un format de [fichier Parquet](https://parquet.apache.org/) pour Machine Learning, car il s’agit d’un format binaire en colonnes.
    
     Les flux de données de mappage Data Factory sont des transformations de données conçues visuellement qui permettent aux ingénieurs de données de ne pas écrire de code. Les flux de données de mappage sont un moyen puissant pour traiter des données volumineuses, car le pipeline utilise des clusters Spark avec Scale-out.

* Fractionner le jeu de données d’apprentissage et le jeu de données de test.
    
    Le jeu de données d’apprentissage sera utilisé pour un modèle d’apprentissage. Le jeu de données de test sera utilisé pour évaluer les modèles dans un projet de Machine Learning. L’activité de fractionnement conditionnel des flux de données de mappage fractionnerait les données d’apprentissage et les données de test.

* Supprimer les données non qualifiées.

    Vous souhaiterez peut-être supprimer les données non qualifiées, comme un fichier Parquet avec zéro ligne. Dans ce tutoriel, nous allons utiliser l’activité Agréger pour obtenir le nombre de lignes. Le nombre de lignes est une condition pour supprimer les données non qualifiées.

## <a name="preparation"></a>Préparation

Utilisez la table Azure SQL Database suivante.

```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Convertir le format de données en Parquet

Le flux de données suivant convertit une table SQL Database au format de fichier Parquet :

- **Jeu de données source** : Table des transactions de SQL Database.
- **Jeu de données récepteur** : Stockage d’objets BLOB au format Parquet.

## <a name="remove-unqualified-data-based-on-row-count"></a>Supprimer les données non qualifiées en fonction du nombre de lignes

Supposons que nous devions supprimer un nombre de lignes inférieur à deux.

1. Utilisez l’activité Agréger pour obtenir le nombre de lignes. Utilisez **Regroupé par** basé sur Col2 et **Agrégats** avec `count(1)` pour le nombre de lignes.

    ![Capture d’écran montrant la configuration de l’activité Agréger pour obtenir le nombre de lignes.](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. À l’aide de l’activité de récepteur, sélectionnez le **Type de récepteur** comme **Cache** sous l’onglet **Récepteur**. Sélectionnez ensuite la colonne souhaitée dans la liste déroulante **Colonnes clés** de l’onglet **Paramètres**.

    ![Capture d’écran montrant la configuration de l’activité CacheSink pour obtenir le nombre de lignes d’un récepteur mis en cache.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Utilisez l’activité Colonne dérivée pour ajouter une colonne du nombre de lignes dans le flux source. Dans l’onglet **Paramètres de la colonne dérivée** , utilisez l'expression `CacheSink#lookup` pour obtenir le nombre de lignes de CacheSink.

    ![Capture d’écran montrant la configuration de l’activité Colonne dérivée pour ajouter le nombre de lignes dans source1.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Utilisez l’activité Fractionnement conditionnel pour supprimer les données non qualifiées. Dans cet exemple, le nombre de lignes est basé sur la colonne Col2. La condition est de supprimer un nombre de lignes inférieur à deux. Deux lignes (ID=2 et ID=7) seront ainsi supprimées. Vous pouvez enregistrer des données non qualifiées dans un stockage d’objets BLOB pour la gestion des données.

    ![Capture d’écran montrant la configuration de l’activité Fractionnement conditionnel pour récupérer des données supérieures ou égales à deux.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Créez une nouvelle source pour obtenir le nombre de lignes qui sera utilisé dans la source d’origine dans des étapes ultérieures.
>    * Utilisez CacheSink du point de vue des performances.

## <a name="split-training-data-and-test-data"></a>Fractionner les données de formation et les données de test

Nous voulons fractionner les données d’apprentissage et les données de test pour chaque partition. Dans cet exemple, pour la même valeur de Col2, récupérez les deux premières lignes comme données de test et les autres lignes comme données d’apprentissage.

1. Utilisez l’activité Fenêtre pour ajouter un numéro de ligne de colonne pour chaque partition. Sous l’onglet **Au-dessus de**, sélectionnez une colonne de partition. Dans ce tutoriel, nous allons partitionner pour Col2. Indiquez un ordre dans l’onglet **Trier** qui, dans ce tutoriel, sera basé sur l’ID. Indiquez un ordre dans l’onglet **Colonnes de fenêtre** pour ajouter une colonne de nombre de lignes pour chaque partition.

    ![Capture d’écran montrant la configuration de l’activité Fenêtre pour ajouter une nouvelle colonne de nombre de lignes.](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Utilisez l’activité Fractionnement conditionnel pour fractionner les deux premières lignes de chaque partition dans le jeu de données de test et les lignes restantes dans le jeu de données d’apprentissage. Dans l’onglet **Paramètres de fractionnement conditionnel**, utilisez l’expression `lesserOrEqual(RowNum,2)` comme condition.

    ![Capture d’écran montrant la configuration de l’activité Fractionnement conditionnel pour fractionner le jeu de données actuel entre le jeu de données d’apprentissage et le jeu de données de test.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Partitionner les jeux de données d’apprentissage et de test avec le format Parquet

À l’aide de l’activité Récepteur, sous l’onglet **Optimiser**, utilisez **Valeur unique par partition** pour définir une colonne comme clé de colonne pour la partition.

![Capture d’écran montrant la configuration de l’activité Récepteur pour définir la partition du jeu de données d’apprentissage.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Revenons sur toute la logique du pipeline.

![Capture d’écran qui montre la logique de l’ensemble du pipeline.](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Étapes suivantes

Créez le reste de votre logique de flux de données à l’aide de [transformations](concepts-data-flow-overview.md) de flux de données de mappage.
