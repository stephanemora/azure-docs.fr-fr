---
title: Résoudre des problèmes de requête lors de l’utilisation d’Azure Cosmos DB
description: Apprenez à identifier, diagnostiquer et résoudre les problèmes de requête SQL Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 852ed8c49eda7f13542eb0bad63d84e1cf770e92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131374"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Résoudre des problèmes de requête lors de l’utilisation d’Azure Cosmos DB

Cet article décrit une approche générale recommandée pour le dépannage des requêtes dans Azure Cosmos DB. Bien que vous ne devriez pas considérer les étapes décrites dans cet article comme une protection complète contre les problèmes de requête potentiels, nous avons inclus ici les conseils sur les performances les plus courants. Vous devez utiliser cet article comme point de départ pour le dépannage des requêtes lentes ou coûteuses dans l’API Core (SQL) d’Azure Cosmos DB. Vous pouvez également utiliser des [journaux de diagnostic](cosmosdb-monitor-resource-logs.md) pour identifier les requêtes qui sont lentes ou qui consomment un débit significatif.

Vous pouvez grosso modo classer les optimisations de requêtes dans Azure Cosmos DB en deux catégories : 

- celles qui réduisent les frais en unités de requête (RU) de la requête
- Optimisations qui réduisent simplement la latence

Si vous réduisez les frais en RU d’une requête, vous diminuez presque certainement aussi la latence.

Cet article fournit des exemples que vous pouvez recréer à l’aide du jeu de données [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

## <a name="important"></a>Important

- Pour des performances optimales, suivez les [conseils relatifs aux performances](performance-tips.md).
    > [!NOTE]
    > Le processus hôte Windows 64 bits est recommandé pour améliorer les performances. Le kit de développement logiciel (SDK) SQL intègre un fichier ServiceInterop.dll natif pour analyser et optimiser les requêtes localement. ServiceInterop.dll est uniquement pris en charge sur la plateforme Windows x64. Pour Linux et les autres plateformes non prises en charge où ServiceInterop.dll n’est pas disponible, il procède à un appel réseau supplémentaire à destination de la passerelle afin d'obtenir la requête optimisée.
- Les requêtes Azure Cosmos DB ne prennent pas en charge un nombre d’éléments minimal.
    - Le code doit gérer toute taille de page comprise entre zéro et le nombre maximal d’éléments.
    - Le nombre d’éléments d’une page peut être modifié sans préavis.
- Des pages vides sont attendues pour les requêtes et peuvent apparaître à tout moment.
    - Les pages vides sont exposées dans les kits de développement logiciel (SDK), car cette exposition permet d’annuler une requête. Il apparaît également clair que le kit de développement logiciel (SDK) effectue plusieurs appels réseau.
    - Des pages vides peuvent apparaître dans les charges de travail existantes, car une partition physique est fractionnée dans Azure Cosmos DB. La première partition n’a aucun résultat, ce qui provoque la page vide.
    - Les pages vides résultent de la préemption d’une requête par le serveur principal en raison du fait que cette requête prend plus de temps sur le serveur principal pour récupérer les documents. Si Azure Cosmos DB préempte une requête, il renvoie un jeton de continuation permettant à la requête de se poursuivre.
- Veillez à vider complètement la requête. Examinez les exemples du kit de développement logiciel (SDK) et utilisez une boucle `while` sur `FeedIterator.HasMoreResults` pour vider l’intégralité de la requête.

## <a name="get-query-metrics"></a>Obtenir les métriques de requête

Lors de l’optimisation d’une requête dans Azure Cosmos DB, la première étape consiste toujours à [obtenir les métriques de requête](profile-sql-api-query.md) pour votre requête. Ces métriques sont également disponibles par le biais du portail Azure :

[ ![Obtention de métriques de requête](./media/troubleshoot-query-performance/obtain-query-metrics.png) ](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Après avoir obtenu les métriques de requête, comparez le nombre de documents récupérés au nombre de documents de sortie pour votre requête. Utilisez cette comparaison pour identifier les sections pertinentes à vérifier dans cet article.

Le nombre de documents récupérés correspond au nombre de documents que la requête a dû charger. Le nombre de documents de sortie correspond au nombre de documents qui ont été nécessaires pour les résultats de la requête. Si le nombre de documents récupérés est beaucoup plus élevé que le nombre de documents de sortie, il y a au moins une partie de votre requête qui n’a pas pu utiliser l’index et qui a dû effectuer une analyse.

Reportez-vous aux sections suivantes pour comprendre les optimisations de requête pertinentes pour votre scénario.

### <a name="querys-ru-charge-is-too-high"></a>Les frais en RU de la requête sont trop élevés

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Le nombre de documents récupérés est beaucoup plus élevé que le nombre de documents de sortie

- [Incluez les chemins nécessaires dans la stratégie d’indexation.](#include-necessary-paths-in-the-indexing-policy)

- [Comprenez quelles fonctions système utilisent l’index.](#understand-which-system-functions-use-the-index)

- [Modifiez les requêtes qui ont à la fois un filtre et une clause ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optimisez les expressions JOIN à l’aide d’une sous-requête.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Le nombre de documents récupérés est à peu près égal au nombre de documents de sortie

- [Évitez les requêtes entre les partitions.](#avoid-cross-partition-queries)

- [Optimisez les requêtes qui ont des filtres sur plusieurs propriétés.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Modifiez les requêtes qui ont à la fois un filtre et une clause ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Les frais en RU de la requête sont acceptables, mais la latence est encore trop élevée

- [Améliorez la proximité.](#improve-proximity)

- [Augmentez le débit provisionné.](#increase-provisioned-throughput)

- [Augmentez MaxConcurrency.](#increase-maxconcurrency)

- [Augmentez MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Requêtes où le nombre de documents récupérés dépasse le nombre de documents de sortie

 Le nombre de documents récupérés correspond au nombre de documents que la requête a dû charger. Le nombre de documents de sortie correspond au nombre de documents qui ont été nécessaires pour les résultats de la requête. Si le nombre de documents récupérés est beaucoup plus élevé que le nombre de documents de sortie, il y a au moins une partie de votre requête qui n’a pas pu utiliser l’index et qui a dû effectuer une analyse.

Voici un exemple de requête d’analyse qui n’a pas été entièrement servie par l’index :

Requête :

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Métriques de requête :

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Le nombre de documents récupérés (60 951) est beaucoup plus élevé que le nombre de documents de sortie (7). Cela signifie que cette requête a dû effectuer une analyse. Dans ce cas, la fonction système [UPPER ()](sql-query-upper.md) n’utilise pas l’index.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Inclure les chemins nécessaires dans la stratégie d’indexation

Votre stratégie d’indexation doit couvrir toutes les propriétés incluses dans les clauses `WHERE`, les clauses `ORDER BY`, `JOIN` et la plupart des fonctions système. Le chemin spécifié dans la stratégie d’index doit correspondre (en respectant la casse) à la propriété dans les documents JSON.

Si vous exécutez une requête simple sur le jeu de données [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json), vous constatez que les frais en RU sont plus faibles quand la propriété de la clause `WHERE` est indexée :

#### <a name="original"></a>Original

Requête :

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Stratégie d’indexation :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Frais en RU :** 409.51 RU

#### <a name="optimized"></a>Optimisée

Stratégie d’indexation mise à jour :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Frais en RU :** 2.98 RU

Vous pouvez ajouter des propriétés à la stratégie d’indexation à tout moment, sans impact sur les performances ou la disponibilité en écriture. Si vous ajoutez une nouvelle propriété à l’index, les requêtes qui utilisent la propriété utiliseront immédiatement le nouvel index disponible. La requête utilisera le nouvel index pendant sa construction. Ainsi, les résultats de la requête peuvent être incohérents pendant que la reconstruction de l’index est en cours. Si une nouvelle propriété est indexée, les requêtes qui utilisent uniquement des index existants ne seront pas affectées lors de la reconstruction de l’index. Vous pouvez [suivre la progression de la transformation d’index](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

### <a name="understand-which-system-functions-use-the-index"></a>Comprendre quelles fonctions système utilisent l’index

Si une expression peut être convertie en une plage de valeurs de chaîne, elle peut utiliser l’index. Dans le cas contraire, elle ne peut pas le faire.

Voici la liste des fonctions de chaîne qui peuvent utiliser l’index :

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, mais seulement si la première num_expr est 0

Voici quelques fonctions système courantes qui n’utilisent pas l’index et doivent charger chaque document :

| **Fonction système**                     | **Idées pour l’optimisation**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Utilisez Recherche Azure pour la recherche en texte intégral.                        |
| UPPER/LOWER                             | Au lieu d’utiliser la fonction système pour normaliser les données pour les comparaisons, normalisez la casse lors de l’insertion. Une requête telle que ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` devient ```SELECT * FROM c WHERE c.name = 'BOB'```. |
| Fonctions mathématiques (non-agrégations) | Si vous devez calculer fréquemment une valeur dans votre requête, stockez cette valeur en tant que propriété dans votre document JSON. |

------

D’autres parties de la requête peuvent toujours utiliser l’index même si les fonctions système ne le peuvent pas.

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Modifier les requêtes qui ont à la fois un filtre et une clause ORDER BY

Les requêtes avec un filtre et une clause `ORDER BY` utilisent normalement un index de plage, mais elles sont plus efficaces si elles peuvent être servies à partir d’un index composite. En plus de modifier la stratégie d’indexation, vous devez ajouter toutes les propriétés de l’index composite à la clause `ORDER BY`. Cette modification de la requête permet de s’assurer qu’elle utilise l’index composite.  Vous pouvez observer l’impact en exécutant une requête sur le jeu de données [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) :

#### <a name="original"></a>Original

Requête :

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Stratégie d’indexation :

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Frais en RU :** 44.28 RU

#### <a name="optimized"></a>Optimisée

Requête mise à jour (comprend les deux propriétés dans la clause `ORDER BY`) :

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Stratégie d’indexation mise à jour :

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Frais en RU :** 8.86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Optimiser les expressions JOIN à l’aide d’une sous-requête
Les sous-requêtes multivaleurs peuvent optimiser des expressions `JOIN` en envoyant les prédicats après chaque expression de sélection multiple, plutôt qu’après toutes les jointures croisées dans la clause `WHERE`.

Prenons par exemple la requête suivante :

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Frais en RU :** 167.62 RU

Pour cette requête, l’index établit une correspondance avec n’importe quel document qui a une étiquette portant le nom « infant formula », nutritionValue supérieur à 0 et une dose supérieure à 1. Dans ce cas, l’expression `JOIN` effectue le produit croisé de tous les éléments des tableaux tags, nutrients et servings pour chaque document correspondant avant l’application de tout filtre. Ensuite, la clause `WHERE` applique le prédicat de filtre sur chaque tuple `<c, t, n, s>`.

Par exemple, si un document correspondant comporte 10 éléments dans chacun des trois tableaux, l’opération aboutit à 1 x 10 x 10 x 10 (autrement dit, 1 000) tuples. L’utilisation de sous-requêtes ici peut aider à filtrer des éléments de tableaux joints avant d’effectuer une jointure avec l’expression suivante.

Cette requête est équivalente à la précédente, mais utilise des sous-requêtes :

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Frais en RU :** 22.17 RU

Supposons qu’un seul élément du tableau tags correspond au filtre, et qu’il existe cinq éléments pour les tableaux nutrients et servings. Les expressions `JOIN` aboutissent à 1 x 1 x 5 x 5 = 25 éléments, au lieu de 1000 éléments dans la première requête.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Requêtes où le nombre de documents récupérés est égal au nombre de documents de sortie

Si le nombre de documents récupérés est à peu près égal au nombre de documents de sortie, la requête n’a pas eu à analyser de nombreux documents inutiles. Pour de nombreuses requêtes, telles que celles qui utilisent le mot clé TOP, le nombre de documents récupérés peut dépasser le nombre de documents de sortie de 1. Vous n’avez pas besoin de vous en préoccuper.

### <a name="avoid-cross-partition-queries"></a>Éviter les requêtes entre les partitions

Azure Cosmos DB utilise le [partitionnement](partitioning-overview.md) pour mettre à l’échelle des conteneurs en fonction de l’augmentation des besoins en unités de requête et en stockage des données. Chaque partition physique a un index distinct et indépendant. Si votre requête a un filtre d’égalité qui correspond à la clé de partition de votre conteneur, vous devrez uniquement vérifier l’index de la partition pertinente. Cette optimisation réduit le nombre total de RU requis par la requête.

Si vous disposez d’un grand nombre d’unités de requête provisionnées (plus de 30 000) ou d’une grande quantité de données stockées (plus de 100 Go environ), vous disposez probablement d’un conteneur suffisamment grand pour constater une réduction significative des frais en RU liés aux requêtes.

Par exemple, si vous créez un conteneur avec la clé de partition foodGroup, les requêtes suivantes ne devront vérifier qu’une seule partition physique :

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Ces requêtes sont également optimisées en ajoutant la clé de partition dans la requête :

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Les requêtes qui ont des filtres de plage sur la clé de partition ou qui n’ont aucun filtre sur la clé de partition devront vérifier l’index de chaque partition physique pour obtenir les résultats :

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Optimiser les requêtes qui ont des filtres sur plusieurs propriétés

Les requêtes avec des filtres sur plusieurs propriétés utilisent normalement un index de plage, mais elles sont plus efficaces si elles peuvent être servies à partir d’un index composite. Pour les petites quantités de données, cette optimisation n’aura pas d’impact significatif. Toutefois, elle peut s’avérer utile pour les grandes quantités de données. Vous pouvez uniquement optimiser, au plus, un filtre de non-égalité par index composite. Si votre requête comporte plusieurs filtres de non-égalité, choisissez l’un d’entre eux qui utilisera l’index composite. Le reste va continuer à utiliser des index de plage. Le filtre de non-égalité doit être défini en dernier dans l’index composite. [En savoir plus sur les index composites](index-policy.md#composite-indexes).

Voici quelques exemples de requêtes qui peuvent être optimisées avec un index composite :

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Voici l’index composite approprié :

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimisations qui réduisent la latence des requêtes

Dans de nombreux cas, les frais en RU peuvent être acceptables, même si la latence des requêtes est encore trop élevée. Les sections suivantes présentent différents conseils pour réduire la latence des requêtes. Si vous exécutez la même requête plusieurs fois sur le même jeu de données, elle aura les mêmes frais en RU à chaque fois. Mais la latence des requêtes peut varier d’une exécution de requête à une autre.

### <a name="improve-proximity"></a>Améliorer la proximité

Les requêtes exécutées à partir d’une région différente de celle du compte Azure Cosmos DB auront une latence plus élevée que si elles étaient exécutées dans la même région. Par exemple, si vous exécutez du code sur votre ordinateur de bureau, vous devez vous attendre à une latence supérieure de quelques dizaines ou centaines de millisecondes (ou plus) à la même requête provenant d’une machine virtuelle située dans la même région Azure qu’Azure Cosmos DB. Il est facile de [distribuer les données globalement dans Azure Cosmos DB](distribute-data-globally.md) pour vous assurer de pouvoir rapprocher vos données de votre application.

### <a name="increase-provisioned-throughput"></a>Augmenter le débit provisionné

Dans Azure Cosmos DB, votre débit provisionné est mesuré en unités de requête (RU). Supposez que vous avez une requête qui consomme 5 RU de débit. Par exemple, si vous provisionnez 1 000 RU, vous pouvez exécuter cette requête 200 fois par seconde. Si vous avez essayé d’exécuter la requête alors que le débit disponible est insuffisant, Azure Cosmos DB retourne une erreur HTTP 429. Les kits SDK actuels de l’API Core (SQL) réessaient automatiquement d’exécuter cette requête après un bref délai. Les requêtes limitées prennent plus de temps ; par conséquent, l’augmentation du débit provisionné peut améliorer la latence des requêtes. Vous pouvez observer le [nombre total de requêtes limitées](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) dans le panneau **Métriques** du portail Azure.

### <a name="increase-maxconcurrency"></a>Augmenter MaxConcurrency

Les requêtes parallèles interrogent plusieurs partitions en parallèle. Les données d’une collection partitionnée individuelle sont toutefois extraites en série dans le cadre de la requête. Par conséquent, si vous réglez de MaxConcurrency sur le nombre de partitions, cela vous donnera les meilleures chances de résultats pour la requête, sous réserve que toutes les autres conditions système restent inchangées. Si vous ne connaissez pas le nombre de partitions, vous pouvez définir MaxConcurrency (ou MaxDegreesOfParallelism dans les versions antérieures du SDK) sur un nombre élevé. Le système choisit la valeur minimale (nombre de partitions, entrée fournie par l’utilisateur) comme degré maximal de parallélisme.

### <a name="increase-maxbuffereditemcount"></a>Augmenter MaxBufferedItemCount

Les requêtes sont conçues pour pré-extraire les résultats pendant que le lot de résultats actuel est en cours de traitement par le client. La pré-récupération permet d’améliorer la latence globale d’une requête. La définition de setMaxBufferedItemCount limite le nombre de résultats pré-extraits. Pour que la requête tire le maximum de bénéfices de la pré-extraction, affectez à cette valeur le nombre de résultats attendus (ou un nombre plus élevé). Si vous définissez cette valeur sur-1, le système détermine automatiquement le nombre d’éléments à mettre en mémoire tampon.

## <a name="next-steps"></a>Étapes suivantes
Reportez-vous aux articles suivants pour des informations sur la mesure des unités de requête par requête et pour obtenir des statistiques d’exécution afin d’optimiser vos requêtes, entre autres :

* [Obtenir des métriques sur l’exécution des requêtes SQL à l’aide du SDK .NET](profile-sql-api-query.md)
* [Réglage des performances de requête avec Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Conseils en matière de performances pour le Kit de développement logiciel (SDK) .NET](performance-tips.md)
