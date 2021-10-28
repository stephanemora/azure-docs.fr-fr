---
title: Tables de métadonnées partagées
description: Azure Synapse Analytics fournit un modèle de métadonnées partagées, où le fait de créer une table dans le pool Apache Spark serverless la rend accessible depuis le pool SQL serverless et le pool SQL dédié sans duplication des données.
services: sql-data-warehouse
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 10/13/2021
author: ma77b
ms.author: maburd
ms.reviewer: wiassaf
ms.custom: devx-track-csharp
ms.openlocfilehash: 7bb66b9fedb0f1e906a522f393ffde32ee9e2e3e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263170"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Tables de métadonnées partagées Azure Synapse Analytics


Azure Synapse Analytics permet aux différents moteurs de calcul d’espace de travail de partager des bases de données et des tables entre ses pools Apache Spark et le pool SQL serverless.

Une fois qu’une base de données a été créée par un travail Spark, vous pouvez y créer des tables avec Spark qui utilisent Parquet ou CSV comme format de stockage. Les noms de table sont convertis en minuscules et doivent être interrogés en utilisant le nom en minuscules. Ces tables sont immédiatement disponibles pour l’interrogation par n’importe lequel des pools Spark de l’espace de travail Azure Synapse. Elles peuvent également être utilisées à partir de n’importe quel travail Spark, soumis à certaines autorisations.

Les tables Spark créées, gérées et externes sont également mises à disposition comme tables externes sous le même nom dans la base de données synchronisée correspondante dans le pool SQL serverless. [Exposition d’une table Spark dans SQL](#expose-a-spark-table-in-sql) fournit plus de détails sur la synchronisation des tables.

Comme elles sont synchronisées avec le pool SQL serverless de façon asynchrone, les tables apparaissent après un petit délai.

## <a name="manage-a-spark-created-table"></a>Gérer une table créée avec Spark

Utilisez Spark pour gérer les bases de données créées avec Spark. Par exemple, supprimez-la via un travail de pool Apache Spark serverless et créez-y des tables depuis Spark.

Les objets des bases de données synchronisées ne peuvent pas être modifiés à partir du pool SQL serverless.

## <a name="expose-a-spark-table-in-sql"></a>Exposer une table Spark dans SQL

### <a name="shared-spark-tables"></a>Tables Spark partagées

Spark fournit deux types de tables qu’Azure Synapse expose automatiquement dans SQL :

- Tables gérées

  Spark offre de nombreuses options pour le stockage des données dans des tables gérées, telles que TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA et LIBSVM. Ces fichiers sont normalement stockés dans le répertoire `warehouse` dans lequel sont stockées les données des tables gérées.

- Tables externes

  Spark fournit également des moyens de créer des tables externes sur des données existantes, soit en spécifiant l’option `LOCATION`, soit en utilisant le format Hive. Ces tables externes peuvent être de divers formats de données, notamment Parquet.

Azure Synapse partage actuellement uniquement les tables Spark gérées et externes qui stockent leurs données au format Parquet ou CSV avec les moteurs SQL. Les tables d’autres formats ne sont pas automatiquement synchronisées. Vous pourrez peut-être synchroniser ces tables de manière explicite en tant que table externe dans votre propre base de données SQL si le moteur SQL prend en charge le format sous-jacent de la table.

> [!NOTE] 
> Actuellement, seuls les formats Parquet et CSV sont synchronisés avec un pool SQL serverless. Les métadonnées d’une table delta Spark ne vont pas se synchroniser avec le moteur SQL, même si la table Delta utilise Parquet comme format de stockage de l’instantané. Les tables externes de Spark ne se synchronisent actuellement pas dans des bases de données de pool SQL dédiées.

### <a name="share-spark-tables"></a>Partager des tables Spark

Les tables Spark gérées et externes partageables sont exposées dans le moteur SQL en tant que tables externes avec les propriétés suivantes :

- La source de données de la table externe SQL est la source de données représentant le dossier d’emplacement de la table Spark.
- Le format de fichier de la table externe SQL est Parquet ou CSV.
- Les informations d’identification d’accès de la table externe SQL sont directes.

Étant donné que tous les noms de tables Spark sont des noms de tables SQL valides et que tous les noms de colonnes Spark sont des noms de colonnes SQL valides, les noms de tables et de colonnes Spark sont utilisés pour la table externe SQL.

Les tables Spark fournissent des types de données différents de ceux des moteurs SQL Synapse. Le tableau suivant mappe les types de données de table Spark aux types SQL :

| Type de données Spark | Type de données SQL | Commentaires |
|---|---|---|
| `LongType`, `long`, `bigint`                | `bigint`              | **Spark** : *LongType* représente des nombres entiers signés de 8 octets.<BR>**SQL** : Consultez [int, bigint, smallint et tinyint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql).|
| `BooleanType`, `boolean`                    | `bit` (Parquet), `varchar(6)` (CSV)  | **Spark** : Booléen.<BR>**SQL** : Consultez [/sql/t-sql/data-types/bit-transact-sql).|
| `DecimalType`, `decimal`, `dec`, `numeric`  | `decimal`             | **Spark** : *DecimalType* représente des nombres décimaux entiers de précision arbitraire. Soutenu en interne par java.math.BigDecimal. Un BigDecimal est constitué d’une valeur entière de précision arbitraire non mise à l’échelle et d’une échelle d’entier 32 bits. <br> **SQL** : nombres de précision et d’échelle fixes. Lorsque la précision maximale est utilisée, les valeurs valides sont comprises entre - 10^38 +1 et 10^38 - 1. Les synonymes ISO de decimal sont dec et dec(p, s) . numeric est fonctionnellement identique à decimal. Consultez [decimal et numeric](/sql/t-sql/data-types/decimal-and-numeric-transact-sql). |
| `IntegerType`, `Integer`, `int`             | `int`                 | **Spark** : *IntegerType* représente des nombres entiers signés de 4 octets. <BR>**SQL** : Consultez [int, bigint, smallint et tinyint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql).|
| `ByteType`, `Byte`, `tinyint`               | `smallint`            | **Spark** : *ByteType* représente des nombres entiers signés de un octet [de -128 à 127] et ShortType représente des nombres entiers signés de deux octets [de -32768 à 32767]. <br> **SQL** : Tinyint représente des nombres entiers signés de un octet [0, 255] et smallint représente des nombres entiers signés de deux octets [-32768, 32767]. Consultez [int, bigint, smallint et tinyint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql).|
| `ShortType`, `Short`, `smallint`            | `smallint`            | Identique à ce qui précède. |
| `DoubleType`, `Double`                      | `float`               | **Spark** : *DoubleType* représente des nombres à virgule flottante double précision de huit octets. **SQL** : Consultez [float et real](/sql/t-sql/data-types/float-and-real-transact-sql).|
| `FloatType`, `float`, `real`                | `real`                | **Spark** : *FloatType* représente des nombres à virgule flottante double précision de quatre octets. **SQL** : Consultez [float et real](/sql/t-sql/data-types/float-and-real-transact-sql).|
| `DateType`, `date`                          | `date`                | **Spark** : *DateType* représente les valeurs des champs Année, Mois et Jour, sans fuseau horaire.<BR>**SQL** : Consultez [date](/sql/t-sql/data-types/date-transact-sql).|
| `TimestampType`, `timestamp`                | `datetime2`           | **Spark** : *TimestampType* représente les valeurs des champs Année, Mois, Jour, Heure, Minute et Seconde, avec le fuseau horaire local de la session. La valeur d’horodatage représente un point absolu dans le temps.<BR>**SQL** : Consultez [datetime2](/sql/t-sql/data-types/datetime2-transact-sql). |
| `char`                                      | `char`                |
| `StringType`, `String`, `varchar`           | `Varchar(n)`          | **Spark**: *StringType* représente des valeurs de chaînes de caractères. *VarcharType(n)* est une variante de StringType qui comporte une limite de longueur. L’écriture de données échoue si la chaîne d’entrée dépasse la limite de longueur. Ce type peut uniquement être utilisé dans un schéma de table. Il ne peut pas être utilisé dans des fonctions ou des opérateurs.<br> *CharType(n)* est une variante de *VarcharType(n)* dont la longueur est fixe. La lecture d’une colonne de type *CharType(n)* retourne toujours des valeurs de chaîne de longueur n. La comparaison des colonnes *CharType(n)* fera correspondre la longueur la plus courte à celle la plus longue. <br> **SQL** : S’il existe une longueur fournie à partir de Spark, n dans *varchar(n)* sera défini sur cette longueur. S’il s’agit d’une colonne partitionnée, n peut avoir la valeur 2048 au maximum. Sinon, il va s’agir de *varchar(max)*. Consultez [char et varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql).<br> Utilisez-le avec le classement `Latin1_General_100_BIN2_UTF8`. |
| `BinaryType`, `binary`                      | `varbinary(n)`        | **SQL** : S’il existe une longueur fournie à partir de Spark, `n` dans *Varbinary(n)* sera défini sur cette longueur. S’il s’agit d’une colonne partitionnée, n peut avoir la valeur 2048 au maximum. Sinon, il va s’agir de *Varbinary(max)*.  Consultez [binary et varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql).|
| `array`, `map`, `struct`                    | `varchar(max)`        | **SQL** : Sérialise en JSON avec le classement `Latin1_General_100_BIN2_UTF8`. Consultez [Données JSON](/sql/relational-databases/json/json-data-sql-server).|

>[!NOTE]
>Le classement au niveau de la base de données est `Latin1_General_100_CI_AS_SC_UTF8`.

## <a name="security-model"></a>Modèle de sécurité

Les bases de données et les tables Spark, ainsi que leurs représentations synchronisées dans le moteur SQL, sont sécurisées au niveau du stockage sous-jacent. Puisqu’elles n’ont actuellement pas d’autorisations sur les objets eux-mêmes, les objets sont visibles dans l’Explorateur d’objets.

Le principal de sécurité qui crée une table gérée est considéré comme le propriétaire de cette table et dispose de tous les droits sur la table ainsi que sur les dossiers et fichiers sous-jacents. En outre, le propriétaire de la base de données devient automatiquement copropriétaire de la table.

Si vous créez une table externe SQL ou Spark avec authentification directe, les données sont sécurisées uniquement au niveau des dossiers et des fichiers. Si quelqu’un interroge ce type de table externe, l’identité de sécurité de l’expéditeur de la requête est transmise au système de fichiers, qui vérifiera les droits d’accès.

Pour plus d’informations sur la façon de définir des autorisations sur les dossiers et les fichiers, consultez [Base de données partagée Azure Synapse Analytics](database.md).

## <a name="examples"></a>Exemples

### <a name="create-a-managed-table-in-spark-and-query-from-serverless-sql-pool"></a>Créer une table gérée dans Spark et interroger à partir du pool SQL serverless

Dans ce scénario, vous disposez d’une base de données Spark nommée `mytestdb`. Consultez [Créer une base de données Spark et s’y connecter avec le pool SQL serverless](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Créez une table Spark gérée avec SparkSQL en exécutant la commande suivante :

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

Cette commande crée la table `myparquettable` dans la base de données `mytestdb`. Les noms de table sont convertis en minuscules. Après un bref délai, vous pouvez voir la table dans votre pool SQL serverless. Exécutez par exemple l’instruction suivante à partir de votre pool SQL serverless.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Vérifiez que `myparquettable` est inclus dans les résultats.

>[!NOTE]
>Une table qui n’utilise pas Parquet ou CSV comme format de stockage n’est pas synchronisée.

Ensuite, insérez des valeurs dans la table à partir de Spark, par exemple avec les instructions Spark C# suivantes dans un notebook C# :

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

Vous pouvez maintenant lire les données de votre pool SQL serverless comme suit :

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

Vous devez obtenir la ligne suivante comme résultat :

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-in-spark-and-query-from-serverless-sql-pool"></a>Créer une table externe dans Spark et interroger à partir du pool SQL serverless

Dans cet exemple, nous allons créer une table Spark externe sur les fichiers de données Parquet qui ont été créés dans l’exemple précédent pour la table gérée.

Par exemple, avec SparkSQL, exécutez :

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<storage-name>.dfs.core.windows.net/<fs>/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Remplacez l’espace réservé `<storage-name>` par le nom du compte de stockage ADLS Gen2 que vous utilisez, `<fs>` par le nom du système de fichiers que vous utilisez, et l’espace réservé `<synapse_ws>` par le nom de l’espace de travail Azure Synapse que vous utilisez pour exécuter cet exemple.

L’exemple précédent crée la table `myextneralparquettable` dans la base de données `mytestdb`. Après un bref délai, vous pouvez voir la table dans votre pool SQL serverless. Exécutez par exemple l’instruction suivante à partir de votre pool SQL serverless.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Vérifiez que `myexternalparquettable` est inclus dans les résultats.

Vous pouvez maintenant lire les données de votre pool SQL serverless comme suit :

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
```

Vous devez obtenir la ligne suivante comme résultat :

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Étapes suivantes

- [Apprenez-en davantage sur les métadonnées partagées Azure Synapse Analytics](overview.md)
- [En savoir plus sur la base de données de métadonnées partagées Azure Synapse Analytics](database.md)
