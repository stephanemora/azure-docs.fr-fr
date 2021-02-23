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
ms.openlocfilehash: 494fc2c227b6fe855e96a780d43cc6702722fa94
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520735"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>Traiter des données à partir de modèles Machine Learning automatisé (AutoML) à l’aide d’un flux de données

Le Machine Learning automatisé (AutoML) est adopté par les projets de Machine Learning pour former, régler et obtenir automatiquement le meilleur modèle en utilisant la métrique cible que vous spécifiez pour la classification, la régression et la prévision des séries chronologiques. 

L’un des défis est que les données brutes provenant d’un entrepôt de données ou d’une base de données transactionnelle représenteraient d’énormes jeux de données, par exemple : 10 Go. Un jeu de données volumineux nécessitant plus de temps pour effectuer l’apprentissage des modèles, il est recommandé d’optimiser le traitement des données avant d’effectuer l’apprentissage des modèles Azure Machine Learning. Ce tutoriel explique comment utiliser ADF pour partitionner le jeu de données en fichiers Parquet pour le jeu de données Azure Machine Learning. 

Dans le projet de Machine Learning automatisé (AutoML), il appliquera les trois scénarios de traitement de données suivants :

* Partitionner les données volumineuses en fichiers Parquet avant la formation des modèles. 

     La [trame de données Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) est couramment utilisée pour traiter les données avant la formation des modèles. La trame de données Pandas fonctionne bien pour les tailles de données inférieures à 1 Go, mais si les données sont plus volumineuses, la trame de données Pandas ralentit le traitement des données, et parfois même émet un message d’erreur de mémoire. Les formats de [fichier Parquet](https://parquet.apache.org/) sont recommandés pour le Machine Learning dans la mesure où il s’agit d’un format binaire en colonnes.
    
    Les flux de données de mappage des fabriques de données Azure sont des transformations de données conçues visuellement et sans code pour les ingénieurs de données. Il s’agit d’un outil puissant pour traiter des données volumineuses dans la mesure où le pipeline utilise des clusters Spark avec Scale-out.

* Fractionner le jeu de données de formation et le jeu de données de test.
    
    Le jeu de données de formation sera utilisé pour effectuer l’apprentissage du modèle, tandis que le jeu de données de test sera utilisé pour évaluer les modèles dans le projet de Machine Learning. L’activité de fractionnement conditionnel des flux de données de mappage fractionnerait les données de formation et les données de test. 

* Supprimer les données non qualifiées.

    Vous pouvez supprimer les données non qualifiées, par exemple : fichier Parquet avec zéro ligne. Dans ce tutoriel, nous allons utiliser l’activité Agrégat pour obtenir le nombre de lignes ; le nombre de lignes sera une condition pour supprimer les données non qualifiées. 


## <a name="preparation"></a>Préparation
Utilisez la table suivante d’Azure SQL Database. 
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

Le flux de données convertit une table d’Azure SQL Database au format de fichier Parquet. 

**Jeu de données source** : Table des transactions d’Azure SQL Database

**Jeu de données récepteur** : Stockage blob au format Parquet


## <a name="remove-unqualified-data-based-on-row-count"></a>Supprimer les données non qualifiées en fonction du nombre de lignes

Supposons que nous supprimions le nombre de lignes inférieur à 2. 

1. Utilisez l’activité Agrégat pour calculer le nombre de lignes : **Regrouper par** sur Col2 et **Agrégats** avec count(1) pour le nombre de lignes. 

    ![Configurer l’activité Agrégat pour calculer le nombre de lignes](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Utilisez l’activité Récepteur, choisissez **Type de récepteur** comme cache dans l’onglet **Récepteur**, puis sélectionnez la colonne souhaitée dans la liste déroulante des **colonnes clés** dans l’onglet **Paramètres**. 

    ![Configurer l’activité CacheSink pour calculer le nombre de lignes dans le récepteur mis en cache](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Utilisez l’activité de colonne dérivée pour ajouter la colonne de nombre de lignes dans le flux source. Dans l’onglet **Paramètres de la colonne dérivée**, utilisez l’expression CacheSink#lookup pour obtenir le nombre de lignes de SinkCache.
    ![Configurer l’activité de colonne dérivée pour ajouter le nombre de lignes dans la source 1](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Utilisez l’activité de fractionnement conditionnel pour supprimer les données non qualifiées. Dans cet exemple, le nombre de lignes est basé sur la colonne Col2 et la condition est de supprimer le nombre de lignes inférieur à 2 ; par conséquent, deux lignes (ID=2 et ID=7) sont supprimées. Vous pouvez enregistrer des données non qualifiées dans un stockage blob pour la gestion des données. 

    ![Configurer l’activité de fractionnement conditionnel pour récupérer des données supérieures ou égales à 2](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    Créez une nouvelle source pour obtenir le nombre de lignes qui sera utilisé dans la source d’origine dans des étapes ultérieures. 
>    *    Utilisez CacheSink du point de vue des performances. 

## <a name="split-training-data-and-test-data"></a>Fractionner les données de formation et les données de test 

1. Nous voulons fractionner les données de formation et les données de test pour chaque partition. Dans cet exemple, pour la même valeur de Col2, récupérez les deux premières lignes comme données de test et les autres lignes comme données de formation. 

    Utilisez l’activité Fenêtre pour ajouter un numéro de ligne de colonne pour chaque partition. Dans l’onglet **Sur**, choisissez la colonne de la partition (dans ce tutoriel, la partition sera Col2), donnez l’ordre dans l’onglet **Tri** (dans ce tutoriel, l’ordre sera basé sur l’ID) et dans l’onglet **Colonnes de la fenêtre** pour ajouter une colonne comme numéro de ligne pour chaque partition. 
    ![Configurer l’activité Fenêtre pour ajouter une nouvelle colonne en tant que numéro de ligne](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Utilisez l’activité de fractionnement conditionnel pour fractionner les deux premières lignes de chaque partition en jeu de données de test, et les lignes restantes en jeu de données de formation. Dans l’onglet **Paramètres du fractionnement conditionnel**, utilisez l’expression lesserOrEqual(RowNum,2) comme condition. 

    ![Configurer l’activité de fractionnement conditionnel pour fractionner le jeu de données actuel en jeu de données de formation et en jeu de données de test](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>Partitionner le jeu de données de formation et le jeu de données de test avec le format Parquet

1. Utilisez l’activité Récepteur, sous l’onglet **Optimiser**, en utilisant **Valeur unique par partition** pour définir une colonne comme clé de colonne pour la partition. 
    ![Configurer l’activité Récepteur pour définir la partition du jeu de données de formation](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    Revenons sur toute la logique du pipeline.
    ![Logique de l’ensemble du pipeline](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>Étapes suivantes

* Créez le reste de votre logique de flux de données à l’aide de [transformations](concepts-data-flow-overview.md) de flux de données de mappage.
