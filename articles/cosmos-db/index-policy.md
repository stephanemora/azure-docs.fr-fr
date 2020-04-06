---
title: Stratégies d’indexation d’Azure Cosmos DB
description: Découvrez comment configurer et modifier la stratégie d’indexation par défaut pour bénéficier d’une indexation automatique et de meilleures performances dans Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tisande
ms.openlocfilehash: 930f156ebec76be860e7af02d41540ce67982f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292063"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Stratégies d’indexation dans Azure Cosmos DB

Dans Azure Cosmos DB, chaque conteneur est doté d’une stratégie d’indexation qui détermine la façon dont les éléments du conteneur doivent être indexés. La stratégie d’indexation par défaut pour les conteneurs nouvellement créés indexe toutes les propriétés de chaque élément, mettant en œuvre des index de plage pour les chaînes ou les nombres et des index spatiaux pour les objets GeoJSON de type Point. Ceci vous permet d’obtenir des performances de requête élevées sans avoir à réfléchir à l’indexation et à la gestion des index dès le départ.

Dans certaines situations, vous souhaiterez peut-être remplacer ce comportement automatique pour mieux répondre à vos besoins. Vous pouvez personnaliser la stratégie d’indexation d’un conteneur en définissant son *mode d’indexation* et inclure ou exclure des *chemins de la propriété*.

> [!NOTE]
> La méthode de mise à jour des stratégies d’indexation décrite dans cet article s’applique uniquement à l’API SQL (principale) de la base de données SQL Azure Cosmos.

## <a name="indexing-mode"></a>Mode d'indexation

Azure Cosmos DB prend en charge deux modes d’indexation :

- **Cohérent** : L’index est mis à jour de manière synchrone quand vous créez, mettez à jour ou supprimez des éléments. Cela signifie que la cohérence de vos requêtes de lecture sera la [cohérence configurée pour le compte](consistency-levels.md).
- **Aucun** : L’indexation est désactivée sur le conteneur. C’est courant lorsqu’un conteneur est exclusivement utilisé comme magasin clé-valeur sans que des index secondaires soient nécessaires. Vous pouvez également l’utiliser pour améliorer les performances des opérations en bloc. Une fois les opérations en bloc effectuées, vous pouvez définir le mode d’indexation Consistent (Cohérent), puis le superviser à l’aide de [IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) jusqu’à la fin du processus.

> [!NOTE]
> Azure Cosmos DB prend également en charge un mode d’indexation différée. L’indexation différée effectue des mises à jour de l’index à un niveau de priorité nettement inférieur quand le moteur ne fait aucun autre travail. Cela peut entraîner des résultats de requête **incohérents ou incomplets**. Si vous prévoyez d’interroger un conteneur Cosmos, vous ne devez pas sélectionner l’indexation différée.

Par défaut, la stratégie d’indexation a la valeur `automatic`. Ce résultat est obtenu en affectant à la propriété `automatic` de la stratégie d’indexation la valeur `true`. L’affectation de `true` à cette propriété permet à Azure CosmosDB d’indexer automatiquement les documents au fur et à mesure de leur rédaction.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a> Inclusion et exclusion de chemins de propriété

Une stratégie d’indexation personnalisée peut spécifier des chemins de propriétés qui sont explicitement inclus dans l’indexation ou en sont exclus. En optimisant le nombre de chemins indexés, vous pouvez réduire la quantité de stockage utilisé par votre conteneur et améliorer la latence des opérations d’écriture. Ces chemins sont définis à l’aide de [la méthode décrite dans la section de vue d’ensemble de l’indexation](index-overview.md#from-trees-to-property-paths) avec les ajouts suivants :

- un chemin menant à une valeur scalaire (chaîne ou nombre) se termine par `/?`
- les éléments tirés d’un tableau sont traités ensemble par le biais de la notation `/[]` (au lieu de `/0`, `/1` etc.)
- le générique `/*` peut être utilisé pour correspondre à tous les éléments situés sous le nœud

En reprenant le même exemple :

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- le chemin du `employees` de `headquarters` est `/headquarters/employees/?`

- le chemin `country` de `locations` est `/locations/[]/country/?`

- le chemin de tout ce qui se trouve sous `headquarters` est `/headquarters/*`

Par exemple, nous pourrions inclure le chemin d’accès `/headquarters/employees/?`. Ce chemin d’accès permet de s’assurer que la propriété employees est indexée, mais sans indexer de JSON imbriqué supplémentaire au sein de cette propriété.

## <a name="includeexclude-strategy"></a>Stratégie inclure/exclure

Toute stratégie d’indexation doit inclure le chemin racine `/*` comme chemin inclus ou exclu.

- Inclure le chemin racine pour exclure sélectivement des chemins qui n’ont pas besoin d’être indexés. C’est l’approche recommandée, car elle permet à Azure Cosmos DB d’indexer proactivement toute nouvelle propriété qui peut être ajoutée à votre modèle.
- Exclure le chemin racine pour inclure sélectivement des chemins devant être indexés.

- Pour les chemins avec des caractères normaux qui incluent : des caractères alphanumériques et _ (caractère de soulignement), vous n’êtes pas obligé d’échapper la chaîne de chemin entourée de guillemets doubles (par exemple, "/path/?"). Pour les chemins avec d’autres caractères spéciaux, vous devez avoir les caractères d’échappement que sont les guillemets doubles autour de la chaîne de chemin (par exemple, "/\"path\"/ ?"). Si vous prévoyez des caractères spéciaux dans votre chemin, vous pouvez échapper chaque chemin pour des raisons de sécurité. Du point de vue fonctionnel, que vous échappiez chaque chemin ou uniquement ceux qui ont des caractères spéciaux ne fait aucune différence.

- La propriété système `_etag` est exclue de l’indexation par défaut, sauf si l’etag est ajouté au chemin inclus pour l’indexation.

- Si le mode d’indexation est défini sur **cohérent**, les propriétés système `id` et `_ts` sont automatiquement indexées.

Lorsque vous incluez et excluez des chemins d’accès, vous pouvez rencontrer les attributs suivants :

- `kind` peut être `range` ou `hash`. La fonctionnalité d’index de plage fournit toutes les fonctionnalités d’un index de hachage. Nous vous recommandons donc d’utiliser un index de plage.

- `precision` est un nombre défini au niveau de l’index pour les chemins inclus. La valeur `-1` indique la précision maximale. Nous vous recommandons de toujours définir cette valeur sur `-1`.

- `dataType` peut être `String` ou `Number`. Cela indique les types de propriétés JSON qui seront indexées.

Lorsqu’elles ne sont pas spécifiées, ces propriétés ont les valeurs par défaut suivantes :

| **Nom de la propriété**     | **Valeur par défaut** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` et `Number` |

Consultez [cette section](how-to-manage-indexing-policy.md#indexing-policy-examples) pour obtenir des exemples de stratégie d’indexation pour l’inclusion et l’exclusion de chemins d’accès.

## <a name="spatial-indexes"></a>Index spatiaux

Lorsque vous définissez un chemin d’accès spatial dans la stratégie d’indexation, vous devez définir l’index ```type``` à appliquer à ce chemin d’accès. Les types possibles pour les index spatiaux sont les suivants :

* Point

* Polygone

* MultiPolygon

* LineString

Azure Cosmos DB, par défaut, ne crée pas d’index spatial. Si vous souhaitez utiliser des fonctions intégrées SQL spatiales, vous devez créer un index spatial sur les propriétés requises. Consultez [cette section](geospatial.md) pour des exemples de stratégies d’indexation afin d’ajouter des index spatiaux.

## <a name="composite-indexes"></a>Index composites

Les requêtes qui ont une clause `ORDER BY` avec deux ou plusieurs propriétés nécessitent un index composite. Vous pouvez également définir un index composite pour améliorer les performances de nombreuses requêtes d’égalité et de plage. Par défaut, aucun index composite n’est défini. Vous devez donc [ajouter des index composites](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) en fonction des besoins.

Lorsque vous définissez un index composite, vous spécifiez :

- deux ou plusieurs chemins de propriété ; la séquence où les chemins de propriété sont des aspects définis ;

- l’ordre (croissant ou décroissant).

> [!NOTE]
> Quand vous ajoutez un index composite, la requête utilise les index de plage existants jusqu’à ce que ce que l’ajout de ce nouvel index soit terminé. Ainsi, quand vous ajoutez un index composite, il se peut que vous ne perceviez pas immédiatement les améliorations de performances. Il est possible de suivre la progression de la transformation d’index [avec un des kits de développement logiciel (SDK)](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>Requêtes ORDER BY sur plusieurs propriétés :

Les considérations suivantes sont utilisées lors de l’utilisation d’index composites pour les requêtes avec une clause `ORDER BY` comptant au moins deux propriétés :

- Si les chemins des index composites ne correspondent pas à la séquence des propriétés dans la clause `ORDER BY`, l’index composite ne peut pas prendre en charge la requête.

- L’ordre des chemins des index composites (croissant ou décroissant) doit également correspondre à `order` dans la clause `ORDER BY`.

- L’index composite prend également en charge une clause `ORDER BY` dont l’ordre est inverse sur tous les chemins.

Prenons l’exemple suivant, où un index composite est défini sur des propriétés name, age et _ts :

| **Index composite**     | **Exemple `ORDER BY` de requête**      | **Pris en charge par l’index composite ?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Vous devez personnaliser votre stratégie d’indexation afin de pouvoir servir toutes les requêtes `ORDER BY` nécessaires.

### <a name="queries-with-filters-on-multiple-properties"></a>Requêtes avec des filtres sur plusieurs propriétés

Si une requête a des filtres sur deux propriétés ou plus, il peut être utile de créer un index composite pour ces propriétés.

Par exemple, considérez la requête suivante qui a un filtre d’égalité sur deux propriétés :

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Cette requête serait plus efficace, prendrait moins de temps et consommerait moins de RU, si elle est en mesure de tirer parti d’un index composite sur (name ASC, age ASC).

Les requêtes avec des filtres de plage peuvent également être optimisées à l’aide d’un index composite. Toutefois, la requête ne peut avoir qu’un seul filtre de plage. Les filtres de plage incluent `>`, `<`, `<=`, `>=` et `!=`. Le filtre de plage doit être défini en dernier dans l’index composite.

Considérez la requête suivante avec des filtres d’égalité et de plage :

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Cette requête serait plus efficace avec un index composite sur (name ASC, age ASC). Toutefois, la requête n’utiliserait pas d’index composite sur (name ASC, age ASC), car les filtres d’égalité doivent être définis en premier dans l’index composite.

Les considérations suivantes sont utilisées lors de la création d’index composites pour les requêtes avec des filtres sur plusieurs propriétés

- Les propriétés du filtre de la requête doivent correspondre à celles de l’index composite. Si une propriété se trouve dans l’index composite, mais qu’elle n’est pas incluse dans la requête en tant que filtre, la requête n’utilise pas l’index composite.
- Si une requête a des propriétés supplémentaires dans le filtre qui n’ont pas été définies dans un index composite, une combinaison d’index composites et de plage sera utilisée pour évaluer la requête. Cela nécessite moins de RU que l’utilisation exclusive d’index de plage.
- Si une propriété a un filtre de plage (`>`, `<`, `<=`, `>=` ou `!=`), cette propriété doit être définie en dernier dans l’index composite. Si une requête a plusieurs filtres de plage, elle n’utilise pas l’index composite.
- Lors de la création d’un index composite pour optimiser des requêtes avec plusieurs filtres, `ORDER` de l’index composite n’aura aucun impact sur les résultats. Cette propriété est facultative.
- Si vous ne définissez pas d’index composite pour une requête avec des filtres sur plusieurs propriétés, la requête réussit quand même. Toutefois, le coût RU de la requête peut être réduit à l’aide d’un index composite.

Prenons les exemples suivants, où un index composite est défini sur des propriétés name, age et timestamp :

| **Index composite**     | **Exemple de requête**      | **Pris en charge par l’index composite ?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Requêtes avec un filtre et une clause ORDER BY

Si une requête filtre sur une ou plusieurs propriétés et possède des propriétés différentes dans la clause ORDER BY, il peut être utile d’ajouter les propriétés du filtre à la clause `ORDER BY`.

Par exemple, en ajoutant les propriétés du filtre à la clause ORDER BY, la requête suivante peut être réécrite pour tirer parti d’un index composite :

Requête utilisant un index de plage :

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Requête utilisant un index composite :

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Le même modèle et les mêmes optimisations de requête peuvent être généralisés pour les requêtes avec plusieurs filtres d’égalité :

Requête utilisant un index de plage :

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Requête utilisant un index composite :

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Les considérations suivantes sont utilisées lors de la création d’index composites pour optimiser une requête avec un filtre et une clause `ORDER BY` :

* Si la requête filtre sur les propriétés, celles-ci doivent être incluses en premier dans la clause `ORDER BY`.
* Si vous ne définissez pas d’index composite sur une requête avec un filtre sur une propriété et une clause `ORDER BY` distincte à l’aide d’une autre propriété, la requête réussit quand même. Toutefois, le coût RU de la requête peut être réduit à l’aide d’un index composite, en particulier si la propriété de la clause `ORDER BY` a une cardinalité élevée.
* Toutes les considérations relatives à la création d’index composites pour les requêtes `ORDER BY` avec plusieurs propriétés, ainsi que les requêtes avec des filtres sur plusieurs propriétés, s’appliquent toujours.


| **Index composite**                      | **Exemple `ORDER BY` de requête**                                  | **Pris en charge par l’index composite ?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Modification de la stratégie d’indexation

La stratégie d’indexation d’un conteneur peut être mise à jour à tout moment [à l’aide du portail Azure ou de l’un des kit de développement logiciel (SDK) pris en charge](how-to-manage-indexing-policy.md). Une mise à jour de la stratégie d’indexation déclenche une transformation de l’ancien index vers le nouveau, qui est effectuée en ligne et localement (aucun espace de stockage supplémentaire n’est consommé pendant l’opération). L’index de l’ancienne stratégie est transformé efficacement en nouvelle stratégie, sans affecter la disponibilité d’écriture ou le débit approvisionné sur le conteneur. La transformation d’index est une opération asynchrone. Le temps nécessaire pour l’effectuer dépend du débit approvisionné, du nombre d’éléments et de leur taille.

> [!NOTE]
> Pendant l’ajout d’un index de plage ou spatial, il est possible que les requêtes ne retournent pas tous les résultats correspondants, et ce, sans retourner d’erreurs. Cela signifie que les résultats de la requête ne seront peut-être pas cohérentes avant la fin de la transformation de l’index. Il est possible de suivre la progression de la transformation d’index [avec un des kits de développement logiciel (SDK)](how-to-manage-indexing-policy.md).

Si le mode d’indexation de la nouvelle stratégie est défini sur Cohérent, aucune autre modification de la stratégie d’indexation ne peut être appliquée pendant la transformation de l’index. Une transformation d’index en cours d’exécution peut être annulée en définissant le mode de la stratégie d’indexation sur Aucun (ce qui supprime immédiatement l’index).

## <a name="indexing-policies-and-ttl"></a>Stratégies d’indexation et TTL

La [fonctionnalité Time-to-Live (TTL)](time-to-live.md) nécessite que l’indexation soit active sur le conteneur sur lequel elle est activée. Cela signifie que :

- il n’est pas possible d’activer TTL sur un conteneur dans lequel le mode d’indexation est défini sur Aucun ;
- il n’est pas possible de définir le mode d’indexation sur Aucun sur un conteneur dans lequel TLL est activée.

Pour les scénarios où aucun chemin de propriété ne doit être indexé, mais où TTL est requise, vous pouvez utiliser une stratégie d’indexation avec :

- un mode d’indexation défini sur Cohérent,
- sans chemin d’accès inclus et avec
- `/*` comme seul chemin exclu.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’indexation, consultez les articles suivants :

- [Vue d’ensemble de l’indexation](index-overview.md)
- [Guide pratique pour gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
