---
title: Synchroniser Apache Spark pour les définitions de tables externes dans le pool SQL serverless
description: Vue d’ensemble de l’interrogation des tables Spark en utilisant le pool SQL serverless
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 642024d9554b51bc60df90cf3d5a7bdd799440b5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432087"
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

| Type de données Spark | Type de données SQL | Commentaires |
|---|---|---|
| `LongType`, `long`, `bigint`                | `bigint`              | **Spark** : *LongType* représente des nombres entiers signés de 8 octets. [Référence](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql) |
| `BooleanType`, `boolean`                    | `bit` (Parquet), `varchar(6)` (CSV)  | |
| `DecimalType`, `decimal`, `dec`, `numeric`  | `decimal`             | **Spark** : *DecimalType* représente des nombres décimaux entiers de précision arbitraire. Soutenu en interne par java.math.BigDecimal. Un BigDecimal est constitué d’une valeur entière de précision arbitraire non mise à l’échelle et d’une échelle d’entier 32 bits. <br> **SQL** : nombres de précision et d’échelle fixes. Lorsque la précision maximale est utilisée, les valeurs valides sont comprises entre - 10^38 +1 et 10^38 - 1. Les synonymes ISO de decimal sont dec et dec(p, s) . numeric est fonctionnellement identique à decimal. [Référence](/sql/t-sql/data-types/decimal-and-numeric-transact-sql]) |
| `IntegerType`, `Integer`, `int`             | `int`                 | **Spark** : *IntegerType* représente des nombres entiers signés de 4 octets. [Référence](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ByteType`, `Byte`, `tinyint`               | `smallint`            | **Spark** : *ByteType* représente des nombres entiers signés de 1 octet [de -128 à 127] et ShortType représente des nombres entiers signés de 2 octets [de -32768 à 32767]. <br> **SQL** : Tinyint représente des nombres entiers signés de 1 octet [0, 255] et smallint représente des nombres entiers signés de 2 octets [-32768, 32767]. [Référence](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ShortType`, `Short`, `smallint`            | `smallint`            | Identique à ce qui précède. |
| `DoubleType`, `Double`                      | `float`               | **Spark** : *DoubleType* représente des nombres à virgule flottante double précision de 8 octets. Pour **SQL**, [consultez cette page](/sql/t-sql/data-types/float-and-real-transact-sql).|
| `FloatType`, `float`, `real`                | `real`                | **Spark** : *FloatType* représente des nombres à virgule flottante double précision de 4 octets. Pour **SQL**, [consultez cette page](/sql/t-sql/data-types/float-and-real-transact-sql).|
| `DateType`, `date`                          | `date`                | **Spark** : *DateType* représente les valeurs des champs Année, Mois et Jour, sans fuseau horaire.|
| `TimestampType`, `timestamp`                | `datetime2`           | **Spark** : *TimestampType* représente les valeurs des champs Année, Mois, Jour, Heure, Minute et Seconde, avec le fuseau horaire local de la session. La valeur d’horodatage représente un point absolu dans le temps.
| `char`                                      | `char`                |
| `StringType`, `String`, `varchar`           | `Varchar(n)`          | **Spark**: *StringType* représente les valeurs des chaînes de caractères. *VarcharType(n)* est une variante de StringType qui comporte une limite de longueur. L’écriture de données échouera si la chaîne d’entrée dépasse la limite de longueur. Ce type peut uniquement être utilisé dans un schéma de table. Il ne peut pas être utilisé dans des fonctions ou des opérateurs.<br> *CharType(n)* est une variante de *VarcharType(n)* avec une longueur fixe. La lecture d’une colonne de type *CharType(n)* retourne toujours des valeurs de chaîne de longueur n. La comparaison des colonnes de type char fera correspondre la longueur la plus courte à celle la plus longue. <br> **SQL** : Dans *Varchar(n)* , n peut être max 8000 et, s’il s’agit d’une colonne partitionnée, n peut être max 2048. <br> Utilisez-le avec le classement `Latin1_General_100_BIN2_UTF8`. |
| `BinaryType`, `binary`                      | `varbinary(n)`        | **SQL** : Dans *Varbinary(n)* , n peut être max 8000 et, s’il s’agit d’une colonne partitionnée, n peut être max 2048. |
| `array`, `map`, `struct`                    | `varchar(max)`        | **SQL** : Sérialise en JSON avec le classement `Latin1_General_100_BIN2_UTF8` |

\* Le classement au niveau de la base de données est Latin1_General_100_CI_AS_SC_UTF8 \* Le classement au niveau des colonnes de type chaîne est Latin1_General_100_BIN2_UTF8

\** ArrayType, MapType et StructType sont représentés au format JSON.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le contrôle d’accès au stockage, accédez à l’article [Contrôle d’accès au stockage](develop-storage-files-storage-access-control.md).
