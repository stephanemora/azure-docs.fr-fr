---
title: Stratégies d’indexation d’Azure Cosmos DB
description: Découvrez comment configurer et modifier la stratégie d’indexation par défaut pour bénéficier d’une indexation automatique et de meilleures performances dans Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: thweiss
ms.openlocfilehash: 01e3e1f1c9bffee0604de1260e8e466f5b1d229d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467878"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Stratégies d’indexation dans Azure Cosmos DB

Dans Azure Cosmos DB, chaque conteneur est doté d’une stratégie d’indexation qui détermine la façon dont les éléments du conteneur doivent être indexés. La stratégie d’indexation par défaut pour les conteneurs nouvellement créés indexe toutes les propriétés de chaque élément, mettant en œuvre des index de plage pour les chaînes ou les nombres et des index spatiaux pour les objets GeoJSON de type Point. Ceci vous permet d’obtenir des performances de requête élevées sans avoir à réfléchir à l’indexation et à la gestion des index dès le départ.

Dans certaines situations, vous souhaiterez peut-être remplacer ce comportement automatique pour mieux répondre à vos besoins. Vous pouvez personnaliser la stratégie d’indexation d’un conteneur en définissant son *mode d’indexation* et inclure ou exclure des *chemins de la propriété*.

> [!NOTE]
> La méthode de mise à jour des stratégies d’indexation décrite dans cet article s’applique uniquement à l’API SQL (principale) de la base de données SQL Azure Cosmos.

## <a name="indexing-mode"></a>Mode d'indexation

Azure Cosmos DB prend en charge deux modes d’indexation :

- **Cohérent** : si la stratégie d’indexation d’un conteneur est défini sur Cohérent, l’index est mis à jour de façon synchrone lorsque vous créez, mettez à jour ou supprimez des éléments. Cela signifie que la cohérence de vos requêtes de lecture sera la [cohérence configurée pour le compte](consistency-levels.md).

- **Aucun** : Si la stratégie d’indexation d’un conteneur est définie sur Aucun, l’indexation est effectivement désactivée sur ce conteneur. C’est courant lorsqu’un conteneur est exclusivement utilisé comme magasin clé-valeur sans que des index secondaires soient nécessaires. Ceci peut également aider à accélérer les opérations d’insertion en bloc.

## <a name="including-and-excluding-property-paths"></a>Inclusion et exclusion de chemins de propriété

Une stratégie d’indexation personnalisée peut spécifier des chemins de propriétés qui sont explicitement inclus dans l’indexation ou en sont exclus. En optimisant le nombre de chemins indexés, vous pouvez réduire la quantité de stockage utilisé par votre conteneur et améliorer la latence des opérations d’écriture. Ces chemins sont définis à l’aide de [la méthode décrite dans la section de vue d’ensemble de l’indexation](index-overview.md#from-trees-to-property-paths) avec les ajouts suivants :

- un chemin menant à une valeur scalaire (chaîne ou nombre) se termine par `/?`
- les éléments tirés d’un tableau sont traités ensemble par le biais de la notation `/[]` (au lieu de `/0`, `/1` etc.)
- le générique `/*` peut être utilisé pour correspondre à tous les éléments situés sous le nœud

En reprenant le même exemple :

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

- le chemin du `employees` de `headquarters` est `/headquarters/employees/?`
- le chemin `country` du de `locations` est `/locations/[]/country/?`
- le chemin de tout ce qui se trouve sous `headquarters` est `/headquarters/*`

Lorsqu’un chemin est explicitement inclus dans la stratégie d’indexation, il doit également définir quels types d’index doivent être appliqués à ce chemin et, pour chaque type d’index, le type de données auquel il s’applique :

| Type d’index | Types de données cibles autorisés |
| --- | --- |
| Plage | Chaîne ou nombre |
| spatial | Point, LineString ou polygone |

Par exemple, nous pourrions inclure le chemin `/headquarters/employees/?` et spécifier qu’un index `Range` soit appliqué sur ce chemin pour les deux valeurs `String` et `Number`.

### <a name="includeexclude-strategy"></a>Stratégie inclure/exclure

Toute stratégie d’indexation doit inclure le chemin racine `/*` comme chemin inclus ou exclu.

- Inclure le chemin racine pour exclure sélectivement des chemins qui n’ont pas besoin d’être indexés. C’est l’approche recommandée, car elle permet à Azure Cosmos DB d’indexer proactivement toute nouvelle propriété qui peut être ajoutée à votre modèle.
- Exclure le chemin racine pour inclure sélectivement des chemins devant être indexés.

- Pour les chemins avec des caractères normaux qui incluent : des caractères alphanumériques et _ (caractère de soulignement), vous n’êtes pas obligé d’échapper la chaîne de chemin autour de guillemets doubles (par exemple, "/ path / ?"). Pour les chemins avec d’autres caractères spéciaux, vous devez avoir les caractères d’échappement que sont les guillemets doubles autour de la chaîne de chemin (par exemple, "/\"path\"/ ?"). Si vous prévoyez des caractères spéciaux dans votre chemin, vous pouvez échapper chaque chemin pour des raisons de sécurité. Du point de vue fonctionnel, que vous échappiez chaque chemin ou uniquement ceux qui ont des caractères spéciaux ne fait aucune différence.

- La propriété système « etag » est exclue de l’indexation par défaut, sauf si l’etag est ajouté au chemin inclus pour l’indexation.

Consultez [cette section](how-to-manage-indexing-policy.md#indexing-policy-examples) pour des exemples de stratégies d’indexation.

## <a name="composite-indexes"></a>Index composites

Les requêtes qui `ORDER BY` deux ou plusieurs propriétés nécessitent un index composite. Actuellement, les index composites sont uniquement utilisées par plusieurs requêtes `ORDER BY`. Par défaut, aucun index composite n’est défini. Vous devez donc [ajouter des index composites](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) en fonction des besoins.

Lorsque vous définissez un index composite, vous spécifiez :

- deux ou plusieurs chemins de propriété ; la séquence où les chemins de propriété sont des aspects définis ;
- l’ordre (croissant ou décroissant).

Les considérations suivantes sont prises en compte lors de l’utilisation d’index composites :

- Si les chemins des index composites ne correspondent pas à la séquence des propriétés dans la clause ORDER BY, l’index composite ne peut pas prendre en charge la requête.

- L’ordre des chemins des index composites (croissant ou décroissant) doit également correspondre à l’ordre dans la clause ORDER BY.

- L’index composite prend également en charge une clause ORDER BY dont l’ordre est inverse sur tous les chemins.

Prenons l’exemple suivant, où un index composite est défini sur des propriétés a, b et c :

| **Index composite**     | **Exemple `ORDER BY` de requête**      | **Pris en charge par l’index ?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

Vous devez personnaliser votre stratégie d’indexation afin de pouvoir servir toutes les requêtes `ORDER BY` nécessaires.

## <a name="modifying-the-indexing-policy"></a>Modification de la stratégie d’indexation

La stratégie d’indexation d’un conteneur peut être mise à jour à tout moment [à l’aide du portail Azure ou de l’un des kit de développement logiciel (SDK) pris en charge](how-to-manage-indexing-policy.md). Une mise à jour de la stratégie d’indexation déclenche une transformation de l’ancien index vers le nouveau, qui est effectuée en ligne et localement (aucun espace de stockage supplémentaire n’est consommé pendant l’opération). L’index de l’ancienne stratégie est transformé efficacement en nouvelle stratégie, sans affecter la disponibilité d’écriture ou le débit approvisionné sur le conteneur. La transformation d’index est une opération asynchrone. Le temps nécessaire pour l’effectuer dépend du débit approvisionné, du nombre d’éléments et de leur taille. 

> [!NOTE]
> Pendant la réindexation, il est possible que les requêtes ne retournent pas tous les résultats correspondants et elles ne retourneront pas d’erreurs. Cela signifie que les résultats de la requête ne seront peut-être pas cohérentes avant la fin de la transformation de l’index. Il est possible de suivre la progression de la transformation d’index [avec un des kits de développement logiciel (SDK)](how-to-manage-indexing-policy.md).

Si le mode d’indexation de la nouvelle stratégie est défini sur Cohérent, aucune autre modification de la stratégie d’indexation ne peut être appliquée pendant la transformation de l’index. Une transformation d’index en cours d’exécution peut être annulée en définissant le mode de la stratégie d’indexation sur Aucun (ce qui supprime immédiatement l’index).

## <a name="indexing-policies-and-ttl"></a>Stratégies d’indexation et TTL

La [fonctionnalité Time-to-Live (TTL)](time-to-live.md) nécessite que l’indexation soit active sur le conteneur sur lequel elle est activée. Cela signifie que :

- il n’est pas possible d’activer TTL sur un conteneur dans lequel le mode d’indexation est défini sur Aucun ;
- il n’est pas possible de définir le mode d’indexation sur Aucun sur un conteneur dans lequel TLL est activée.

Pour les scénarios où aucun chemin de propriété ne doit être indexé, mais où TTL est requise, vous pouvez utiliser une stratégie d’indexation avec :

- un mode d’indexation défini sur Cohérent,
- sans chemin d’accès inclus et avec
- `/*` comme seul chemin exclu.

## <a name="obsolete-attributes"></a>Attributs obsolètes

Lorsque vous travaillez avec des stratégies d’indexation, vous pouvez rencontrer les attributs suivants, qui sont désormais obsolètes :

- `automatic` est une valeur booléenne définie à la racine d’une stratégie d’indexation. Elle est désormais ignorée et peut être définie sur `true` lorsque l’outil que vous utilisez l’exige.
- `precision` est un nombre est défini au niveau de l’index pour les chemins inclus. Elle est désormais ignorée et peut être définie sur `-1` lorsque l’outil que vous utilisez l’exige.
- `hash` est un type d’index qui est désormais remplacé par le type de portée.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’indexation, consultez les articles suivants :

- [Vue d’ensemble de l’indexation](index-overview.md)
- [Guide pratique pour gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
