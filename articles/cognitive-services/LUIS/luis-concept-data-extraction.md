---
title: Extraction des données : LUIS
description: Extraire des données d’un texte d’énoncé avec des intentions et des entités. Découvrez quel type de données il est possible d’extraire à partir de Language Understanding (LUIS).
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 1c1a744c06e5347625fb96518bd809481ee797e5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716294"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extraire des données d’un texte d’énoncé avec des intentions et des entités
LUIS donne la possibilité d’obtenir des informations à partir des énoncés d’un utilisateur en langage naturel. Les informations sont extraites de façon à pouvoir être utilisées par un programme, une application ou un chatbot de manière exploitable. Dans les sections suivantes, découvrez quelles sont les données retournées à partir des intentions et des entités avec des exemples de JSON.

Les données les plus difficiles à extraire sont les données issues du Machine Learning, car il n’y a pas de correspondance de texte exacte. L’extraction de données à partir [d’entités](luis-concept-entity-types.md) issues du Machine Learning doit faire partie du [cycle de création](luis-concept-app-iteration.md) jusqu’à ce que vous ayez la certitude de recevoir les données attendues.

## <a name="data-location-and-key-usage"></a>Emplacement des données et utilisation de la clé
LUIS fournit les données à partir du [point de terminaison](luis-glossary.md#endpoint) publié. La **requête HTTPS** (POST ou GET) contient l’énoncé ainsi que certaines configurations facultatives, comme l’environnement de production ou l’environnement intermédiaire.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Demande de point de terminaison de prédiction V2](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[Demande de point de terminaison de prédiction V3](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *

La valeur `appID` est disponible dans la page **Paramètres** de l’application LUIS ; elle fait également partie de l’URL (après `/apps/`) dans la page de modification de l’application. La clé `subscription-key` est la clé du point de terminaison utilisée pour interroger l’application. Vous pouvez utiliser votre clé gratuite de démarrage/création tant que vous apprenez à vous servir de LUIS, mais il est important de remplacer la clé de point de terminaison par une clé qui prend en charge votre [utilisation attendue de LUIS](luis-boundaries.md#key-limits). L’unité `timezoneOffset` est exprimée en minutes.

La **réponse HTTPS** contient toutes les informations que LUIS peut déterminer sur les intentions et les entités à partir du modèle publié à un instant t d’un point de terminaison intermédiaire ou de production. L’URL de point de terminaison se trouve sur le site web [LUIS](luis-reference-regions.md), dans la section **Manage** (Gérer), dans la page **Keys and endpoints** (Clés et points de terminaison).

## <a name="data-from-intents"></a>Données issues des intentions
La principale donnée est le **nom de l’intention** qui a obtenu le meilleur score. La réponse du point de terminaison est la suivante :

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *

|Objet de données|Type de données|Emplacement des données|Valeur|
|--|--|--|--|
|Intentionnel|String|topScoringIntent.intent|"GetStoreInfo"|

Si votre chatbot ou votre application d’appel à LUIS prend une décision sur la base du score de plusieurs intentions, retournez le score de toutes les intentions.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

Définissez le paramètre de chaîne de requête, `verbose=true`. La réponse du point de terminaison est la suivante :

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

Définissez le paramètre de chaîne de requête, `show-all-intents=true`. La réponse du point de terminaison est la suivante :

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *

Les intentions sont triées du score le plus élevé au score le plus faible.

|Objet de données|Type de données|Emplacement des données|Valeur|Score|
|--|--|--|--|:--|
|Intentionnel|String|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intentionnel|String|intents[1].intent|"None"|0.0168218873|

Si vous ajoutez des domaines prédéfinis, le nom de l’intention indique le domaine, par exemple `Utilties` ou `Communication`, ainsi que l’intention :

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *

|Domain|Objet de données|Type de données|Emplacement des données|Valeur|
|--|--|--|--|--|
|Services|Intentionnel|String|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Communication|Intentionnel|String|intents[1].intent|<b>Communication</b>.StartOver"|
||Intentionnel|String|intents[2].intent|"None"|


## <a name="data-from-entities"></a>Données issues des entités
Pour la plupart des applications et chatbots, le nom d’intention n’est pas suffisant. Les données supplémentaires facultatives proviennent d’entités découvertes dans l’énoncé. Chaque type d’entité retourne des informations différentes sur la correspondance.

Un seul mot ou une seule expression dans l’énoncé peut correspondre à plusieurs entités. Dans ce cas, chaque entité présentant une correspondance est retournée avec son score.

Toutes les entités sont retournées dans le tableau **entities** de la réponse provenant du point de terminaison :

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *

## <a name="tokenized-entity-returned"></a>Entité retournée segmentée en unités lexicales

Examinez la [prise en charge des jetons](luis-language-support.md#tokenization) dans LUIS.

## <a name="simple-entity-data"></a>Données d’entité simple

Une [entité simple](reference-entity-simple.md) est une valeur issue du Machine Learning. Il peut s’agir d’un mot ou d’une expression.

## <a name="composite-entity-data"></a>Données d’entité composite

Une [entité composite](reference-entity-composite.md) est constituée d’autres entités (prédéfinies, simples, expressions régulières et listes). Les entités distinctes forment une entité entière.

## <a name="list-entity-data"></a>Données d’entité de liste

Les [entités de liste](reference-entity-list.md) représentent un ensemble fixe, fermé de mots associés, ainsi que leurs synonymes. LUIS ne détecte pas les valeurs supplémentaires pour les entités de liste. Utilisez la fonctionnalité **Recommander** pour trouver des suggestions de nouveaux mots à partir de la liste actuelle. S’il existe plusieurs entités de liste avec la même valeur, chaque entité est retournée dans la requête du point de terminaison.

## <a name="prebuilt-entity-data"></a>Données d’entité prédéfinie
Les entités [prédéfinies](luis-concept-entity-types.md) sont découvertes par correspondance avec une expression régulière à l’aide du projet [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) open source. Elles sont retournées dans le tableau entities et utilisent le nom de type avec le préfixe `builtin::`. Le texte suivant est un exemple d’énoncé avec les entités prédéfinies retournées :

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

Sans le paramètre de chaîne de requête, `verbose=true` :

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

Avec le paramètre de chaîne de requête, `verbose=true` :

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *
## <a name="regular-expression-entity-data"></a>Données d’entité d’expression régulière

Une [entité d’expression régulière](reference-entity-regular-expression.md) extrait une entité en fonction du modèle d’expression régulière que vous fournissez.

## <a name="extracting-names"></a>Extraction de noms
Il est difficile d’extraire des noms d’un énoncé, car un nom peut être pratiquement n’importe quelle combinaison de lettres et de mots. Les possibilités dépendent du type de nom extrait. Les suggestions suivantes ne sont pas des règles, mais plutôt des recommandations.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Ajouter des entités PersonName et GeographyV2 prédéfinies

Les entités [PersonName](luis-reference-prebuilt-person.md) et [GeographyV2](luis-reference-prebuilt-geographyV2.md) sont disponibles dans certaines [cultures de langue](luis-reference-prebuilt-entities.md).

### <a name="names-of-people"></a>Noms de personnes

Les noms de personnes peuvent avoir un format légèrement en fonction de la langue et de la culture. Utilisez une entité **[personName](luis-reference-prebuilt-person.md)** prédéfinie ou une **[entité simple](luis-concept-entity-types.md#simple-entity)** avec les [rôles](luis-concept-roles.md) du prénom et du nom.

Si vous utilisez l’entité simple, veillez à donner des exemples qui utilisent le prénom et le nom à différents endroits de l’énoncé, dans des énoncés de longueurs différentes et pour toutes les intentions, y compris l’intention None. [Vérifiez](luis-how-to-review-endoint-utt.md) régulièrement les énoncés du point de terminaison pour étiqueter les noms qui n’ont pas été prédits correctement.

### <a name="names-of-places"></a>Noms de lieux

Les noms d’endroits sont définis et connus : villes, départements, États, provinces et pays/régions. Utilisez l’entité prédéfinie **[geographyV2](luis-reference-prebuilt-geographyv2.md)** pour extraire des informations de localisation.

### <a name="new-and-emerging-names"></a>Nouveaux noms

Certaines applications doivent être capables de rechercher les nouveaux noms, comme les produits ou les entreprises. Ces types de noms sont les plus difficiles à extraire. Commencez par une **[entité simple](luis-concept-entity-types.md#simple-entity)** et ajoutez une [liste d’expressions](luis-concept-feature.md). [Vérifiez](luis-how-to-review-endoint-utt.md) régulièrement les énoncés du point de terminaison pour étiqueter les noms qui n’ont pas été prédits correctement.

## <a name="pattern-roles-data"></a>Données de rôles de modèle
Les rôles sont des différences d’entités contextuelles.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

Le nom de l’entité est `Location`, avec deux rôles, `Origin` et `Destination`.

```JSON
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
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

Dans V3, le **nom du rôle** correspond au nom de l’objet.

Le nom de l’entité est `Location`, avec deux rôles, `Origin` et `Destination`.

Sans le paramètre de chaîne de requête, `verbose=true` :

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

Avec le paramètre de chaîne de requête, `verbose=true` :

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *

## <a name="patternany-entity-data"></a>Données d’entité Pattern.any

[Pattern.any](reference-entity-pattern-any.md) est un espace réservé à longueur variable utilisé uniquement dans le gabarit d’énoncé d’un modèle pour marquer où l’entité commence et se termine.

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


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

Sans le paramètre de chaîne de requête, `verbose=true` :

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

Avec le paramètre de chaîne de requête, `verbose=true` :

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *


## <a name="data-matching-multiple-entities"></a>Données correspondant à plusieurs entités

LUIS retourne toutes les entités découvertes dans l’énoncé. Le chatbot devra donc dans certains cas prendre des décisions en fonction des résultats. Un énoncé peut comporter de nombreuses entités :

`book me 2 adult business tickets to paris tomorrow on air france`

Le point de terminaison LUIS peut découvrir les mêmes données dans différentes entités.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

Sans `verbose=true` en tant que paramètre de chaîne de requête.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

Avec `verbose=true` en tant que paramètre de chaîne de requête.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *

## <a name="data-matching-multiple-list-entities"></a>Données correspondant à plusieurs entités de liste

Si un mot ou une phrase correspond à plusieurs entités Liste, la requête du point de terminaison retourne chaque entité Liste.

Pour la requête `when is the best time to go to red rock?`, et si l’application contient le mot `red` dans plusieurs listes, LUIS reconnaît toutes les entités et retourne un tableau d’entités dans le cadre de la réponse du point de terminaison JSON :

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

Sans `verbose=true` dans la chaîne de requête :

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


Avec `verbose=true` dans la chaîne de requête :

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

* * *

## <a name="next-steps"></a>Étapes suivantes

Consulter [Ajouter des entités](luis-how-to-add-entities.md) pour découvrir comment ajouter des entités à votre application LUIS.
