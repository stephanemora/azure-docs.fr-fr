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
ms.openlocfilehash: 774e503bec3f1f8c4cc5b85bb599230a3397f811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858436"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Aide-mémoire sur Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

L’aide-mémoire sur Azure Synapse Analytics vous guide tout au long des concepts de base du service et des commandes importantes. Cet article est utile tant pour les débutants que pour les personnes qui souhaitent connaître les points essentiels des principales rubriques Azure Synapse.

## <a name="basics"></a>Informations de base

Un **espace de travail Synapse** est une limite de collaboration sécurisable pour l’analytique d’entreprise basée sur le cloud dans Azure. Un espace de travail est déployé dans une région spécifique et est associé à un compte ADLS Gen2 et à un système de fichiers (pour le stockage des données temporaires). Un espace de travail se trouve sous un groupe de ressources.

Un espace de travail vous permet d’effectuer des analyses avec SQL et Apache Spark. Les ressources disponibles pour les analyses SQL et Spark sont organisées en **pools** SQL et Spark. 

## <a name="synapse-sql"></a>SQL Synapse
**Synapse SQL** permet d’effectuer des analyses basées sur T-SQL dans un espace de travail Synapse. Synapse SQL dispose de deux modèles de consommation : dédié et serverless.  Pour le modèle dédié, utilisez des **pools SQL** dédiés. Un espace de travail peut avoir un nombre quelconque de ces pools. Pour utiliser le modèle serverless, utilisez le pool SQL serverless appelé « SQL à la demande ». Chaque espace de travail possède un de ces pools.

## <a name="apache-spark-for-synapse"></a>Apache Spark pour Synapse
Pour utiliser les analyses Spark, créez et utilisez des **pools Spark** dans votre espace de travail Synapse.

## <a name="sql-terminology"></a>Terminologie SQL
| Terme                         | Définition      |
|:---                                 |:---                 |
| **Requête SQL**  |   Opération telle qu’une requête exécutée par le biais du pool SQL ou de SQL à la demande. |

## <a name="spark-terminology"></a>Terminologie Spark
| Terme                         | Définition      |
|:---                                 |:---                 |
|**Apache Spark pour Synapse** | Runtime Spark utilisé dans un pool Spark. La version actuelle prise en charge est Spark 2.4 avec Python 3.6.1, Scala 2.11.12, la prise en charge de .NET pour Apache Spark 0.5 et Delta Lake 0.3.  | 
| **Pool Apache Spark**  | 0 à N ressources provisionnées Spark avec leurs bases de données correspondantes peuvent être déployées dans un espace de travail. Un pool Spark peut être mis en pause, repris et mis à l’échelle automatiquement.  |
| **Application Spark**  |   Elle se compose d’un processus de pilote et d’un ensemble de processus d’exécuteur. Une application Spark s’exécute sur un pool Spark.            |
| **Session Spark**  |   Point d’entrée unifié d’une application Spark. Elle offre un moyen d’interagir avec les différentes fonctionnalités de Spark et avec un nombre inférieur de constructions. Pour exécuter un notebook, vous devez créer une session. Une session peut être configurée pour s’exécuter sur un nombre spécifique d’exécuteurs d’une taille donnée. Par défaut, une session de notebook est configurée pour s’exécuter sur 2 exécuteurs de taille moyenne. |
|**Intégration de données**| Permet d’ingérer des données de diverses sources et d’orchestrer des activités exécutées dans un espace de travail ou à l’extérieur d’un espace de travail.| 
|**Artefacts**| Concept qui englobe tous les objets nécessaires à un utilisateur pour les tâches de gestion de sources de données, de développement, d’orchestration et de visualisation.|
|**Notebook**| Interface d’ingénierie et de science des données interactive et réactive prenant en charge C#, Scala, PySpark et SparkSQL. |
|**Définition d’un travail Spark**|Interface pour soumettre un travail Spark avec un fichier jar d’assembly contenant le code et ses dépendances.|
|**Flux de données**|  Offre une expérience entièrement visuelle sans qu’aucun code ne soit nécessaire pour effectuer la transformation de Big Data. L’optimisation et l’exécution sont entièrement gérées de manière serverless. |
|**Script SQL**| Ensemble de commandes SQL enregistrées dans un fichier. Un script SQL peut contenir une ou plusieurs instructions SQL. Il peut être utilisé pour exécuter des requêtes SQL par le biais du pool SQL ou de SQL à la demande.|
|**Pipeline**| Regroupement logique d’activités qui exécutent une tâche ensemble.|
|**Activité**| Définit les actions à effectuer sur des données telles que la copie de données ou l’exécution d’un notebook ou d’un script SQL.|
|**Déclencheur**| Exécute un pipeline. Il peut être exécutée manuellement ou automatiquement (planification, fenêtre bascule ou événement).|
|**Service lié**| Chaînes de connexion qui définissent les informations de connexion nécessaires à l’espace de travail pour se connecter à des ressources externes.|
|**Dataset**|  Vue de données nommée qui pointe ou référence simplement les données à utiliser dans une activité en guise d’entrée et de sortie. Elle appartient à un service lié.|

## <a name="next-steps"></a>Étapes suivantes

- [Créer un espace de travail](quickstart-create-workspace.md)
- [Utiliser Synapse Studio](quickstart-synapse-studio.md)
- [Créer un pool SQL](quickstart-create-sql-pool-portal.md)
- [Créer un pool Apache Spark](quickstart-create-apache-spark-pool-portal.md)
- [Utiliser SQL à la demande](quickstart-sql-on-demand.md)

