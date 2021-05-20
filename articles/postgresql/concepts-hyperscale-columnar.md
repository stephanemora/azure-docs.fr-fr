---
title: Préversion du stockage de table en colonnes - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Compression de données à l’aide du stockage en colonnes (préversion)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/04/2021
ms.openlocfilehash: c42cfcc35edf33cf30c4d69b4a1fb15d39dd4009
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108773680"
---
# <a name="columnar-table-storage-preview"></a>Stockage de tables en colonnes (préversion)

> [!IMPORTANT]
> Le stockage de tables en colonnes dans Hyperscale (Citus) est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>
> Vous pouvez consulter la liste complète des nouvelles fonctionnalités dans [Fonctionnalités d’évaluation pour Hyperscale (Citus)](hyperscale-preview-features.md).

Azure Database pour PostgreSQL - Hyperscale (Citus) prend en charge le stockage de tables en colonnes en ajout uniquement pour les charges de travail d’analyse et d’entreposage de données. Lorsque les colonnes (plutôt que les lignes) sont stockées de façon contiguë sur le disque, les données deviennent plus compressibles et les requêtes peuvent demander un sous-ensemble de colonnes plus rapidement.

## <a name="usage"></a>Utilisation

Pour utiliser le stockage en colonnes, spécifiez `USING columnar` lors de la création d’une table :

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

Hyperscale (Citus) convertit les lignes en stockage en colonnes dans des « franges » pendant l’insertion. Chaque frange conserve les données d’une transaction, ou 150 000 lignes, selon la valeur la moins élevée.  (La taille de frange et les autres paramètres d’une table en colonnes peuvent être modifiés à l’aide de la fonction [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set).)

Par exemple, l’instruction suivante place les cinq lignes dans la même frange, car toutes les valeurs sont insérées dans une transaction unique :

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

Dans la mesure du possible, il est préférable de créer des franges larges, car Hyperscale (Citus) compresse les données en colonnes séparément par frange. Nous pouvons voir des statistiques sur notre table en colonnes, comme le taux de compression, le nombre de franges et le nombre moyen de lignes par frange avec `VACUUM VERBOSE` :

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

La sortie montre que Hyperscale (Citus) a utilisé l’algorithme de compression zstd pour obtenir la compression de données 1.31x. Le taux de compression compare a) la taille des données insérées telles qu’elles ont été indexées en mémoire par rapport à b) la taille de ces données compressées dans leur frange éventuelle.

En raison de la façon dont il est mesuré, le taux de compression peut ou ne peut pas correspondre à la différence de taille entre le stockage de lignes et de colonnes pour une table. La seule façon de véritablement trouver cette différence est de générer une table de lignes et de colonnes qui contient les mêmes données et de comparer.

## <a name="measuring-compression"></a>Mesure de la compression

Nous allons créer un exemple avec davantage de données pour évaluer les économies de compression.

```postgresql
-- first a wide table using row storage
CREATE TABLE perf_row(
  c00 int8, c01 int8, c02 int8, c03 int8, c04 int8, c05 int8, c06 int8, c07 int8, c08 int8, c09 int8,
  c10 int8, c11 int8, c12 int8, c13 int8, c14 int8, c15 int8, c16 int8, c17 int8, c18 int8, c19 int8,
  c20 int8, c21 int8, c22 int8, c23 int8, c24 int8, c25 int8, c26 int8, c27 int8, c28 int8, c29 int8,
  c30 int8, c31 int8, c32 int8, c33 int8, c34 int8, c35 int8, c36 int8, c37 int8, c38 int8, c39 int8,
  c40 int8, c41 int8, c42 int8, c43 int8, c44 int8, c45 int8, c46 int8, c47 int8, c48 int8, c49 int8,
  c50 int8, c51 int8, c52 int8, c53 int8, c54 int8, c55 int8, c56 int8, c57 int8, c58 int8, c59 int8,
  c60 int8, c61 int8, c62 int8, c63 int8, c64 int8, c65 int8, c66 int8, c67 int8, c68 int8, c69 int8,
  c70 int8, c71 int8, c72 int8, c73 int8, c74 int8, c75 int8, c76 int8, c77 int8, c78 int8, c79 int8,
  c80 int8, c81 int8, c82 int8, c83 int8, c84 int8, c85 int8, c86 int8, c87 int8, c88 int8, c89 int8,
  c90 int8, c91 int8, c92 int8, c93 int8, c94 int8, c95 int8, c96 int8, c97 int8, c98 int8, c99 int8
);

-- next a table with identical columns using columnar storage
CREATE TABLE perf_columnar(LIKE perf_row) USING COLUMNAR;
```

Remplissez les deux tables avec le même jeu de données volumineux :

```postgresql
INSERT INTO perf_row
  SELECT
    g % 00500, g % 01000, g % 01500, g % 02000, g % 02500, g % 03000, g % 03500, g % 04000, g % 04500, g % 05000,
    g % 05500, g % 06000, g % 06500, g % 07000, g % 07500, g % 08000, g % 08500, g % 09000, g % 09500, g % 10000,
    g % 10500, g % 11000, g % 11500, g % 12000, g % 12500, g % 13000, g % 13500, g % 14000, g % 14500, g % 15000,
    g % 15500, g % 16000, g % 16500, g % 17000, g % 17500, g % 18000, g % 18500, g % 19000, g % 19500, g % 20000,
    g % 20500, g % 21000, g % 21500, g % 22000, g % 22500, g % 23000, g % 23500, g % 24000, g % 24500, g % 25000,
    g % 25500, g % 26000, g % 26500, g % 27000, g % 27500, g % 28000, g % 28500, g % 29000, g % 29500, g % 30000,
    g % 30500, g % 31000, g % 31500, g % 32000, g % 32500, g % 33000, g % 33500, g % 34000, g % 34500, g % 35000,
    g % 35500, g % 36000, g % 36500, g % 37000, g % 37500, g % 38000, g % 38500, g % 39000, g % 39500, g % 40000,
    g % 40500, g % 41000, g % 41500, g % 42000, g % 42500, g % 43000, g % 43500, g % 44000, g % 44500, g % 45000,
    g % 45500, g % 46000, g % 46500, g % 47000, g % 47500, g % 48000, g % 48500, g % 49000, g % 49500, g % 50000
  FROM generate_series(1,50000000) g;

INSERT INTO perf_columnar
  SELECT
    g % 00500, g % 01000, g % 01500, g % 02000, g % 02500, g % 03000, g % 03500, g % 04000, g % 04500, g % 05000,
    g % 05500, g % 06000, g % 06500, g % 07000, g % 07500, g % 08000, g % 08500, g % 09000, g % 09500, g % 10000,
    g % 10500, g % 11000, g % 11500, g % 12000, g % 12500, g % 13000, g % 13500, g % 14000, g % 14500, g % 15000,
    g % 15500, g % 16000, g % 16500, g % 17000, g % 17500, g % 18000, g % 18500, g % 19000, g % 19500, g % 20000,
    g % 20500, g % 21000, g % 21500, g % 22000, g % 22500, g % 23000, g % 23500, g % 24000, g % 24500, g % 25000,
    g % 25500, g % 26000, g % 26500, g % 27000, g % 27500, g % 28000, g % 28500, g % 29000, g % 29500, g % 30000,
    g % 30500, g % 31000, g % 31500, g % 32000, g % 32500, g % 33000, g % 33500, g % 34000, g % 34500, g % 35000,
    g % 35500, g % 36000, g % 36500, g % 37000, g % 37500, g % 38000, g % 38500, g % 39000, g % 39500, g % 40000,
    g % 40500, g % 41000, g % 41500, g % 42000, g % 42500, g % 43000, g % 43500, g % 44000, g % 44500, g % 45000,
    g % 45500, g % 46000, g % 46500, g % 47000, g % 47500, g % 48000, g % 48500, g % 49000, g % 49500, g % 50000
  FROM generate_series(1,50000000) g;

VACUUM (FREEZE, ANALYZE) perf_row;
VACUUM (FREEZE, ANALYZE) perf_columnar;
```

Pour ces données, vous pouvez voir un taux de compression supérieur à 8 fois dans la table de colonnes.

```postgresql
SELECT pg_total_relation_size('perf_row')::numeric/
       pg_total_relation_size('perf_columnar') AS compression_ratio;
 compression_ratio
--------------------
 8.0196135873627944
(1 row)
```

## <a name="example"></a>Exemple

Le stockage en colonnes fonctionne bien avec le partitionnement des tables. Pour obtenir un exemple, consultez dans la documentation de la communauté du moteur Citus l’[archivage avec le stockage en colonnes](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage).

## <a name="gotchas"></a>Astuces

* Le stockage en colonnes compresse par franges. Les franges sont créées par transaction. Par conséquent, l’insertion d’une ligne par transaction insère des lignes uniques dans leurs propres franges. La compression et les performances des franges de lignes uniques seront pires qu’une table de lignes. Insérez toujours en bloc dans une table en colonnes.
* Si vous échouez et mettez en colonnes des petites franges, vous êtes bloqué.
  La seule solution consiste à créer une nouvelle table en colonnes et à copier les données de l’original dans une transaction :
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* Les données fondamentalement non compressibles peuvent être un problème, même si le stockage en colonnes est toujours utile lors de la sélection de colonnes spécifiques. Il n’est pas nécessaire de charger les autres colonnes en mémoire.
* Sur une table partitionnée avec une combinaison de partitions de lignes et de colonnes, les mises à jour doivent être ciblées avec soin. Filtrez-les pour qu’elles atteignent uniquement les partitions de lignes.
   * Si l’opération est ciblée sur une partition de lignes spécifique (par exemple, `UPDATE p2 SET i = i + 1`), elle réussit ; si elle est ciblée sur une partition en colonnes spécifiée (par exemple, `UPDATE p1 SET i = i + 1`), elle échoue.
   * Si l’opération est ciblée sur la table partitionnée et contient une clause WHERE qui exclut toutes les partitions en colonnes (par exemple, `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'`), elle réussit.
   * Si l’opération est ciblée sur la table partitionnée, mais n’effectue pas de filtrage sur les colonnes clé de partition, elle échoue. Même s’il existe des clauses WHERE qui correspondent aux lignes dans les partitions en colonnes uniquement, ce n’est pas suffisant. La clé de partition doit également être filtrée.

## <a name="limitations"></a>Limites

Cette fonctionnalité présente toujours des limites importantes. Consultez [Limites et limitations de Hyperscale (Citus)](concepts-hyperscale-limits.md#columnar-storage).

## <a name="next-steps"></a>Étapes suivantes

* Consultez un exemple de stockage en colonnes dans un [tutoriel de série chronologique](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) Citus (lien externe).
