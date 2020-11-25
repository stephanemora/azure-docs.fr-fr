---
title: Aide-mémoire – Azure Synapse Analytics (espaces de travail, préversion)
description: Guide de référence pour l’utilisateur d’Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: a592b1b160edef1ed1f41478187d989d087e9617
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844980"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Aide-mémoire sur Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

L’aide-mémoire sur Azure Synapse Analytics vous guide tout au long des concepts de base du service et des commandes importantes. Cet article est utile tant pour les débutants que pour les personnes qui souhaitent connaître les points essentiels des principales rubriques Azure Synapse.

## <a name="basics"></a>Informations de base

Un **espace de travail Synapse** est une limite de collaboration sécurisable pour l’analytique d’entreprise basée sur le cloud dans Azure. Un espace de travail est déployé dans une région spécifique et est associé à un compte ADLS Gen2 et à un système de fichiers (pour le stockage des données temporaires). Un espace de travail se trouve sous un groupe de ressources.

Un espace de travail vous permet d’effectuer des analyses avec SQL et Apache Spark. Les ressources disponibles pour les analyses SQL et Spark sont organisées en **pools** SQL et Spark. 

Un espace de travail peut contenir un nombre quelconque de **services liés**, essentiellement des chaînes de connexion qui définissent les informations de connexion nécessaires à l’espace de travail pour se connecter à des ressources externes.

## <a name="synapse-sql-terminology"></a>Terminologie Synapse SQL

**Synapse SQL** permet d’effectuer des analyses basées sur T-SQL dans un espace de travail Synapse. Synapse SQL dispose de deux modèles de consommation : dédié et serverless.  Pour le modèle dédié, utilisez des **pools SQL** dédiés. Un espace de travail peut avoir un nombre quelconque de ces pools. Pour utiliser le modèle serverless, utilisez les **pools SQL serverless**. Chaque espace de travail possède un de ces pools.

* **Demande SQL** – Opération telle qu’une requête exécutée par le biais d’un pool SQL dédié ou d’un pool SQL serverless.
* **Script SQL** – Ensemble de commandes SQL enregistrées dans un fichier. Un script SQL peut contenir une ou plusieurs instructions SQL. Il peut être utilisé pour exécuter des requêtes SQL par le biais d’un pool SQL dédié ou d’un pool SQL serverless.

## <a name="apache-spark-for-synapse-terminology"></a>Terminologie Apache Spark pour Synapse

Pour utiliser les analyses Spark, créez et utilisez des **pools Apache Spark serverless** dans votre espace de travail Synapse.


* **Apache Spark pour Synapse** – Runtime Spark utilisé dans un pool Spark serverless. La version actuelle prise en charge est Spark 2.4 avec Python 3.6.1, Scala 2.11.12, la prise en charge de .NET pour Apache Spark 0.5 et Delta Lake 0.3.  
* **Pool Apache Spark** – 0 à N ressources provisionnées Spark avec leurs bases de données correspondantes peuvent être déployées dans un espace de travail. Un pool Spark peut être mis en pause, repris et mis à l’échelle automatiquement.  
* **Application Spark** – Elle se compose d’un processus pilote et d’un ensemble de processus d’exécution. Une application Spark s’exécute sur un pool Spark serverless.            
* **Session Spark** – Point d’entrée unifié d’une application Spark. Elle offre un moyen d’interagir avec les différentes fonctionnalités de Spark et avec un nombre inférieur de constructions. Pour exécuter un notebook, vous devez créer une session. Une session peut être configurée pour s’exécuter sur un nombre spécifique d’exécuteurs d’une taille donnée. Par défaut, une session de notebook est configurée pour s’exécuter sur 2 exécuteurs de taille moyenne.
* **Notebook** – Interface d’ingénierie et de science des données interactive et réactive prenant en charge Scala, PySpark, C# et SparkSQL.
* **Définition d’un travail Spark** – Interface pour soumettre un travail Spark avec un fichier jar d’assembly contenant le code et ses dépendances.

## <a name="pipelines-terminology"></a>Terminologie des pipelines
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

