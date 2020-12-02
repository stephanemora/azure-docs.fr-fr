---
title: Terminologie - Azure Synapse Analytics (espaces de travail, préversion)
description: Guide de référence pour l’utilisateur d’Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c58ee46a608ccdcbb01a082ee278d9e0f8a07f6e
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030677"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologie Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Ce document présente les concepts de base d’Azure Synapse Analytics.

## <a name="basics"></a>Informations de base

Un **espace de travail Synapse** est une limite de collaboration sécurisable pour l’analytique d’entreprise basée sur le cloud dans Azure. Un espace de travail est déployé dans une région spécifique et est associé à un compte ADLS Gen2 et à un système de fichiers (pour le stockage des données temporaires). Un espace de travail se trouve sous un groupe de ressources.

Un espace de travail vous permet d’effectuer des analyses avec SQL et Apache Spark. Les ressources disponibles pour les analyses SQL et Spark sont organisées en **pools** SQL et Spark. 

## <a name="linked-services"></a>Services liés

Un espace de travail peut contenir un nombre quelconque de **services liés**, essentiellement des chaînes de connexion qui définissent les informations de connexion nécessaires à l’espace de travail pour se connecter à des ressources externes.

## <a name="synapse-sql"></a>SQL Synapse

**Synapse SQL** permet d’effectuer des analyses basées sur T-SQL dans un espace de travail Synapse. Synapse SQL dispose de deux modèles de consommation : dédié et serverless.  Pour le modèle dédié, utilisez des **pools SQL** dédiés. Un espace de travail peut avoir un nombre quelconque de ces pools. Pour utiliser le modèle serverless, utilisez les **pools SQL serverless**. Chaque espace de travail possède un de ces pools.

* **Demande SQL** – Opération telle qu’une requête exécutée par le biais d’un pool SQL dédié ou d’un pool SQL serverless.
* **Script SQL** – Ensemble de commandes SQL enregistrées dans un fichier. Un script SQL peut contenir une ou plusieurs instructions SQL. Il peut être utilisé pour exécuter des requêtes SQL par le biais d’un pool SQL dédié ou d’un pool SQL serverless.

## <a name="apache-spark-for-synapse"></a>Apache Spark pour Synapse

Pour utiliser les analyses Spark, créez et utilisez des **pools Apache Spark serverless** dans votre espace de travail Synapse. Lorsque vous commencez à utiliser un pool Spark, les espaces de travail créent une **session Spark** afin de gérer les ressources associées à cette session. 

Il existe deux façons d’utiliser Spark dans Synapse :
* **Notebooks Spark** pour l’ingénierie et la science des données avec Scala, PySpark, C# et SparkSQL
* **Définitions de travaux Spark** pour l’exécution de travaux Spark par lots à l’aide de fichiers jar.

Versions prises en charge :
* Spark 2.4
* Python 3.6.1+
* Scala 2.11.12
* .NET pour Apache Spark 1.0
* Delta Lake 0.3  

## <a name="pipelines"></a>Pipelines

* **Intégration de données** – Permet d’ingérer des données de diverses sources et d’orchestrer des activités exécutées dans un espace de travail ou hors d’un espace de travail.
* **Flux de données** – Offre une expérience entièrement visuelle sans qu’aucun code ne soit nécessaire pour effectuer une transformation de données volumineuses. L’optimisation et l’exécution sont entièrement gérées de manière serverless.
* **Pipeline** – Regroupement logique d’activités qui effectuent une tâche ensemble.
* **Activité** – Définit les actions à effectuer sur des données, telles que la copie de données ou l’exécution d’un notebook ou d’un script SQL.
* **Déclencheur** – Exécute un pipeline. Il peut être exécuté manuellement ou automatiquement (planification, fenêtre bascule ou événement).
* **Jeu de données d’intégration** – Vue de données nommée qui pointe ou référence simplement les données à utiliser dans une activité en guise d’entrée et de sortie. Elle appartient à un service lié.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Azure Synapse Analytics](get-started.md)
* [Créer un espace de travail](quickstart-create-workspace.md)
* [Utiliser un pool SQL serverless](quickstart-sql-on-demand.md)

