---
title: FAQ - Azure Synapse Analytics (espaces de travail, préversion)
description: FAQ pour Azure Synapse Analytics (espaces de travail, préversion)
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 530ebe09ee0ca44b0a5fbd4b8da2784e2c7ae7ea
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843891"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Questions fréquentes sur Azure Synapse Analytics (espace de travail, préversion)

Ce guide recense les questions les plus fréquemment posées sur Synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Général

### <a name="q-what-is-azure-synapse-analytics"></a>Q : Qu’est-ce qu’Azure Synapse Analytics ?

A : Azure Synapse est une plateforme de données intégrée pour le décisionnel, l’IA et l’intelligence continue. Elle connecte plusieurs runtimes d’analytique tels que SQL et Apache Spark par le biais d’une plateforme unique qui offre un moyen unifié d’effectuer les opérations suivantes :

- Sécuriser vos ressources d’analytique, y compris le réseau, en gérant l’accès avec authentification unique aux artefacts de pool, de données et de développement.
- Superviser facilement les événements qui se produisent dans les activités de votre espace de travail sur n’importe quelle couche, optimiser ces événements, y réagir et les déboguer rapidement.
- Gérer vos métadonnées entre les moteurs. Créer une table de pool Apache Spark serverless, qui sera automatiquement disponible dans vos bases de données Azure Synapse.
- Interagir avec les données par le biais d’une expérience utilisateur unifiée. Synapse Studio réunit les développeurs de Big Data, les ingénieurs de données, les administrateurs de bases de données, les analystes de données et les scientifiques des données sur la même plateforme.

Pour plus d’informations, consultez l’article [Qu’est-ce qu’Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics/overview-what-is).

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>Q : Quels sont les principaux composants d’Azure Synapse Analytics ?

A : Azure Synapse a les fonctionnalités suivantes :

- Fonctionnalités d’analytique, proposées par le biais d’un pool SQL dédié ou d’un pool SQL serverless (préversion).
- Pool Apache Spark serverless (préversion) avec prise en charge complète de Scala, Python, Spark, SQL et C#
- Flux de données offrant une expérience de transformation de Big Data sans code
- Intégration et orchestration de données pour intégrer vos données et mettre en œuvre tout le développement de votre code
- Studio pour accéder à toutes ces fonctionnalités par le biais d’une seule interface utilisateur web

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>Q : Quel est le rapport entre Azure Synapse Analytics et Azure SQL Data Warehouse ?

A : Azure Synapse Analytics est une évolution d’Azure SQL Data Warehouse vers une plateforme d’analytique, où le pool SQL dédié fait office de solution d’entrepôt de données. Cette plateforme combine l’exploration de données, l’ingestion, la transformation, la préparation et une couche d’analytique de service.

## <a name="use-cases"></a>Cas d'utilisation

### <a name="q-how-do-i-rename-a-published-artifact-dataset-notebook-sql-script-and-so-on-in-azure-synapse"></a>Q : Comment renommer un artefact publié (jeu de données, notebook, script SQL, etc.) dans Azure Synapse ?

A : Pour renommer un fichier d’artefact publié, commencez par cloner le fichier et renommez le nouveau fichier à votre convenance. Vous devrez mettre à jour manuellement toutes les références de l’artefact avec le nouveau nom de fichier et supprimer l’ancien.

### <a name="q-what-is-a-good-use-case-for-dedicated-sql-pool"></a>Q : Qu’est-ce qu’un bon cas d’usage pour le pool SQL dédié ?

A : Le pool SQL dédié est au centre de vos besoins d’entreposage de données. Il s’agit de la solution d’entreposage de données offrant le meilleur rapport [prix/performances](https://azure.microsoft.com/services/sql-data-warehouse/compare/). Le pool SQL dédié est la solution d’entreposage de données cloud leader du secteur, car vous pouvez :

- Servir une grande variété de charges de travail sans impact sur les performances grâce à une concurrence élevée et à une isolation des charges de travail
- Sécuriser vos données facilement grâce à des fonctionnalités avancées allant de la sécurité réseau à l’accès précis
- Tirer parti d’un large éventail d’écosystèmes

### <a name="q-what-is-a-good-use-case-for-serverless-apache-spark-pool-in-azure-synapse"></a>Q : Qu’est-ce qu’un bon cas d’usage pour le pool Apache Spark serverless dans Azure Synapse ?

A : Notre premier objectif est de procurer une expérience exceptionnelle d’Engineering données pour la transformation de données du lac en lot ou dans un flux. Son intégration étroite et simple à notre orchestration de données facilite la mise en œuvre de votre travail de développement.

Un autre cas d’usage pour Apache Spark est qu’un scientifique des données peut :

- Extraire une caractéristique
- Explorer des données
- Entraîner un modèle

### <a name="q-what-is-a-good-use-case-for-serverless-sql-pool-in-azure-synapse"></a>Q : Qu’est-ce qu’un bon cas d’usage pour le pool SQL serverless dans Azure Synapse ?

A : Le pool SQL serverless est un service de requête sur les données de votre lac de données. Il vous permet de démocratiser l’accès à toutes vos données en fournissant une syntaxe T-SQL familière pour interroger les données en place, sans avoir besoin de copier ou de charger des données dans un magasin spécialisé.

Voici quelques exemples de cas d’usage :

- Découverte et exploration de base : permet aux analystes de données, aux ingénieurs de données et aux scientifiques des données émergents d’obtenir facilement leur premier insight issu des données qui se trouvent dans leur lac de données avec un schéma sur les requêtes T-SQL de lecture
- Entrepôt de données logique : les analystes de données peuvent exploiter l’expressivité complète du langage T-SQL pour interroger et analyser directement les données résidant dans le Stockage Azure et utiliser des outils décisionnels familiers (par exemple, Azure Analysis Services, Power BI Premium, etc.) afin d’actualiser les tableaux de bord en réexécutant les requêtes Starlight Query
- ETL de type « requête unique » : permet aux ingénieurs de données de convertir les données basées sur le Stockage Azure d’un format en un autre, de les filtrer, agréger, etc. d’une manière rappelant le traitement parallèle massif, de conserver les résultats des requêtes dans le Stockage Azure et de les rendre immédiatement disponibles pour un traitement ultérieur dans Starlight Query ou d’autres services dignes d’intérêt

### <a name="q-what-is-a-good-use-case-for-data-flow-in-azure-synapse"></a>Q : Qu’est-ce qu’un bon cas d’usage de flux de données dans Azure Synapse ?

A : Un flux de données permet aux ingénieurs de données de développer une logique de transformation graphique des données sans rédiger de code. Les flux de données résultants sont exécutés en tant qu’activités au sein de l’intégration et de l’orchestration de données. Les activités de flux de données peuvent être mises en œuvre à l’aide de fonctionnalités de planification, de contrôle, de flux et de supervision existantes.

## <a name="security-and-access"></a>Sécurité et accès

A : L’expérience de l’authentification unique de bout en bout est un processus d’authentification important dans Synapse Analytics. La gestion et le passage de l’identité via une intégration Azure AD complète sont indispensables.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>Q : Comment accéder aux fichiers et aux dossiers dans ADLS Gen2 ?

A : L’accès aux fichiers et aux dossiers est géré par le biais d’ADLS Gen2. Pour plus d’informations, consultez [Contrôle d’accès au stockage dans Data Lake](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>Q : Puis-je utiliser des outils décisionnels tiers pour accéder à Azure Synapse Analytics ?

A : Oui, vous pouvez utiliser vos applications métier tierces, telles que Tableau et Power BI, pour vous connecter au pool SQL dédié et au pool SQL serverless. Spark prend en charge IntelliJ.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Azure Synapse Analytics](get-started.md)
* [Créer un espace de travail](quickstart-create-workspace.md)
* [Utiliser un pool SQL serverless](quickstart-sql-on-demand.md)
