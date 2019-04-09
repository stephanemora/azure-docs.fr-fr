---
title: Évaluer vos requêtes avec la fonction de profil d’exécution pour l’API Azure Cosmos DB Gremlin
description: Découvrez comment résoudre les problèmes et améliorer vos requêtes Gremlin à l’aide de l’étape de profil d’exécution.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288605"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>L’utilisation de l’étape de profil d’exécution pour évaluer vos requêtes Gremlin

Cet article fournit une vue d’ensemble de l’utilisation de l’étape de profil d’exécution pour les bases de données Azure Cosmos DB Gremlin API graphiques. Cette étape fournit les informations pertinentes pour la résolution des problèmes et optimisation des requêtes et il est compatible avec n’importe quelle requête Gremlin qui peut être exécutée sur un compte d’API Cosmos DB Gremlin.

Pour utiliser cette étape, ajoutez simplement le `executionProfile()` appel à la fin de votre requête Gremlin de fonction. **Votre requête Gremlin sera exécutée** et le résultat de l’opération retourne un objet de réponse JSON avec le profil de l’exécution de requête.

Par exemple : 

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Après avoir appelé la `executionProfile()` étape, la réponse sera un objet JSON qui comprend l’étape de Gremlin exécutée, la durée totale nécessaire et un tableau des opérateurs de runtime Cosmos DB qui a entraîné l’instruction.

> [!NOTE]
> Cette implémentation pour le profil d’exécution n’est pas définie dans la spécification Apache Tinkerpop. Il est propre à l’implémentation de l’API Gremlin Azure Cosmos DB.


## <a name="response-example"></a>Exemple de réponse

Voici un exemple annoté de la sortie qui sera retourné :

> [!NOTE]
> Cet exemple est annoté avec des commentaires qui expliquent la structure générale de la réponse. Une réponse executionProfile réelle ne contient pas tous les commentaires.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> L’étape executionProfile exécute la requête Gremlin. Cela inclut la `addV` ou `addE`étapes, ce qui entraînent la création et valident les modifications spécifiées dans la requête. Par conséquent, les unités de requête généré par la requête Gremlin sera également facturées.

## <a name="execution-profile-response-objects"></a>Objets de réponse de profils d’exécution

La réponse d’une fonction executionProfile() génèrera une hiérarchie d’objets JSON avec la structure suivante :
  - **Objet de l’opération gremlin**: Représente l’intégralité de l’opération Gremlin qui a été exécutée. Contient les propriétés suivantes.
    - `gremlin`: L’instruction Gremlin explicite qui a été exécutée.
    - `totalTime`: La durée, en millisecondes, que l’exécution de l’étape accumulés au cours. 
    - `metrics`: Tableau qui contient tous les opérateurs du runtime Cosmos DB qui ont été exécutées pour répondre à la requête. Cette liste est triée par ordre d’exécution.
    
  - **Opérateurs de runtime COSMOS DB**: Représente chacun des composants de l’opération entière de Gremlin. Cette liste est triée par ordre d’exécution. Chaque objet contient les propriétés suivantes :
    - `name`: Nom de l’opérateur. Il s’agit du type d’étape qui a été évaluée et exécutée. En savoir plus dans le tableau ci-dessous.
    - `time`: Durée, en millisecondes, nécessaire à un opérateur donné.
    - `annotations`: Contient des informations supplémentaires, spécifiques à l’opérateur qui a été exécutée.
    - `annotations.percentTime`: Pourcentage de la durée totale d’exécution de l’opérateur spécifique.
    - `counts`: Nombre d’objets qui ont été retournés à partir de la couche de stockage par cet opérateur. Celle-ci est contenue dans la `counts.resultCount` valeur scalaire dans.
    - `storeOps`: Représente une opération de stockage qui peut couvrir une ou plusieurs partitions.
    - `storeOps.fanoutFactor`: Représente le nombre de partitions accessibles de cette opération de stockage spécifique.
    - `storeOps.count`: Représente le nombre de résultats retournée de cette opération de stockage.
    - `storeOps.size`: Représente la taille en octets du résultat d’une opération de stockage donné.

Opérateur de Runtime COSMOS DB Gremlin|Description
---|---
`GetVertices`| Cette étape Obtient un ensemble de prédicat d’objets à partir de la couche de persistance. 
`GetEdges`| Cette étape Obtient les bords sont adjacents à un ensemble de sommets. Cette étape peut entraîner une ou plusieurs opérations de stockage.
`GetNeighborVertices`| Cette étape Obtient les vertex qui sont connectés à un ensemble d’arêtes. Les bords contient la partition clés et l’ID de vertex de leur source et cible.
`Coalesce`| Comptes de cette étape pour l’évaluation de deux opérations chaque fois que le `coalesce()` Gremlin étape est exécutée.
`CartesianProductOperator`| Cette étape calcule un produit cartésien de deux jeux de données. Généralement exécutée chaque fois que les prédicats `to()` ou `from()` sont utilisés.
`ConstantSourceOperator`| Cette étape calcule une expression pour produire une valeur de constante en conséquence.
`ProjectOperator`| Cette étape prépare et sérialise une réponse à l’aide du résultat des opérations précédentes.
`ProjectAggregation`| Cette étape prépare et sérialise une réponse pour une opération d’agrégation.

> [!NOTE]
> Cette liste continueront à être mis à jour lors de l’ajout de nouveaux opérateurs.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Exemples sur la façon d’analyser une réponse de profil d’exécution

Voici quelques exemples d’optimisations courantes qui peuvent être détectées à l’aide de la réponse de profil d’exécution :
  - Requête de distribution ramifiée invisible.
  - Requête non filtrée.

### <a name="blind-fan-out-query-patterns"></a>Modèles de requête de distribution ramifiée invisible

Supposons que la réponse de profil de l’exécution suivante à partir d’un **graphique partitionné**:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Les conclusions suivantes peuvent être définis à partir de celui-ci :
- La requête est une recherche d’ID unique, dans la mesure où l’instruction Gremlin suit le modèle `g.V('id')`.
- À en juger par la `time` métrique, la latence de cette requête semble être élevé, car il est [plus de 10 ms pour une seule opération de lecture de point](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Si nous allons examiner la `storeOps` de l’objet, nous pouvons voir que le `fanoutFactor` est `5`, ce qui signifie que [5 partitions](https://docs.microsoft.com/azure/cosmos-db/partition-data) consultés par cette opération.

Comme une conclusion de cette analyse, nous pouvons déterminer que la première requête accède à plus de partitions que nécessaire. Ce problème peut être résolu en spécifiant la clé de partitionnement dans la requête en tant que prédicat. Cela permet à une latence moindre et à moindre coût par requête. En savoir plus sur [partitionnement dans graph](graph-partitioning.md). Une requête plus optimale serait `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Modèles de requête non filtrées

Comparer les réponses de profil de l’exécution de deux suivantes. Par souci de simplicité, ces exemples utilisent un graphique partitionné unique.

Cette première requête récupère tous les vertex avec l’étiquette `tweet` et obtient ensuite leurs sommets voisins :

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Notez que le profil de la même requête, mais désormais avec un filtre supplémentaire, `has('lang', 'en')`, avant d’Explorer les sommets adjacents :

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Ces deux requêtes atteint le même résultat, en revanche, la première condition nécessitent plusieurs unités de requête, car il est nécessaire pour effectuer une itération d’un plus grand jeu de données initial avant d’interroger les éléments adjacents. Nous pouvons voir les indicateurs de ce comportement lorsque vous comparez les paramètres suivants à partir de ces deux réponses :
- Le `metrics[0].time` la valeur est supérieure dans la première réponse, ce qui indique que cette étape unique a duré plus longtemps à résoudre.
- Le `metrics[0].counts.resultsCount` la valeur est supérieure également dans la première réponse, ce qui indique que le jeu de données de travail initial était plus volumineux.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [prise en charge des fonctionnalités de Gremlin](gremlin-support.md) dans Azure Cosmos DB. 
* En savoir plus sur la [API Gremlin dans Azure Cosmos DB](graph-introduction.md).
