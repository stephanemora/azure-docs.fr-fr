---
title: Synchroniser Apache Spark pour les définitions de tables externes dans un pool SQL serverless (préversion)
description: Vue d’ensemble de l’interrogation des tables Spark en utilisant un pool SQL serverless (préversion)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ea4e7cd009be8a78faa0dcfab44371a350b6a200
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315834"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool-preview"></a>Synchroniser Apache Spark pour les définitions de tables externes d’Azure Synapse dans un pool SQL serverless (préversion)

Un pool SQL serverless (préversion) peut synchroniser automatiquement les métadonnées depuis Apache Spark. Une base de données de pool SQL serverless sera créée pour chaque base de données existante dans les pools Apache Spark (préversion). 

Pour chaque table externe Spark de type Parquet et située dans Stockage Azure, une table externe est créée dans la base de données du pool SQL serverless. Par conséquent, vous pouvez arrêter vos pools Spark et interroger quand même les tables externes Spark à partir du pool SQL serverless.

Quand une table est partitionnée dans Spark, les fichiers dans le stockage sont organisés par dossiers. Le pool SQL serverless utilise les métadonnées des partitions et cible seulement les dossiers et fichiers pertinents pour votre requête.

La synchronisation des métadonnées est configurée automatiquement pour chaque pool Apache Spark serverless provisionné dans l’espace de travail Azure Synapse. Vous pouvez tout de suite commencer à interroger des tables externes Spark.

Chaque table externe Parquet Spark qui se trouve dans Stockage Azure est représentée par une table externe dans un schéma dbo qui correspond à une base de données de pool SQL serverless. 

Pour les requêtes de table externe Spark, exécutez une requête qui cible une [spark_table] externe. Avant d’exécuter l’exemple ci-dessous, veillez à disposer d’un [accès correct au compte de stockage](develop-storage-files-storage-access-control.md) où se trouvent les fichiers.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> L’ajout, la suppression et la modification des commandes de tables externes Spark pour une colonne ne sont pas reflétés dans la table externe du pool SQL serverless.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Mappage des types de données Apache Spark aux types de données SQL

| Type de données Spark | Type de données SQL               |
| --------------- | --------------------------- |
| ByteType        | SMALLINT                    |
| Type court      | SMALLINT                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | Décimal                     |
| TimestampType   | datetime2                   |
| DateType        | Date                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)\* (dans JSON)\** |
| MapType         | varchar(max)\* (dans JSON)\** |
| StructType      | varchar(max)\* (dans JSON)\** |

\* Le classement utilisé est Latin1_General_100_BIN2_UTF8.

\** ArrayType, MapType et StructType sont représentés au format JSON.



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le contrôle d’accès au stockage, accédez à l’article [Contrôle d’accès au stockage](develop-storage-files-storage-access-control.md).
