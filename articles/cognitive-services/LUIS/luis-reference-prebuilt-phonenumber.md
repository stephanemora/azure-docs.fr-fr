---
title: Entités prédéfinies de numéro de téléphone
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie phonenumber dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 43d0b855c25ed10b074d99b247ee56dc2ba7769b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146175"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie numéro de téléphone pour une application LUIS
L’entité `phonenumber` extrait une série de numéros de téléphone, avec le code pays. Étant donné que cette entité est déjà formée, il est inutile d’ajouter des exemples d’énoncés à l’application. L’entité `en-us` est prise en charge uniquement dans la culture `phonenumber`. 

## <a name="types-of-a-phone-number"></a>Types d’un numéro de téléphone
`Phonenumber` est géré à partir de la [modules de reconnaissance de texte](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) référentiel GitHub

## <a name="resolution-for-this-prebuilt-entity"></a>Résolution de cette entité prédéfinie

### <a name="api-version-2x"></a>Version de l’API 2.x

L’exemple suivant montre la résolution de l’entité **builtin.phonenumber**.

```json
{
  "query": "my mobile is 1 (800) 642-7676",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Version d’évaluation API 3.x

Le code JSON suivant est avec la `verbose` paramètre défini sur `false`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

Le code JSON suivant est avec la `verbose` paramètre défini sur `true`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ],
            "$instance": {
                "phonenumber": [
                    {
                        "type": "builtin.phonenumber",
                        "text": "1 (800) 642-7676",
                        "startIndex": 13,
                        "length": 16,
                        "score": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) et [temperature](luis-reference-prebuilt-temperature.md). 
