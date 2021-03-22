---
title: Qu’est-ce qu’Azure Synapse Analytics ?
description: Vue d’ensemble d’Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/28/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 37768b994d4b61ee728e04352f027a6f5a478341
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031614"
---
# <a name="what-is-azure-synapse-analytics"></a>Qu’est-ce qu’Azure Synapse Analytics ?

En entreprise, l’analytique doit pouvoir traiter des quantités massives de données, qu’elles soient brutes, épurées ou très organisées. Les entreprises sont alors généralement obligées de combiner les technologies du Big Data et de l’entreposage de données dans des pipelines de données complexes qui fonctionnent sur des données de magasins relationnels et de lacs de données. Ces types de solutions sont difficiles à créer, à gérer et à sécuriser. Leur complexité retarde la livraison des insights dont les entreprises ont besoin.

**Azure Synapse** est un service d’analytique intégré qui accélère la génération d’insights dans les entrepôts de données et les systèmes de Big Data. Azure Synapse réunit ce qu’il se fait de mieux dans les technologies **SQL** utilisées dans l’entreposage des données d’entreprise, les **technologies Spark** utilisées pour le Big Data et les **pipelines** pour l’intégration des données et l’ETL/ELT. Il offre également un haut degré d’intégration à d’autres services Azure tels que **Power BI**, **CosmosDB** et **AzureML**.

## <a name="key-features--benefits"></a>Fonctionnalités et avantages clés

### <a name="industry-leading-sql"></a>SQL de pointe

* **Synapse SQL** est un système de requêtes distribuées pour T-SQL qui autorise les scénarios d’entreposage de données et de virtualisation de données et qui permet à T-SQL de gérer les scénarios de streaming et de machine learning.
* Synapse SQL offre à la fois des modèles de ressources **serverless** et **dédiés** pour fournir des options de consommation et de facturation adaptées à vos besoins. Pour bénéficier de performances et de coûts prévisibles, créez des pools SQL dédiés afin de réserver de la puissance de traitement aux données stockées dans les tables SQL. Pour des charges de travail non planifiées ou en rafale, utilisez le point de terminaison SQL serverless toujours disponible.
* Utilisez les fonctionnalités de **streaming** intégrées pour placer des données de sources de données cloud dans des tables SQL.
* Intégrez l’IA à SQL en utilisant des modèles **Machine Learning** pour effectuer le scoring des données avec la [fonction T-SQL PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true).

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

### <a name="built-in-data-integration-via-pipelines"></a>Intégration des données intégrée via des pipelines

Azure Synapse fournit par défaut le même moteur d’intégration de données et les mêmes expériences qu’Azure Data Factory, ce qui vous permet de créer des pipelines ETL à grande échelle sans quitter Azure Synapse Analytics.

* Ingérer des données depuis plus de 90 sources de données
* ETL sans code avec les activités de flux de données
* Orchestrer des notebooks, des travaux Spark, des procédures stockées, des scripts SQL, etc.

### <a name="unified-management-monitoring-and-security"></a>Gestion, supervision et sécurité unifiées

Azure Synapse offre aux entreprises une méthode unique pour gérer les ressources d’analytique, superviser l’utilisation et l’activité, et appliquer la sécurité.

* Affecter des utilisateurs à un rôle pour simplifier l’accès aux ressources d’unique
* Contrôle d’accès fin sur les données et le code
* Tableau de bord unique pour superviser les ressources, l’utilisation et les utilisateurs dans SQL et Spark

### <a name="unified-experience"></a>Expérience unifiée

**Synapse Studio** est l’expérience utilisateur qui associe tout pour les ingénieurs de données. Elle leur permet d’effectuer toutes les tâches nécessaires à l’élaboration d’une solution d’analyse complète.

* Principales tâches des ingénieurs de données à un même emplacement : ingérer, explorer, préparer, orchestrer, visualiser
* Productivité de pointe pour l’écriture de code SQL ou Spark : création, débogage et optimisation des performances
* Intégrer au processus CI/CD d’entreprise

## <a name="engage-with-the-synapse-engineering-team"></a>Collaborer avec l’équipe d’ingénierie de Synapse

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse) : posez vos questions sur le développement.
- [Page de questions Microsoft Q&A](/answers/topics/azure-synapse-analytics.html) : posez vos questions techniques.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Azure Synapse Analytics](get-started.md)
* [Créer un espace de travail](quickstart-create-workspace.md)
* [Utiliser un pool SQL serverless](quickstart-sql-on-demand.md)
