---
title: Qu’est-ce qu’Azure Synapse Analytics ?
description: Vue d’ensemble d’Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/24/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: bb09a25d7ea242dfafd7d853015ae8e127b13883
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383403"
---
# <a name="what-is-azure-synapse-analytics"></a>Qu’est-ce qu’Azure Synapse Analytics ?

**Azure Synapse** est un service d’analyse pour entreprise qui raccourcit le délai d’obtention d’analyses sur l’ensemble des entrepôts de données et des systèmes Big Data. Azure Synapse réunit ce qu’il se fait de mieux dans les technologies **SQL** utilisées dans l’entreposage des données d’entreprise, les **technologies Spark** utilisées pour le Big Data et les **pipelines** pour l’intégration des données et l’ETL/ELT. Il offre également un haut degré d’intégration à d’autres services Azure tels que **Power BI**, **CosmosDB** et **AzureML**.

![Diagramme de l'architecture d'Azure Synapse Analytics.](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>SQL de pointe

**Synapse SQL** est un système de requêtes distribuées pour T-SQL qui autorise les scénarios d’entreposage de données et de virtualisation de données et qui permet à T-SQL de gérer les scénarios de streaming et de machine learning.

* Synapse SQL propose des modèles de ressources à la fois **serverless** et **dédiés**. Pour bénéficier de performances et de coûts prévisibles, créez des pools SQL dédiés afin de réserver de la puissance de traitement aux données stockées dans les tables SQL. Pour des charges de travail non planifiées ou en rafale, utilisez le point de terminaison SQL serverless toujours disponible.
* Utilisez les fonctionnalités de **streaming** intégrées pour placer des données de sources de données cloud dans des tables SQL.
* Intégrez l’IA à SQL en utilisant des modèles **Machine Learning** pour effectuer le scoring des données avec la [fonction T-SQL PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="industry-standard-apache-spark"></a>Apache Spark standard

**Apache Spark pour Azure Synapse** offre une intégration profonde et fluide avec Apache Spark, le moteur de Big Data open source le plus répandu dans les domaines de la préparation des données, de l’engineering données, des processus ETL (extraction, transformation et chargement) et de l’apprentissage automatique.

* Modèles ML avec algorithmes SparkML et intégration d’AzureML pour Apache Spark 2.4 avec prise en charge intégrée de Linux Foundation Delta Lake.
* Modèle de ressource simplifié qui vous évite d’avoir à vous soucier de la gestion des clusters.
* Démarrage rapide Spark et mise à l’échelle automatique agressive.
* Prise en charge intégrée de .NET pour Spark, ce qui vous permet de mettre à profit votre expertise en C# et votre code .NET existant au sein d’une application Spark.

## <a name="working-with-your-data-lake"></a>Travailler avec votre lac de données

Azure Synapse supprime les barrières technologiques traditionnelles qui empêchent d’utiliser conjointement SQL et Spark. Vous pouvez mélanger les fonctions de façon fluide selon vos besoins et votre expertise.

* Les tables définies sur les fichiers du lac de données sont utilisées de manière transparente par Spark ou Hive.
* SQL et Spark peuvent explorer et analyser directement des fichiers Parquet, CSV, TSV et JSON stockés dans le lac de données.
* Chargement de données rapide et évolutif entre les bases de données SQL et Spark

## <a name="built-in-data-integration"></a>Intégration de données prédéfinie

Azure Synapse contient le même moteur et les mêmes expériences d’intégration des données qu’Azure Data Factory, ce qui vous permet de créer des pipelines ETL enrichis à l’échelle sans quitter Azure Synapse Analytics.

* Ingérer des données depuis plus de 90 sources de données
* ETL sans code avec les activités de flux de données
* Orchestrer des notebooks, des travaux Spark, des procédures stockées, des scripts SQL, etc.

## <a name="unified-management-monitoring-and-security"></a>Gestion, supervision et sécurité unifiées

Azure Synapse offre aux entreprises une méthode unique pour gérer les ressources d’analytique, superviser l’utilisation et l’activité, et appliquer la sécurité.

* Affecter des utilisateurs à un rôle pour simplifier l’accès aux ressources d’unique
* Contrôle d’accès fin sur les données et le code
* Tableau de bord unique pour superviser les ressources, l’utilisation et les utilisateurs dans SQL et Spark

## <a name="unified-experience"></a>Expérience unifiée

**Synapse Studio** est l’expérience utilisateur qui associe tout pour les ingénieurs de données. Elle leur permet d’effectuer toutes les tâches nécessaires à l’élaboration d’une solution d’analyse complète.

* Réalisation à partir du même emplacement des principales tâches qui incombent aux ingénieurs de données : ingérer, explorer, préparer, orchestrer, visualiser
* Productivité de pointe pour l’écriture de code SQL ou Spark : création, débogage et optimisation des performances
* Intégrer au processus CI/CD d’entreprise

## <a name="engage-with-the-synapse-community"></a>Impliquez-vous dans la communauté Synapse

- [Microsoft Q&A](/answers/topics/azure-synapse-analytics.html) : posez vos questions techniques
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse) : posez vos questions sur le développement.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Azure Synapse Analytics](get-started.md)
* [Créer un espace de travail](quickstart-create-workspace.md)
* [Utiliser un pool SQL serverless](quickstart-sql-on-demand.md)
