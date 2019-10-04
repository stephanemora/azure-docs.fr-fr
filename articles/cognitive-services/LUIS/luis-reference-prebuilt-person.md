---
title: Entité prédéfinie PersonName - LUIS
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations sur l’entité prédéfinie personName de l’API Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a9da5089c9309ec06f29fd3e0868102cfa538049
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933458"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie PersonName pour une application LUIS
L’entité prédéfinie personName détecte le nom des personnes. Étant donné que cette entité est déjà entraînée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant personName aux intentions de l’application. L’entité personName est prise en charge pour les [cultures](luis-reference-prebuilt-entities.md) Anglais et Chinois.

## <a name="resolution-for-personname-entity"></a>Résolution de l’entité personName

### <a name="api-version-2x"></a>API version 2.x

L’exemple suivant montre la résolution de l’entité **builtin.personName**.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  },
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>API 3.x en préversion

Le code JSON suivant a le paramètre `verbose` défini sur `false` :

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ]
        }
    }
}
```

Le code JSON suivant a le paramètre `verbose` défini sur `true` :

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ],
            "$instance": {
                "personName": [
                    {
                        "type": "builtin.personName",
                        "text": "Jill Jones",
                        "startIndex": 3,
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

En savoir plus sur les entités [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) et [ordinal](luis-reference-prebuilt-ordinal.md). 
