---
title: Concepts de serveur dans Azure Database pour PostgreSQL
description: Cet article indique les éléments à prendre en considération et fournit des instructions pour configurer et gérer des serveurs Azure Database pour PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077469"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Colocalisation de table dans la base de données Azure pour PostgreSQL – très grande échelle (Citus) (version préliminaire)

La colocalisation, vous stockez des informations connexes ensemble sur les mêmes nœuds. Requêtes peuvent atteindre rapidement lorsque les données nécessaires sont disponibles sans aucun trafic réseau. Colocalisation de données associées sur différents nœuds permet aux requêtes à s’exécuter en parallèle sur chaque nœud.

## <a name="data-colocation-for-hash-distributed-tables"></a>Colocalisation de données pour les tables distribuées par hachage

Dans la très grande échelle, une ligne est stockée dans une partition si le hachage de la valeur dans la colonne de distribution se situe dans la plage de hachage de la partition. Partitions avec la même plage de hachage sont toujours placées sur le même nœud. Lignes avec des valeurs de colonne de distribution égale sont toujours sur le même nœud entre les tables.

![Partitions](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Un exemple pratique de colocalisation

Prenez en compte les tableaux suivants qui peuvent faire partie d’une analytique web mutualisée SaaS :

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Maintenant, nous voulons pour répondre aux requêtes qui peuvent être émis par un tableau de bord orientés client, telles que : « Retourner le nombre de visites de la semaine dernière pour toutes les pages en commençant par ' / blog » dans le locataire six. »

Si nos données se trouvait dans une option de déploiement de serveur unique, nous pourrions facilement exprimer notre requête à l’aide de l’ensemble complet d’opérations relationnelles offertes par SQL :

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Tant que le [jeu de travail](https://en.wikipedia.org/wiki/Working_set) pour cette requête correspond en mémoire, une table de serveur unique est une solution appropriée. Toutefois, prenons l’exemple d’opportunités de mise à l’échelle le modèle de données avec l’option de déploiement très grande échelle.

### <a name="distributing-tables-by-id"></a>Distribution de tables par ID

Requêtes de serveur unique démarrer ralentit le que le nombre de clients et les données stockées pour chaque client augmente. Jeu de travail s’arrête s’inscrivant dans la mémoire et processeur devient un goulot d’étranglement.

Dans ce cas, nous pouvons partition les données sur de nombreux nœuds à l’aide de très grande échelle. Le choix du premier et le plus important que nous devons apporter lorsque le partitionnement est la colonne de distribution. Commençons par un choix naïve de l’utilisation de `event_id` pour la table d’événements et `page_id` pour le `page` table :

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Lorsque les données sont dispersées sur différents workers, nous ne pouvons pas effectuer une jointure, comme nous le ferait sur un seul nœud de PostgreSQL. Au lieu de cela, nous devons émettre les deux requêtes :

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Par la suite, les résultats des deux étapes doivent être combinées par l’application.

Les requêtes en cours d’exécution doit consulter les données dans des partitions réparties entre les nœuds.

![requêtes inefficaces](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

Dans ce cas, la distribution des données crée substantielles inconvénients :

-   Surcharge de l’interrogation de chaque partition, plusieurs requêtes en cours d’exécution
-   Surcharge de Q1, retourne le nombre de lignes au client
-   Q2 devenir volumineux
-   La nécessité d’écrire des requêtes en plusieurs étapes nécessite des modifications dans l’application

Étant donné que les données sont réparties, les requêtes peuvent être parallélisées. Toutefois, il est uniquement utile si la quantité de travail qui effectue la requête est nettement supérieure à la surcharge de l’interrogation de plusieurs partitions.

### <a name="distributing-tables-by-tenant"></a>Distribution de tables par locataire

Dans très grande échelle, lignes avec la même valeur de colonne de distribution sont obligatoirement situés sur le même nœud. Recommencer, nous pouvons créer nos tables avec `tenant_id` comme colonne de distribution.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Maintenant Hyperscale peut répondre à la requête de serveur unique d’origine sans modification (Q1) :

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

En raison de filtre de jointure sur tenant_id, Hyperscale sait que l’intégralité de la requête permettre être traitée à l’aide de l’ensemble de partitions COLOCALISÉES qui contiennent les données pour ce client particulier. Un seul nœud PostgreSQL peut répondre à la requête en une seule étape.

![requête mieux](media/concepts-hyperscale-colocation/colocation-better-query.png)

Dans certains cas, les requêtes et les schémas de table doivent être modifiées pour inclure l’ID de locataire dans des contraintes uniques et de conditions de jointure. Toutefois, il s’agit généralement d’une modification simple.

## <a name="next-steps"></a>Étapes suivantes

- Voir comment les données de locataire sont COLOCALISÉES dans la [didacticiel d’architecture mutualisée](tutorial-design-database-hyperscale-multi-tenant.md)
