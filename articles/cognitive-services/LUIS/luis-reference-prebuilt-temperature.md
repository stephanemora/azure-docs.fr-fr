---
title: Entité prédéfinie temperature
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie temperature dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 8d5e10a7a4fe7fb12bc4140690fcabffa16b32be
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072109"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie temperature pour une application LUIS
L’entité temperature extrait divers types de températures. Étant donné que cette entité est déjà formée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant des températures à l’application. L’entité temperature est prise en charge dans de [nombreuses cultures](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Types d’entités temperature
L’entité temperature est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Résolution pour entité prédéfinie temperature

### <a name="api-version-2x"></a>Version de l’API 2.x

L’exemple suivant montre la résolution de l’entité **builtin.temperature**.

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Version d’évaluation API 3.x

Le code JSON suivant est avec la `verbose` paramètre défini sur `false`:

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ]
        }
    }
}
```

Le code JSON suivant est avec la `verbose` paramètre défini sur `true`:

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ],
            "$instance": {
                "temperature": [
                    {
                        "type": "builtin.temperature",
                        "text": "30 degrees",
                        "startIndex": 23,
                        "length": 10,
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

En savoir plus sur les entités [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) et [age](luis-reference-prebuilt-age.md). 
