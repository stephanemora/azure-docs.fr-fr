---
title: Migrer Azure Data Lake Analytics vers Azure Synapse Analytics.
description: Cet article explique comment migrer d’Azure Data Lake Analytics vers Azure Synapse Analytics.
author: lingluo0531
ms.author: lingluo
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: migrate-azure-data-lake-analytics-to-synapse
ms.date: 08/25/2021
ms.openlocfilehash: 34a199fe8f488add4f1f2cdd9357dd9ac718d4f0
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254972"
---
# <a name="migrate-azure-data-lake-analytics-to-azure-synapse-analytics"></a>Migrer Azure Data Lake Analytics vers Azure Synapse Analytics

Microsoft a lancé Azure Synapse Analytics, qui vise à rassembler les lacs de données et l’entrepôt de données pour une expérience d’analyse de Big Data unique. Il aidera les clients à collecter et à analyser toutes les diverses données, à résoudre l’inefficacité des données et à travailler ensemble. En outre, l’intégration de Synapse à Azure Machine Learning et Power BI permettra aux organisations d’obtenir des insights de leurs données et d’exécuter le Machine Learning sur toutes leurs applications intelligentes. 

Le document vous montre comment effectuer la migration d’Azure Data Lake Analytics vers Azure Synapse Analytics. 

## <a name="recommended-approach"></a>Approche recommandée
- Étape 1 : Évaluer la préparation
- Étape 2 : Préparer la migration
- Étape 3 : Migrer les données et les charges de travail d’application
- Étape 4 : Passer d’Azure Data Lake Analytics à Azure Synapse Analytics

### <a name="step-1-assess-readiness"></a>Étape 1 : Évaluer la préparation

1. Examinez [Apache Spark sur Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-overview.md) et comprenez les principales différences entre Azure Data Lake Analytics et Spark sur Azure Synapse Analytics. 

    |Élément | Service Analytique Azure Data Lake | Spark sur Synapse |
    | --- | --- |--- |
    | Tarifs  |Par unité d’analyse/heure |Par vCore/heure|
    |Moteur     |Service Analytique Azure Data Lake  |Apache Spark
    |Langage de programmation par défaut    |U-SQL   |T-SQL, Python, Scala, Spark SQL et .NET
    |Sources de données   |Azure Data Lake Storage    |Stockage Blob Azure, Azure Data Lake Storage

2. Passez en revue le <a href="#questionnaire">Questionnaire pour l’évaluation de la migration</a> et répertoriez les risques potentiels à prendre en compte. 

### <a name="step-2-prepare-to-migrate"></a>Étape 2 : Préparer la migration

1.  Identifiez les travaux et les données que vous allez migrer.
    -   Profitez-en pour nettoyer les travaux que vous n’utilisez plus. Si vous ne souhaitez pas migrer toutes vos données à la fois, profitez-en pour identifier les groupes logiques de travaux qui peuvent faire l’objet d’une migration en plusieurs phases.
    -   Évaluez la taille des données et comprenez le format de données Apache Spark. Passez en revue vos scripts U-SQL, évaluez les efforts de réécriture des scripts et comprenez le concept de code Apache Spark.

2.  Déterminez l’impact qu’aura une migration sur votre entreprise. Par exemple, si vous pouvez vous permettre des temps d’arrêt pendant la migration.

3.  Créez un plan de migration.

### <a name="step-3-migrate-data-and-application-workload"></a>Étape 3 : Migrer les données et le charge de travail d’application

1.  Migrez vos données d’Azure Data Lake Storage Gen1 vers Azure Data Lake Storage Gen2. <br></br>
    Azure Data Lake Storage Gen1 sera mis hors service en février 2024. Consultez l'[annonce officielle](https://azure.microsoft.com/updates/action-required-switch-to-azure-data-lake-storage-gen2-by-29-february-2024/). Nous vous suggérons de migrer d’abord les données vers Gen2. Consultez [Comprendre les formats de données Apache Spark pour les développeurs U-SQL d’Azure Data Lake Analytics](understand-spark-data-formats.md) et déplacez le fichier et les données stockées dans des tables U-SQL pour les rendre accessibles à Azure Synapse Analytics.  Vous trouverez plus d’informations sur le guide de migration [ici](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). 

2.  Transformez vos scripts U-SQL en Spark. 
    Reportez-vous à [Comprendre les concepts de code Apache Spark pour les développeurs U-SQL d’Azure Data Lake Analytics](understand-spark-code-concepts.md) pour transformer vos scripts U-SQL en Spark. 

3.  Transformez ou recréez vos pipelines d’orchestration des travaux vers le nouveau programme Spark.

### <a name="step-4-cut-over-from-azure-data-lake-analytics-to-azure-synapse-analytics"></a>Étape 4 : Passer d’Azure Data Lake Analytics à Azure Synapse Analytics

Une fois que vous êtes certain que vos applications et vos charges de travail sont stables, vous pouvez commencer à utiliser Azure Synapse Analytics pour vos scénarios métiers. Désactivez les pipelines restants qui s’exécutent sur Azure Data Lake Analytics et désactivez vos comptes Azure Data Lake Analytics.

<a name="questionnaire"></a>
## <a name="questionnaire-for-migration-assessment"></a>Questionnaire pour l’évaluation de la migration 

|Catégorie   |Questions  |Informations de référence|
| --- | --- |--- |
|Évaluer la taille de la migration |Combien de comptes Azure Data Lake Analytics avez-vous ? Combien de pipelines sont utilisés ? Combien de scripts U-SQL sont utilisés ?| Plus il y a de données et de scripts à migrer, plus il y a d’UDO/UDF utilisés dans les scripts et plus la migration est difficile. Le temps et les ressources nécessaires à la migration doivent être bien planifiés en fonction de l’échelle du projet.|
|Paramètres |Quelle est la taille de la source de données ? Quels sont les types de formats de données pour le traitement ? |[Comprendre les formats de données Apache Spark pour les développeurs U-SQL d’Azure Data Lake Analytics](understand-spark-data-formats.md)|
|Sortie des données |Conserverez-vous les données de sortie pour une utilisation ultérieure ? Si les données de sortie sont enregistrées dans des tables U-SQL, comment les gérer ? | Si les données de sortie seront fréquemment utilisées et enregistrées dans des tables U-SQL, vous devez modifier les scripts et remplacer les données de sortie par un format de données Spark pris en charge.|
|Migration des données |Avez-vous créé le plan de migration du stockage ? |[Migrer Azure Data Lake Storage de Gen1 vers Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) |
|Transformation de scripts U-SQL|Utilisez-vous UDO/UDF (.NET, Python, etc.) ? Si vous répondez oui à la question ci-dessus, quelle langage utilisez-vous dans votre UDO/UDF et tout problème de transformation pendant la transformation ? La requête fédérée est-elle utilisée dans U-SQL ?|[Comprendre les concepts de code Apache Spark pour les développeurs U-SQL d’Azure Data Lake Analytics](understand-spark-code-concepts.md)|

## <a name="next-steps"></a>Étapes suivantes

- [Azure Synapse Analytics](../synapse-analytics/get-started.md)
