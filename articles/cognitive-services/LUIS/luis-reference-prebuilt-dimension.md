---
title: Entités prédéfinies de dimension
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie dimension dans Language Understanding (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 9099cdbb91e41998065d953b9d48b3b501df7c10
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336935"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie dimension pour une application LUIS
L’entité prédéfinie dimension détecte différents types de dimensions, indépendamment de la culture de l’application LUIS. Étant donné que cette entité est déjà formée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant des dimensions aux intentions de l’application. L’entité dimension est prise en charge dans de [nombreuses cultures](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Types d’entités dimension

L’entité dimension est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)


## <a name="resolution-for-dimension-entity"></a>Résolution pour l’entité dimension
L’exemple suivant montre la résolution de l’entité **builtin.dimension**.

```json
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) et [ordinal](luis-reference-prebuilt-ordinal.md). 
