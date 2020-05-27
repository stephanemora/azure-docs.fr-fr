---
title: Qu’est-ce qu’Azure Synapse Analytics ?
description: Vue d’ensemble d’Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 5c458b1c04a7f3be1a43d725591426e619286b7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587871"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Présentation d’Azure Synapse Analytics (espaces de travail, préversion)

[!INCLUDE [preview](includes/note-preview.md)]

Aujourd’hui, l’analytique d’entreprise doit pouvoir traiter des quantités massives de données, qu’elles soient brutes, épurées ou très organisées. Avant, pour créer ces types de solutions analytiques, les entreprises devaient combiner le Big Data à des technologies d’entreposage de données comme Spark et SQL. Elles devaient ensuite les intégrer à des pipelines avancés capables de traiter les données dans des magasins relationnels et des lacs de données.  

De telles solutions sont difficiles à créer, configurer, sécuriser et gérer, ce qui retarde la possibilité d’extraire des insights intéressants.

**Azure Synapse** est un service d’analytique intégré qui accélère la génération d’insights à partir d’un ensemble de données provenant d’entrepôts de données et de systèmes d’analytique de Big Data, quelle que soit l’échelle. Il réunit ce qu’il se fait de mieux dans les technologies **SQL** utilisées dans l’entreposage des données d’entreprise, les **technologies Spark** utilisées dans l’analytique du Big Data et les **pipelines** pour orchestrer les activités et le déplacement des données.

Azure Synapse offre aux utilisateurs une expérience native sur le web de type **Studio** qui propose un environnement et un modèle uniques pour la gestion, la supervision, le codage et la sécurité.

Azure Synapse fournit aux entreprises le moyen le plus simple et le plus rapide de collecter des insights sur toutes leurs données, quelle que soit leur taille, à l’aide d’outils d’analytique qu’elles connaissent bien. Du fait de son intégration étroite à **Power BI**, les ingénieurs de données peuvent créer des solutions d’analytique qui fonctionnent de bout en bout pour le décisionnel.

Azure Synapse facilite par ailleurs la génération de modèles prédictifs et l’analytique avancée avec le Machine Learning grâce à sa prise en charge intégrée d’**AzureML**.

## <a name="key-features--benefits"></a>Fonctionnalités et avantages clés

### <a name="industry-leading-sql"></a>SQL de pointe

* **SQL Synapse** est un système de requêtes distribuées qui permet aux entreprises d’implémenter des scénarios d’entreposage et de virtualisation de données à l’aide d’expériences T-SQL standard bien connues des ingénieurs de données. Il étend également les capacités de SQL pour prendre en charge les scénarios de streaming et de Machine Learning.

* SQL Synapse offre des modèles de ressources **serverless** et **provisionnés** pour fournir des options de consommation et de facturation adaptées à vos besoins. Pour bénéficier de performances et de coûts prévisibles, provisionnez des pools afin de réserver de la puissance de traitement aux données stockées dans les tables SQL. Pour des charges de travail non planifiées ou en rafale, utilisez le point de terminaison SQL serverless toujours disponible.
* Utilisez les fonctionnalités de **streaming** intégrées pour placer des données de sources de données cloud dans des tables SQL.
* Intégrez l’IA à SQL en utilisant des modèles **Machine Learning** pour effectuer le scoring des données avec la fonction T-SQL PREDICT.

### <a name="industry-standard-apache-spark"></a>Apache Spark standard

**Apache Spark pour Azure Synapse** offre une intégration profonde et fluide avec Apache Spark, le moteur de Big Data open source le plus répandu dans les domaines de la préparation des données, de l’engineering données, des processus ETL (extraction, transformation et chargement) et de l’apprentissage automatique.

* Modèles ML avec algorithmes SparkML et intégration d’AzureML pour Apache Spark 2.4 avec prise en charge intégrée de Linux Foundation Delta Lake.
* Modèle de ressource simplifié qui vous évite d’avoir à vous soucier de la gestion des clusters.
* Démarrage rapide Spark et mise à l’échelle automatique agressive.
* Prise en charge intégrée de .NET pour Spark, ce qui vous permet de mettre à profit votre expertise en C# et votre code .NET existant au sein d’une application Spark.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Interop de SQL et Apache Spark sur votre lac de données

Azure Synapse supprime les barrières technologiques traditionnelles qui empêchent d’utiliser conjointement SQL et Spark. Vous pouvez mélanger les fonctions de façon fluide selon vos besoins et votre expertise.

* Un système de métadonnées compatible Hive partagé permet aux tables définies sur des fichiers dans le lac de données d’être consommées de manière homogène par Spark ou Hive.
* SQL et Spark peuvent explorer et analyser directement des fichiers Parquet, CSV, TSV et JSON stockés dans le lac de données.
* Chargement et déchargement scalables rapides pour les données passant entre les bases de données SQL et Spark

### <a name="built-in-orchestration-via-pipelines"></a>Orchestration intégrée au moyen de pipelines

Azure Synapse fournit par défaut le même moteur d’intégration de données et les mêmes expériences qu’Azure Data Factory, ce qui vous permet de créer des pipelines de données avancés sans recourir à un moteur d’orchestration distinct.

* Déplacer des données entre Synapse et 85+ sources de données locales
* Orchestrer des notebooks, des pipelines, des travaux Spark, des scripts SQL, des procédures stockées
* ETL sans code avec les activités de flux de données

### <a name="unified-management-monitoring-and-security"></a>Gestion, supervision et sécurité unifiées

Azure Synapse offre aux entreprises une méthode unique pour gérer les ressources d’analytique, superviser l’utilisation et l’activité, et appliquer la sécurité.

* Affecter des utilisateurs à un rôle pour simplifier l’accès aux ressources d’unique
* Contrôle d’accès fin sur les données et le code
* Tableau de bord unique pour superviser les ressources, l’utilisation et les utilisateurs dans SQL et Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** est une expérience native dans le web qui regroupe dans un seul et même endroit tout ce dont les ingénieurs de données ont besoin pour effectuer les tâches nécessaires à la création d’une solution complète.

* Créer une solution d’analytique de bout en bout en un seul endroit : ingestion, exploration, préparation, orchestration, visualisation
* Productivité de pointe pour les ingénieurs de données écrivant du code SQL ou Spark : création, débogage et optimisation des performances
* Intégrer aux processus CI/CD d’entreprise

## <a name="next-steps"></a>Étapes suivantes

* [Créer un espace de travail](quickstart-create-workspace.md)
* [Utiliser Synapse Studio](quickstart-synapse-studio.md)
* [Créer un pool SQL](quickstart-create-sql-pool.md)
* [Utiliser SQL à la demande](quickstart-sql-on-demand.md)
* [Créer un pool Apache Spark](quickstart-create-apache-spark-pool.md)
