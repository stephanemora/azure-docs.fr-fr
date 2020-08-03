---
title: 'Tutoriel : Bien démarrer avec Azure Synapse Analytics'
description: Dans ce tutoriel, vous allez découvrir les étapes de base à suivre pour configurer et utiliser Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075866"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Bien démarrer avec Azure Synapse Analytics

Ce tutoriel est un guide pas à pas des principaux domaines de fonctionnalité d’Azure Synapse Analytics. Ce tutoriel est le point de départ idéal pour quelqu’un qui souhaite une visite guidée des principaux scénarios d’Azure Synapse Analytics. Après avoir suivi les étapes du tutoriel, vous disposerez d’un espace de travail Synapse pleinement fonctionnel dans lequel vous pourrez commencer à analyser des données à l’aide de SQL, de SQL à la demande et d’Apache Spark.

Vous apprendrez à effectuer les opérations suivantes :
* Provisionner un espace de travail Synapse dans un abonnement Azure
* Configurer le contrôle d’accès sur un compte ADLSGEN2 pour qu’il fonctionne en toute transparence avec l’espace de travail Synapse
* Charger l’exemple de données NYCTaxi dans l’espace de travail Synapse pour qu’il puisse être utilisé par SQL, SQL à la demande et Spark
* Modifier et exécuter des scripts SQL et des notebooks Synapse à l’aide de Synapse Studio
* Interroger des tables SQL et des tables Spark
* Charger des données à partir de tables SQL dans des DataFrames Spark
* Charger des données dans des tables SQL à partir de DataFrames Spark
* Explorer le contenu d’un compte ADLSGEN2
* Analyser des fichiers de données Parquet dans des comptes ADLSGEN2 à l’aide de Spark et de SQL à la demande 
* Générer un pipeline de données qui exécute automatiquement un notebook Synapse toutes les heures

Suivez les étapes *dans l’ordre* indiqué ci-dessous et vous examinerez la plupart des fonctionnalités et apprendrez à tester ses fonctionnalités de base.

* [ÉTAPE 1 – Créer et configurer un espace de travail Synapse](get-started-create-workspace.md)
* [ÉTAPE 2 – Analyser avec un pool SQL](get-started-analyze-sql-pool.md)
* [ÉTAPE 3 – Analyser avec Spark](get-started-analyze-spark.md)
* [ÉTAPE 4 – Analyser avec SQL à la demande](get-started-analyze-sql-on-demand.md)
* [ÉTAPE 5 – Analyser des données dans un compte de stockage](get-started-analyze-storage.md)
* [ÉTAPE 6 – Orchestrer avec des pipelines](get-started-pipelines.md)
* [ÉTAPE 7 – Visualiser des données avec Power BI](get-started-visualize-power-bi.md)
