---
title: Préversion du stockage de table en colonnes - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Compression de données à l’aide du stockage en colonnes (préversion)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023817"
---
# <a name="columnar-table-storage-preview"></a>Stockage de tables en colonnes (préversion)

> [!IMPORTANT]
> Le stockage de tables en colonnes dans Hyperscale (Citus) est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>
> Vous pouvez consulter la liste complète des nouvelles fonctionnalités dans [Fonctionnalités d’évaluation pour Hyperscale (Citus)](hyperscale-preview-features.md).

Azure Database pour PostgreSQL - Hyperscale (Citus) prend en charge le stockage de tables en colonnes en ajout uniquement pour les charges de travail d’analyse et d’entreposage de données. Lorsque les colonnes (plutôt que les lignes) sont stockées de façon contiguë sur le disque, les données deviennent plus compressibles et les requêtes peuvent demander un sous-ensemble de colonnes plus rapidement.

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

En raison de la façon dont il est mesuré, le taux de compression peut ou ne peut pas correspondre à la différence de taille entre le stockage de lignes et de colonnes pour une table. La seule façon de véritablement trouver cette différence est de générer une table de lignes et de colonnes qui contient les mêmes données, et de comparer :

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

Pour notre petite table, le stockage en colonnes utilise en fait plus d’espace, mais à mesure que les données augmentent, la compression gagne.

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

Cette fonctionnalité présente toujours un certain nombre de limitations importantes. Consultez [Limites et limitations de Hyperscale (Citus)](concepts-hyperscale-limits.md#columnar-storage).

## <a name="next-steps"></a>Étapes suivantes

* Consultez un exemple de stockage en colonnes dans un [tutoriel de série chronologique](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) Citus (lien externe).
