---
title: Synchroniser Apache Spark pour les définitions de tables externes dans le pool SQL serverless
description: Vue d’ensemble de l’interrogation des tables Spark en utilisant le pool SQL serverless
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 10/05/2021
author: maburd
ms.author: maburd
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: d26dfd80e6faf0292cea35c82bc2ec529dc481ab
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129995997"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>Synchroniser Apache Spark pour les définitions de tables externes d’Azure Synapse dans le pool SQL serverless

Le pool SQL serverless peut synchroniser automatiquement les métadonnées depuis Apache Spark. Une base de données de pool SQL serverless sera créée pour chaque base de données existante dans les pools Apache Spark. 

Pour chaque table externe Spark de type Parquet ou CSV et située dans Stockage Azure, une table externe est créée dans la base de données du pool SQL serverless. Par conséquent, vous pouvez arrêter vos pools Spark et interroger quand même les tables externes Spark à partir du pool SQL serverless.

Quand une table est partitionnée dans Spark, les fichiers dans le stockage sont organisés par dossiers. Le pool SQL serverless utilise les métadonnées des partitions et cible seulement les dossiers et fichiers pertinents pour votre requête.

La synchronisation des métadonnées est configurée automatiquement pour chaque pool Apache Spark serverless provisionné dans l’espace de travail Azure Synapse. Vous pouvez tout de suite commencer à interroger des tables externes Spark.

Chaque table externe Parquet Spark ou CSV qui se trouve dans Stockage Azure est représentée par une table externe dans un schéma dbo qui correspond à une base de données de pool SQL serverless. 

Pour les requêtes de table externe Spark, exécutez une requête qui cible une [spark_table] externe. Avant d’exécuter l’exemple ci-dessous, veillez à disposer d’un [accès correct au compte de stockage](develop-storage-files-storage-access-control.md) où se trouvent les fichiers.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Mappage des types de données Apache Spark aux types de données SQL

Pour plus d’informations sur le mappage des types de données Apache Spark aux types de données SQL, consultez [Tables de métadonnées partagées Azure Synapse Analytics](../metadata/table.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le contrôle d’accès au stockage, accédez à l’article [Contrôle d’accès au stockage](develop-storage-files-storage-access-control.md).
