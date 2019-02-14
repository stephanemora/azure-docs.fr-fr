---
title: Syntaxe de recherche JSON – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez la syntaxe de recherche JSON que vous pouvez utiliser dans l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: fddd2291fe7fbb46c57d31e9aebc7fc6244df971
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884798"
---
# <a name="json-search-syntax"></a>Syntaxe de recherche JSON

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

Les noms des nœuds d’un chemin d’accès de requête (_v0, v1, ..._) servent d’identificateurs de nœuds qui peuvent être référencés dans l’objet de requête ; les noms des arêtes (_e0, e1, ..._) du chemin d’accès représentent les types d’arêtes correspondantes. Nous pouvons utiliser un astérisque _*_ en tant que nœud ou nom d’arête (sauf pour le nœud de démarrage, qui doit être fourni) afin de déclarer qu’il n’existe aucune contrainte sur cet élément. Par exemple, un chemin d’accès de requête `/v0/*/v1/e1/*/` récupère les chemins d’accès à partir du graphique sans restreindre le type d’arête _(v0, v1)_. Pendant ce temps, la requête n’est associée à aucune contrainte sur la destination (le dernier nœud) du chemin d’accès.

Lorsqu’un chemin d’accès ne contient qu’un nœud, par exemple _v0_, la requête renvoie simplement toutes les entités qui respectent les contraintes. Un objet de contrainte appliqué au nœud de démarrage est appelé *Starting Query Object*, dont la spécification est la suivante.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

Lorsqu’un chemin d’accès ne contient pas uniquement un nœud de démarrage, le processeur de requêtes effectue une traversée de graphique qui suit le modèle de chemin d’accès spécifié. Lorsque le processeur de requêtes arrive à un nœud, les actions de traversée spécifiées par l’utilisateur sont déclenchées. En d’autres termes, il détermine s’il convient de s’arrêter au niveau du nœud actuel, auquel cas les résultats sont renvoyés, ou de continuer à explorer le graphique. Lorsqu’aucune action de traversée n’est spécifiée, les actions par défaut sont effectuées. Pour un nœud intermédiaire, l’action par défaut consiste à continuer l’exploration du graphique. Pour le dernier nœud d’un chemin d’accès, l’action par défaut consiste à s’arrêter et à renvoyer les résultats. Un objet de contrainte qui spécifie les actions de traversée est appelé *Traversal Action Object*. Sa spécification est la suivante :

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

Le corps POST d’une requête de recherche *json* doit contenir au moins un modèle de chemin d’accès *path*. Les objets d’action de traversée sont facultatifs. Voici deux exemples.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

