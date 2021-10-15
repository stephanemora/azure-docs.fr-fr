---
title: Optimisation des performances sources dans le flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez l’optimisation des performances sources dans les flux de données de mappage dans les pipelines Azure Data Factory et Azure Synapse Analytics.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 7fef72d1ca52ae04106797c8ce7088b259aad033
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293577"
---
# <a name="optimizing-sources"></a>Optimisation des sources

Pour chaque source à l’exception d’Azure SQL Database, il est recommandé de conserver l’option **Utiliser le partitionnement actuel** sélectionnée. Lors de la lecture à partir de tous les autres systèmes sources, le flux de données partitionne automatiquement les données uniformément en fonction de la taille des données. Une nouvelle partition est créée pour chaque volume d’environ 128 Mo de données. Le nombre de partitions augmente à mesure que la taille des données augmente.

Tout partitionnement personnalisé se produit *après* que Spark a lu les données, et a un impact négatif sur les performances de votre flux de données. Comme les données étant partitionnées uniformément lors de la lecture, cela n’est pas recommandé. 

> [!NOTE]
> Les vitesses de lecture peuvent être limitées par le débit de votre système source.

## <a name="azure-sql-database-sources"></a>Sources d’Azure SQL Database

Azure SQL Database offre une option de partitionnement unique appelée partitionnement de la source. L’activation du partitionnement de la source peut améliorer vos temps de lecture à partir d’Azure SQL DB en activant des connexions parallèles sur le système source. Spécifiez le nombre de partitions et la manière de partitionner vos données. Utilisez une colonne de partition avec une cardinalité élevée. Vous pouvez également entrer une requête correspondant au schéma de partitionnement de votre table source.

> [!TIP]
> Pour le partitionnement de la source, les E/S de SQL Server constituent le goulot d’étranglement. L’ajout d’un trop grand nombre de partitions peut saturer votre base de données source. Généralement, quatre ou cinq partitions sont idéales lors de l’utilisation de cette option.

:::image type="content" source="media/data-flow/sourcepart3.png" alt-text="Partitionnement de source":::

### <a name="isolation-level"></a>Niveau d'isolation

Le niveau d’isolation de la lecture sur un système source SQL Azure a un impact sur les performances. L’option « Lecture non validée » permet d’obtenir des performances optimales et d’éviter tout verrouillage de la base de données. Pour en savoir plus sur les niveaux d’isolation SQL, consultez [Présentation des niveaux d’isolation](/sql/connect/jdbc/understanding-isolation-levels).

### <a name="read-using-query"></a>Lire à l’aide d’une requête

Vous pouvez lire à partir d’Azure SQL Database à l’aide d’une table ou d’une requête SQL. Si vous exécutez une requête SQL, celle-ci doit se terminer avant que la transformation puisse démarrer. Des requêtes SQL peuvent être utiles pour effectuer plus tôt des opérations susceptibles de s’exécuter plus rapidement et réduire la quantité de données lues à partir d’un serveur SQL Server, comme des instructions SELECT, WHERE et JOIN. En effectuant des opérations plus tôt, vous perdez la possibilité de suivre le lignage et les performances des transformations avant que les données entrent dans le flux de données.

## <a name="azure-synapse-analytics-sources"></a>Sources d’Azure Synapse Analytics

Quand vous utilisez Azure Synapse Analytics, un paramètre appelé **Activer le mode de préproduction** existe dans les options de source. Cela permet au service de lire à partir de Synapse à l’aide de ```Staging```, ce qui améliore considérablement les performances de lecture à l’aide de la commande [Instruction COPY Synapse](/sql/t-sql/statements/copy-into-transact-sql) et fournit la capacité de chargement en masse la plus performante. L’activation de ```Staging``` nécessite que vous spécifiiez un Stockage Blob Azure ou un emplacement de préproduction Azure Data Lake Storage Gen2 dans les paramètres d’activité du flux de données.

:::image type="content" source="media/data-flow/enable-staging.png" alt-text="Activer le mode de préproduction":::

## <a name="file-based-sources"></a>Sources basées sur des fichiers

Si les flux de données prennent en charge un vaste éventail de types de fichiers, le format Parquet natif de Spark est recommandé pour optimiser les temps de lecture et d’écriture.

Si vous exécutez le même flux de données sur un ensemble de fichiers, nous vous recommandons de lire à partir d’un dossier en utilisant des chemins d’accès génériques ou en lisant à partir d’une liste de fichiers. Une exécution d’activité de flux de données unique peut traiter tous vos fichiers par lots. Vous trouverez plus d’informations sur la configuration de ces paramètres dans la section **Transformation de la source** de la page de documentation [Connecteur Stockage Blob Azure](connector-azure-blob-storage.md#source-transformation).

Autant que possible, évitez d’utiliser l’activité For-Each pour exécuter des flux de données sur un ensemble de fichiers. Cela aura pour effet que chaque itération de l’activité For-Each lance son propre cluster Spark, ce qui n’est souvent pas nécessaire et peut s’avérer onéreux. 

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des performances de flux de données](concepts-data-flow-performance.md)
- [Optimisation des récepteurs](concepts-data-flow-performance-sinks.md)
- [Optimisation des transformations](concepts-data-flow-performance-transformations.md)
- [Utilisation des flux de données dans des pipelines](concepts-data-flow-performance-pipelines.md)

Consultez d’autres articles sur les flux de données consacrés aux performances :

- [Activité Data Flow](control-flow-execute-data-flow-activity.md)
- [Analyser les performances des flux de données](concepts-data-flow-monitoring.md)
- [Performances Integration Runtime](concepts-integration-runtime-performance.md)
