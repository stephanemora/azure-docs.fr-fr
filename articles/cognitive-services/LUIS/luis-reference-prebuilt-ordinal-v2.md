---
title: Entité prédéfinie V2 ordinal - LUIS
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations sur l’entité prédéfinie V2 ordinal dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 1579c50f2dcf78e350ce014ffb1516cec4ac85b3
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933486"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie V2 ordinal pour une application LUIS
Le numéro V2 ordinal développe [Ordinal](luis-reference-prebuilt-ordinal.md) pour fournir des références relatives telles que `next`, `last`, et `previous`. Ces informations ne sont pas extraites à l’aide de l’entité prédéfinie ordinale.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Résolution pour une entité prédéfinie V2 ordinal

### <a name="api-version-2x"></a>API version 2.x

L’exemple suivant montre la résolution de l’entité **builtin.ordinalV2**.

```json
{
    "query": "what is the second to last choice in the list",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.823669851
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.823669851
        }
    ],
    "entities": [
        {
            "entity": "the second to last",
            "type": "builtin.ordinalV2.relative",
            "startIndex": 8,
            "endIndex": 25,
            "resolution": {
                "offset": "-1",
                "relativeTo": "end"
            }
        }
    ]
}
```

### <a name="preview-api-version-3x"></a>API 3.x en préversion

Le code JSON suivant a le paramètre `verbose` défini sur `false` :

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ]
        }
    }
}
```

Le code JSON suivant a le paramètre `verbose` défini sur `true` :

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ],
            "$instance": {
                "ordinalV2": [
                    {
                        "type": "builtin.ordinalV2.relative",
                        "text": "the second to last",
                        "startIndex": 8,
                        "length": 18,
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

En savoir plus sur les entités [percentage](luis-reference-prebuilt-percentage.md), [phone number](luis-reference-prebuilt-phonenumber.md) et [temperature](luis-reference-prebuilt-temperature.md). 
