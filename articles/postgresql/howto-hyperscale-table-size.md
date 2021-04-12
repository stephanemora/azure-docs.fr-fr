---
title: Déterminer la taille des tables – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Procédure pour déterminer la taille réelle des tables distribuées dans un groupe de serveurs Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937466"
---
# <a name="determine-table-and-relation-size"></a>Déterminer la taille des tables et des relations

La méthode habituelle pour déterminer les tailles de tables dans PostgreSQL, `pg_total_relation_size`, sous-estime la taille des tables distribuées sur Hyperscale (Citus).
Sur un groupe de serveurs Hyperscale (Citus), cette fonctionnalité affiche la taille des tables sur le nœud coordinateur.  En réalité, les données des tables distribuées résident sur les nœuds Worker (dans des partitions), et non sur le coordinateur. Une mesure réelle de la taille d’une table distribuée est obtenue sous la forme d’une somme des tailles de partitions. Hyperscale (Citus) intègre des fonctions qui aident à obtenir ces informations.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>Fonction</th>
<th>Retours</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size(nom_relation)</td>
<td><ul>
<li>Taille des données réelles dans la table (la « <a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">branche principale</a> »).</li>
<li>Une relation peut représenter le nom d’une table ou d’un index.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size(nom_relation)</td>
<td><ul>
<li><p>citus_relation_size plus :</p>
<blockquote>
<ul>
<li>taille du <a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">mappage d’espace libre</a></li>
<li>taille du <a href="https://www.postgresql.org/docs/current/static/storage-vm.html">mappage de visibilité</a></li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size(nom_relation)</td>
<td><ul>
<li><p>citus_table_size plus :</p>
<blockquote>
<ul>
<li>taille des index</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Ces fonctions sont analogues à trois des [fonctions de taille d’objet](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE) PostgreSQL standard, sauf si elles ne peuvent pas se connecter à un nœud, auquel cas elles génèrent une erreur.

## <a name="example"></a> Exemple

Voici comment répertorier les tailles de toutes les tables distribuées :

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Sortie :

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [mettre à l’échelle un groupe de serveurs](howto-hyperscale-scale-grow.md) pour stocker plus de données.
* Distinguer les [types de tables](concepts-hyperscale-nodes.md) dans un groupe de serveurs Hyperscale (Citus).
