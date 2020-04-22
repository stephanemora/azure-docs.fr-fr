---
title: Interroger des tables Spark avec SQL à la demande (préversion)
description: Vue d’ensemble de l’interrogation des tables Spark avec SQL à la demande (préversion).
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420073"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Interroger des tables Spark avec Azure Synapse Analytics avec SQL à la demande (préversion)

SQL à la demande (préversion) peut synchroniser automatiquement les métadonnées à partir des pools Spark dans l’espace de travail Synapse (préversion). Une base de données SQL à la demande sera créée pour chaque base de données existante dans les pools Spark (préversion). Pour chaque table Spark basée sur Parquet ou CSV, une table externe est créée dans la base de données SQL à la demande. Par conséquent, vous pouvez arrêter vos pools Spark et quand même interroger les tables Spark à partir de SQL à la demande.

Quand une table est partitionnée dans Spark, les fichiers dans le stockage sont organisés par dossiers. SQL à la demande utilise les métadonnées de partition et cible uniquement les dossiers et fichiers pertinents pour votre requête.

La synchronisation des métadonnées est configurée automatiquement pour chaque pool Spark provisionné dans l’espace de travail Azure Synapse. Vous pouvez commencer à interroger des tables Spark immédiatement.

Chaque table Spark est représentée par une table externe dans un schéma dbo qui correspond à une base de données SQL à la demande. Pour les requêtes de table Spark, exécutez une requête qui cible un [spark_table] externe. Avant d’exécuter l’exemple ci-dessous, assurez-vous de disposer d’un [accès correct au compte de stockage](develop-storage-files-storage-access-control.md) où se trouvent les fichiers.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Mappage des types de données Spark aux types de données SQL

| Type de données Spark | Type de données SQL               |
| --------------- | --------------------------- |
| ByteType        | SMALLINT                    |
| ShortType       | SMALLINT                    |
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
