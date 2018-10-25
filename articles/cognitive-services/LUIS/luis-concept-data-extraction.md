---
title: Concepts d’extraction de données dans LUIS - Language Understanding
titleSuffix: Azure Cognitive Services
description: Découvrez quel type de données il est possible d’extraire à partir de Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 284cc8ec6c2325df069d06039a6a22169c3107e7
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638339"
---
# <a name="data-extraction"></a>Extraction de données
LUIS donne la possibilité d’obtenir des informations à partir des énoncés d’un utilisateur en langage naturel. Les informations sont extraites de façon à pouvoir être utilisées par un programme, une application ou un chatbot de manière exploitable. Dans les sections suivantes, découvrez quelles sont les données retournées à partir des intentions et des entités avec des exemples de JSON.

Les données les plus difficiles à extraire sont les données issues du Machine Learning, car il n’y a pas de correspondance de texte à l’exact. L’extraction de données à partir [d’entités](luis-concept-entity-types.md) issues du Machine Learning doit faire partie du [cycle de création](luis-concept-app-iteration.md) jusqu'à ce que vous ayez la certitude de recevoir les données attendues.

## <a name="data-location-and-key-usage"></a>Emplacement des données et utilisation de la clé
LUIS fournit les données à partir du [point de terminaison](luis-glossary.md#endpoint) publié. La **requête HTTPS** (POST ou GET) contient l’énoncé ainsi que certaines configurations facultatives, comme l’environnement de production ou l’environnement intermédiaire.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

La valeur `appID` est disponible sur la page **Paramètres** de l’application LUIS ; elle fait également partie de l’URL (après `/apps/`) sur la page de modification de l’application. La clé `subscription-key` est la clé du point de terminaison utilisée pour interroger l’application. Vous pouvez utiliser votre clé gratuite de démarrage/création tant que vous apprenez à vous servir de LUIS, mais il est important de remplacer la clé de point de terminaison par une clé qui prend en charge votre [utilisation attendue de LUIS](luis-boundaries.md#key-limits). L’unité `timezoneOffset` est exprimée en minutes.

La **réponse HTTPS** contient toutes les informations que LUIS peut déterminer sur les intentions et les entités à partir du modèle publié à un instant t d’un point de terminaison intermédiaire ou de production. L’URL de point de terminaison se trouve sur le site web [LUIS](luis-reference-regions.md), dans la section **Manage** (Gérer), dans la page **Keys and endpoints** (Clés et points de terminaison).

## <a name="data-from-intents"></a>Données issues des intentions
La principale donnée est le **nom de l’intention** qui a obtenu le meilleur score. Avec le `MyStore` [Démarrage rapide](luis-quickstart-intents-only.md), la réponse du point de terminaison est la suivante :

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

|Objet de données|Type de données|Emplacement des données|Valeur|
|--|--|--|--|
|Intention|Chaîne|topScoringIntent.intent|"GetStoreInfo"|

Si votre chatbot ou votre application d’appel à LUIS prend une décision sur la base du score de plusieurs intentions, retournez le score de toutes les intentions en définissant le paramètre de chaîne de requête `verbose=true`. La réponse du point de terminaison est la suivante :

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

Les intentions sont triées du score le plus élevé au score le plus faible.

|Objet de données|Type de données|Emplacement des données|Valeur|Score|
|--|--|--|--|:--|
|Intention|Chaîne|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intention|Chaîne|intents[1].intent|"None"|0.0168218873|

Si vous ajoutez des domaines prédéfinis, le nom de l’intention indique le domaine, par exemple `Utilties` ou `Communication`, ainsi que l’intention :

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

|Domaine|Objet de données|Type de données|Emplacement des données|Valeur|
|--|--|--|--|--|
|Services|Intention|Chaîne|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Communication|Intention|Chaîne|intents[1].intent|<b>Communication</b>.StartOver"|
||Intention|Chaîne|intents[2].intent|"None"|


## <a name="data-from-entities"></a>Données issues des entités
Pour la plupart des applications et chatbots, le nom d’intention n’est pas suffisant. Les données supplémentaires facultatives proviennent d’entités découvertes dans l’énoncé. Chaque type d’entité retourne des informations différentes sur la correspondance.

Un seul mot ou une seule expression dans l’énoncé peut correspondre à plusieurs entités. Dans ce cas, chaque entité présentant une correspondance est retournée avec son score.

Toutes les entités sont retournées dans le tableau **entities** de la réponse provenant du point de terminaison :

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

## <a name="tokenized-entity-returned"></a>Entité retournée segmentée en unités lexicales
Plusieurs [cultures](luis-language-support.md#tokenization) retournent l’objet entité avec la valeur `entity` [segmentée en unités lexicales (tokenization)](luis-glossary.md#token). Les index startIndex et endIndex retournés par LUIS dans l’objet entité ne correspondent pas à la nouvelle valeur segmentée, mais à la requête d’origine, ce qui permet d’extraire l’entité brute par programme. 

Par exemple, en allemand, le mot `das Bauernbrot` a comme unités lexicales `das bauern brot`. La valeur segmentée, `das bauern brot`, est retournée ; la valeur d’origine peut être déterminée par programme à partir des index startIndex et endIndex de la requête d’origine, ce qui donne `das Bauernbrot`.

## <a name="simple-entity-data"></a>Données d’entité simple

Une [entité simple](luis-concept-entity-types.md) est une valeur issue du Machine Learning. Il peut s’agir d’un mot ou d’une expression.

`Bob Jones wants 3 meatball pho`

Dans l’énoncé précédent, `Bob Jones` est marqué comme entité simple `Customer`.

Le point de terminaison retourne les données suivantes : nom de l’entité, texte découvert dans l’énoncé, emplacement de ce texte et score.

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Objet de données|Nom de l’entité|Valeur|
|--|--|--|
|Entité simple|"Customer"|"bob jones"|

## <a name="hierarchical-entity-data"></a>Données d’entité hiérarchique

Les entités [hiérarchiques](luis-concept-entity-types.md) sont des valeurs issues du Machine Learning. Elles peuvent comporter un mot ou une expression. Les enfants sont identifiés par le contexte. Si vous recherchez une relation parent-enfant avec correspondance de texte à l’exact, utilisez une entité de [liste](#list-entity-data).

`book 2 tickets to paris`

Dans l’énoncé précédent, `paris` est étiqueté comme enfant `Location::ToLocation` de l’entité hiérarchique `Location`.

Le point de terminaison retourne les données suivantes : nom de l’entité et de l’enfant, texte découvert dans l’énoncé, emplacement de ce texte et score.

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Location::ToLocation",
    "startIndex": 18,
    "endIndex": 22,
    "score": 0.6866132
  }
]
```

|Objet de données|Parent|Enfant|Valeur|
|--|--|--|--|--|
|Entité hiérarchique|Lieu|ToLocation|"paris"|

## <a name="composite-entity-data"></a>Données d’entité composite
Les entités [composites](luis-concept-entity-types.md) sont des valeurs issues du Machine Learning. Elles peuvent comporter un mot ou une expression. Prenons l’exemple d’une entité composite de `number` et `Location::ToLocation` prédéfinis avec l’énoncé suivant :

`book 2 tickets to paris`

Notez qu’entre `2`, le nombre, et `paris`, ToLocation, se trouvent des mots qui ne font partie d’aucune entité. Le soulignement vert, utilisé dans un énoncé étiqueté sur le site web [LUIS](luis-reference-regions.md), indique une entité composite.

![Entité composite](./media/luis-concept-data-extraction/composite-entity.png)

Les entités composites sont retournées dans un tableau `compositeEntities`, et toutes les entités qui la composent dans le tableau `entities` :

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 18,
      "endIndex": 22,
      "score": 0.956998169
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2 tickets to paris",
      "type": "Order",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.7714499
    }
  ],
  "compositeEntities": [
    {
      "parentType": "Order",
      "value": "2 tickets to paris",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Location::ToLocation",
          "value": "paris"
        }
      ]
    }
  ]
```    

|Objet de données|Nom de l’entité|Valeur|
|--|--|--|
|Entité prédéfinie – nombre|"builtin.number"|"2"|
|Entité hiérarchique – Location|"Location::ToLocation"|"paris"|

## <a name="list-entity-data"></a>Données d’entité de liste

Une entité de [liste](luis-concept-entity-types.md) n’est pas issue du Machine Learning. Il s’agit d’une correspondance de texte à l’exact. Une liste représente les éléments de la liste de même que leurs synonymes. LUIS marque comme entité chaque correspondance avec un élément d’une liste dans la réponse. Un synonyme peut se trouver dans plusieurs listes.

Supposons que l’application comporte une liste nommée `Cities`, permettant des variations de noms de ville : ville aéroportuaire (Sea-tac), indicatif d’aéroport (SEA), code postal (98101) et indicatif téléphonique régional (206).

|Élément de liste|Synonymes de l’élément|
|---|---|
|Seattle|sea-tac, sea, 98101, 206, +1 |
|Paris|cdg, roissy, 75001, 1, +33|

`book 2 tickets to paris`

Dans l’énoncé précédent, le mot `paris` est mappé à l’élément paris au sein de l’entité de liste `Cities`. Celle-ci est mise en correspondance avec le nom normalisé de l’élément ainsi que ses synonymes.

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

Voici un autre exemple d’énoncé, avec un synonyme de Paris :

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

## <a name="prebuilt-entity-data"></a>Données d’entité prédéfinie
Les entités [prédéfinies](luis-concept-entity-types.md) sont découvertes par correspondance avec une expression régulière à l’aide du projet [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) open source. Elles sont retournées dans le tableau entities et utilisent le nom de type avec le préfixe `builtin::`. Le texte suivant est un exemple d’énoncé avec les entités prédéfinies retournées :

`Dec 5th send to +1 360-555-1212`

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

## <a name="regular-expression-entity-data"></a>Données d’entité d’expression régulière
Les entités [d’expression régulière](luis-concept-entity-types.md) sont découvertes par correspondance avec une expression régulière indiquée lors de la création des entités. Si l’on utilise `kb[0-9]{6}` comme définition de l’entité d’expression régulière, la réponse JSON suivante est un exemple d’énoncé avec les entités d’expression régulière retournées pour la requête `When was kb123456 published?` :

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="extracting-names"></a>Extraction de noms
Il est difficile d’extraire des noms d’un énoncé, car un nom peut être pratiquement n’importe quelle combinaison de lettres et de mots. Les possibilités dépendent du type de nom extrait. Il ne s’agit pas de règles, mais plutôt de recommandations.

### <a name="names-of-people"></a>Noms de personnes
Les noms de personnes peuvent avoir un format légèrement en fonction de la langue et de la culture. Utilisez soit une entité hiérarchique avec comme enfants le prénom et le nom, soit une entité simple avec des rôles de nom et de prénom. Veillez à donner des exemples qui utilisent le prénom et le nom à différents endroits de l’énoncé, dans des énoncés de longueurs différentes et pour toutes les intentions, y compris l’intention None. [Vérifiez](luis-how-to-review-endoint-utt.md) régulièrement les énoncés du point de terminaison pour étiqueter les noms qui n’ont pas été prédits correctement.

### <a name="names-of-places"></a>Noms de lieux
Les noms d’endroits sont définis et connus : villes, départements, États, provinces et pays. Si votre application utilise un ensemble connu de lieux, envisagez d’utiliser une entité de liste. Si vous devez rechercher tous les noms de lieux, créez une entité simple et donnez différents exemples. Ajoutez une liste d’expressions de noms de lieux pour renforcer le type de noms de lieux de votre application. [Vérifiez](luis-how-to-review-endoint-utt.md) régulièrement les énoncés du point de terminaison pour étiqueter les noms qui n’ont pas été prédits correctement.

### <a name="new-and-emerging-names"></a>Nouveaux noms
Certaines applications doivent être capables de rechercher les nouveaux noms, comme les produits ou les entreprises. Il s’agit du type le plus difficile d’extraction de données. Commencez par une entité simple et ajoutez une liste d’expressions. [Vérifiez](luis-how-to-review-endoint-utt.md) régulièrement les énoncés du point de terminaison pour étiqueter les noms qui n’ont pas été prédits correctement.

## <a name="pattern-roles-data"></a>Données de rôles de modèle
Les rôles sont des différences d’entités contextuelles.

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
  "entities": [
    {
      "entity": "bob jones",
      "type": "Employee",
      "startIndex": 5,
      "endIndex": 13,
      "score": 0.922820568,
      "role": ""
    },
    {
      "entity": "seattle",
      "type": "Location",
      "startIndex": 20,
      "endIndex": 26,
      "score": 0.948008537,
      "role": "Origin"
    },
    {
      "entity": "redmond",
      "type": "Location",
      "startIndex": 31,
      "endIndex": 37,
      "score": 0.7047979,
      "role": "Destination"
    }
  ]
}
```

## <a name="patternany-entity-data"></a>Données d’entité Pattern.any
Les entités Pattern.any sont des entités de longueur variable utilisées dans les énoncés d’un [modèle](luis-concept-patterns.md).

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```


## <a name="sentiment-analysis"></a>analyse de sentiments
Si l’analyse des sentiments est configurée, la réponse JSON de LUIS l’intègre. Pour plus d’informations sur l’analyse des sentiments, consultez la documentation [Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

### <a name="sentiment-data"></a>Données de sentiment
Les données de sentiment correspondent à un score compris entre 1 et 0 indiquant le sentiment, positif (plus proche de 1) ou négatif (plus proche de 0), des données.

Lorsque la culture est `en-us`, la réponse est :

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Pour toutes les autres cultures, la réponse est :

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Données d’entité d’extraction de phrases clés
L’entité d’extraction de phrases clés retourne les phrases clés de l’énoncé, fournies par [l’Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

## <a name="data-matching-multiple-entities"></a>Données correspondant à plusieurs entités
LUIS retourne toutes les entités découvertes dans l’énoncé. Le chatbot devra donc dans certains cas prendre des décisions en fonction des résultats. Un énoncé peut comporter de nombreuses entités :

`book me 2 adult business tickets to paris tomorrow on air france`

Le point de terminaison LUIS peut découvrir les mêmes données dans différentes entités :

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Consulter [Ajouter des entités](luis-how-to-add-entities.md) pour découvrir comment ajouter des entités à votre application LUIS.
