---
title: Type d’entité composite - LUIS
titleSuffix: Azure Cognitive Services
description: Une entité composite est constituée d’autres entités (prédéfinies, simples, expressions régulières et listes). Les entités distinctes forment une entité entière.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71695163"
---
# <a name="composite-entity"></a>Entité composite 

Une entité composite est constituée d’autres entités (prédéfinies, simples, expressions régulières et listes). Les entités distinctes forment une entité entière. 

**Cette entité convient bien lorsque les données :**

* Sont associées. 
* Sont liés l’un à l’autre dans le contexte de l’énoncé.
* Utilisent divers types d’entités.
* Doivent être regroupées et traitées par l’application cliente en tant qu’unité d’informations.
* Ont divers énoncés d’utilisateur nécessitant un apprentissage automatique.

![entité composite](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Exemple JSON

Prenons l’exemple d’une entité composite `number` et `Location::ToLocation` prédéfinie avec l’énoncé suivant :

`book 2 tickets to cairo`

Notez qu’entre `2`, le nombre, et `cairo`, ToLocation, se trouvent des mots qui ne font partie d’aucune entité. Le soulignement vert, utilisé dans un énoncé étiqueté sur le site web [LUIS](luis-reference-regions.md), indique une entité composite.

![Entité composite](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

Les entités composites sont retournées dans un tableau `compositeEntities`, et toutes les entités qui la composent dans le tableau `entities` :

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```    

#### <a name="v3-prediction-endpoint-response"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

Il s’agit du JSON si `verbose=false` est défini dans la chaîne de requête :

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Il s’agit du JSON si `verbose=true` est défini dans la chaîne de requête :

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 


|Objet de données|Nom de l’entité|Valeur|
|--|--|--|
|Entité prédéfinie – nombre|"builtin.number"|"2"|
|Entité prédéfinie - GeographyV2|"Location::ToLocation"|"cairo"|

## <a name="next-steps"></a>Étapes suivantes

Dans ce [tutoriel](luis-tutorial-composite-entity.md), vous allez ajouter une **entité composite** pour regrouper les données extraites dans une seule entité contenante. Grâce au regroupement des données, l’application cliente peut extraire facilement les données associées dans différents types de données.
