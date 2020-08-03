---
title: Synchronisation d’Apache Spark pour les définitions de tables externes dans SQL à la demande (préversion)
description: Vue d’ensemble de l’interrogation des tables Spark avec SQL à la demande (préversion).
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3e9f688a31d2847505e974ab6a1557aa6a7b2047
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046851"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>Synchronisation d’Apache Spark pour les définitions de tables externes Azure Synapse dans SQL à la demande (préversion)

SQL à la demande (préversion) peut synchroniser automatiquement les métadonnées à partir d’Apache Spark pour les pools Azure Synapse. Une base de données SQL à la demande sera créée pour chaque base de données existante dans les pools Spark (préversion). 

Pour chaque table externe Spark de type Parquet et située dans le Stockage Azure, une table externe est créée dans la base de données SQL à la demande. Par conséquent, vous pouvez arrêter vos pools Spark et interroger quand même les tables externes Spark à partir de SQL à la demande.

Quand une table est partitionnée dans Spark, les fichiers dans le stockage sont organisés par dossiers. SQL à la demande utilise les métadonnées de partition et cible uniquement les dossiers et fichiers pertinents pour votre requête.

La synchronisation des métadonnées est configurée automatiquement pour chaque pool Spark provisionné dans l’espace de travail Azure Synapse. Vous pouvez tout de suite commencer à interroger des tables externes Spark.

Chaque table externe Parquet Spark située dans le Stockage Azure est représentée par une table externe dans un schéma dbo qui correspond à une base de données SQL à la demande. 

Pour les requêtes de table externe Spark, exécutez une requête qui cible une [spark_table] externe. Avant d’exécuter l’exemple ci-dessous, assurez-vous de disposer d’un [accès correct au compte de stockage](develop-storage-files-storage-access-control.md) où se trouvent les fichiers.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> L’ajout, la suppression et la modification des commandes de tables externes Spark pour une colonne ne sont pas reflétés dans la table externe SQL à la demande.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Mappage des types de données Apache Spark aux types de données SQL

| Type de données Spark | Type de données SQL               |
| --------------- | --------------------------- |
| ByteType        | SMALLINT                    |
| Type court       | SMALLINT                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | Décimal                     |
| TimestampType   | datetime2                   |
| DateType        | Date                        |
| StringType      | varchar(max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)* (au format JSON)** |
| MapType         | varchar(max)* (au format JSON)** |
| StructType      | varchar(max)* (au format JSON)** |

\* Le classement utilisé est Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType et StructType sont représentés au format JSON.



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le contrôle d’accès au stockage, accédez à l’article [Contrôle d’accès au stockage](develop-storage-files-storage-access-control.md).
