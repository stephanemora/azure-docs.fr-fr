---
title: Entité prédéfinie ordinal
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie ordinal dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 16529c8334ae9f2eed5715abb22dcbcdbebec7c4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485106"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie ordinal pour une application LUIS
Un nombre ordinal est une représentation numérique d’un objet à l’intérieur d’un ensemble : `first`, `second`, `third`. Étant donné que cette entité est déjà entraînée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant un ordinal aux intentions de l’application. L’entité ordinale est prise en charge dans de [nombreuses cultures](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Types d’ordinaux
L’entité ordinal est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Résolution pour une entité ordinal prédéfinie

### <a name="api-version-2x"></a>API version 2.x

L’exemple suivant montre la résolution de l’entité **builtin.ordinal**.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>API 3.x en préversion

Le code JSON suivant a le paramètre `verbose` défini sur `false` :

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ]
        }
    }
}
```

Le code JSON suivant a le paramètre `verbose` défini sur `true` :

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ],
            "$instance": {
                "ordinal": [
                  {
                    "type": "builtin.ordinal",
                    "text": "second",
                    "startIndex": 10,
                    "length": 6,
                    "modelTypeId": 2,
                    "modelType": "Prebuilt Entity Extractor",
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

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [phone number](luis-reference-prebuilt-phonenumber.md) et [temperature](luis-reference-prebuilt-temperature.md). 
