---
title: Méthode Graph Search – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Utilisez la méthode Graph Search dans l’API Connaissances universitaires pour renvoyer un ensemble d’entités universitaires basé sur des modèles de graphique spécifiques.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 5d47b938560fb1bd15adfe1a1c2d35b7359d47a3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57977409"
---
# <a name="graph-search-method"></a>Méthode Graph Search

L’API REST **graph search** est utilisée pour renvoyer un ensemble d’entités universitaires en fonction de modèles de graphiques donnés.  La réponse est un ensemble de chemins d’accès de graphiques qui respectent les contraintes spécifiées par l’utilisateur. Un chemin d’accès de graphique est une séquence entrelacée de nœuds et d’arêtes de graphique au format _v0, e0, v1, e1, ..., vn_, où _v0_ correspond au nœud de démarrage du chemin d’accès.
<br>

**Point de terminaison REST :**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Paramètres de la requête  

Nom     | Valeur | Requis ?  | Description
-----------|-----------|---------|--------
**mode**       | Chaîne de texte | Oui | Nom du modèle que vous souhaitez utiliser. La valeur est *json* ou *lambda*.

La méthode de recherche de graphique doit être appelée par le biais d’une requête HTTP POST. La requête post doit inclure l’en-tête de type de contenu : **application/json**.

##### <a name="json-search"></a>Recherche JSON 

Pour la recherche *json*, le corps POST est un objet JSON. L’objet JSON décrit un modèle de chemin d’accès avec des contraintes spécifiées par l’utilisateur (consultez la page des [spécifications d’un objet JSON](JSONSearchSyntax.md) pour la recherche *json*).


##### <a name="lambda-search"></a>Recherche lambda

Pour la recherche *lambda*, le corps POST est une chaîne de texte brut. Le corps POST est une chaîne de requête lambda LIKQ, qui est une instruction C# unique (consultez la page des [spécifications de la chaîne de requête](LambdaSearchSyntax.md) pour la recherche *lambda*). 

<br>

## <a name="response-json"></a>Réponse (JSON)

Nom | Description
-------|-----   
**results** | Tableau comportant 0 ou plusieurs entités correspondant à l’expression de requête. Chaque entité contient les valeurs des attributs requis. Ce champ est présent si la requête a bien été traitée.
**error** | Codes d’état HTTP. Ce champ est présent si la requête échoue.
**message** | Message d’erreur. Ce champ est présent si la requête échoue.

Si une requête ne peut pas être traitée dans un délai de _800 ms_, une erreur _timeout_ est renvoyée. 

<br>

#### <a name="example"></a>Exemple :

##### <a name="json-search"></a>Recherche JSON
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
Pour la recherche *json*, si nous voulons obtenir les publications dont le titre contient « graph engine » et qui ont été écrites par « bin shao », nous pouvons spécifier la requête de la façon suivante.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

Le résultat d’une requête est un tableau de chemins d’accès de graphiques. Un chemin d’accès de graphique est un tableau d’objets de nœuds correspondant aux nœuds indiqués dans le chemin d’accès de la requête. Ces objets de nœuds disposent au moins d’une propriété *CellID* qui représente l’ID de l’entité. D’autres propriétés peuvent être récupérées en spécifiant les noms des propriétés via l’opérateur de sélection d’un objet d’action de traversée [*Traversal Action Object*](JSONSearchSyntax.md).

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>Recherche lambda 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
Pour la recherche *lambda*, si nous voulons obtenir les ID des auteurs d’une publication donnée, nous pouvons écrire une requête comme suit.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Le résultat d’une requête de recherche *lambda* est également sous forme de tableau de chemins d’accès de graphiques :

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>Schéma de graphiques

Un schéma de graphiques est utile pour écrire des requêtes de recherche de graphiques. Il est illustré à la figure suivante.

![Schéma Microsoft Academic Graph](./Images/AcademicGraphSchema.png)
