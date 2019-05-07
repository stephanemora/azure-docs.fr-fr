---
title: Données distribuées dans Azure Database pour PostgreSQL – très grande échelle (Citus) (version préliminaire)
description: Les tables et partitions distribuées dans le groupe de serveurs.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077334"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Données distribuées dans Azure Database pour PostgreSQL – très grande échelle (Citus) (version préliminaire)

Cet article décrit les types de trois tables de très grande échelle (Citus).
Il montre comment distribuées tables sont stockées sous forme de partitions et la manière de partitions sont placées sur les nœuds.

## <a name="table-types"></a>Types de tables

Il existe trois types de tables dans un groupe de serveurs de très grande échelle, chacun est utilisé à des fins différentes.

### <a name="type-1-distributed-tables"></a>Type 1 : tables distribuées

Le premier type et la plus courante est *distribuée* tables. Elles semblent être des tables normales aux instructions SQL, mais horizontalement *partitionnée* entre les nœuds de travail. Cela signifie que les lignes de la table sont stockées sur différents nœuds dans les tables de fragment appelés *partitions*.

Hyperscale s’exécute pas uniquement à SQL, mais les instructions DDL tout au long d’un cluster, par conséquent, modification du schéma d’une table distribuée se répercute pour mettre à jour les partitions de tous les la table sur les workers.

#### <a name="distribution-column"></a>Colonne de distribution

Hyperscale utilise le partitionnement algorithmique pour affecter des lignes à des partitions. L’affectation est effectuée de façon déterministe en fonction sur la valeur d’une colonne de table appelée la *colonne de distribution.* L’administrateur de cluster doit désigner cette colonne lors de la distribution d’une table.
Il est important pour les performances et les fonctionnalités de faire le bon choix.

### <a name="type-2-reference-tables"></a>Type 2 : tables de référence

Une table de référence est un type de table distribuée dont le contenu entier est concentré dans une seule partition. La partition est répliquée sur chaque processus de travail, afin que les requêtes sur n’importe quel employé peuvent accéder localement, les informations de référence sans la surcharge du réseau de demander des lignes à partir d’un autre nœud. Tables de référence n’ont aucune colonne de distribution, car il n’est pas nécessaire de distinguer des partitions distinctes par ligne.

Tables de référence sont généralement petits et servent à stocker des données qui s’appliquent aux requêtes en cours d’exécution sur n’importe quel nœud de travail. Par exemple, énumérées valeurs telles que les statuts de commande ou les catégories de produits.

### <a name="type-3-local-tables"></a>Type 3 : tables locales

Lorsque vous utilisez Hyperscale, le nœud coordinateur à que vous vous connectez est une base de données PostgreSQL normale. Vous pouvez créer des tables ordinaires sur le coordinateur et choisissez de ne pas partition les.

Un bon candidat pour les tables locales serait petites tables d’administration qui ne font pas partie des requêtes de jointure. Par exemple, une table d’utilisateurs pour l’authentification et de connexion de l’application.

## <a name="shards"></a>Partitions

La section précédente décrits tables distribués sont stockés en tant que partitions sur les nœuds worker. Cette section Obtient de façon plus précise les détails techniques.

Le `pg_dist_shard` table de métadonnées sur le coordinateur contient une ligne pour chaque partition de chaque table distribuée dans le système. La ligne correspond à un ID de partition avec une plage d’entiers dans un espace de hachage (shardminvalue, shardmaxvalue) :

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Si le nœud coordinateur souhaite déterminer quelle partition conserve une ligne de `github_events`, il hache la valeur de la colonne de distribution dans la ligne et vérifie quelle partition\'plage de s contient la valeur de hachage. (Les plages sont définies afin que l’image de la fonction de hachage est leur union disjointe.)

### <a name="shard-placements"></a>Placement de partition

Supposons que cette partition 102027 est associée à la ligne en question. La ligne sera être lues ou écrite dans une table appelée `github_events_102027` dans un des travailleurs. Travail qui ? Qui est déterminé entièrement par les tables de métadonnées, et le mappage de partition et le travail est connu en tant que la partition *placement*.

Le nœud coordinateur réécrit les requêtes en fragments qui font référence à des tables spécifiques telles que `github_events_102027`, et exécute ces fragments sur les workers appropriés. Voici un exemple d’une requête à exécuter en arrière-plan pour rechercher le nœud contenant la partition ID 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>Étapes suivantes
- Apprenez à [choisir une colonne de distribution](concepts-hyperscale-choose-distribution-column.md) pour les tables distribuées
