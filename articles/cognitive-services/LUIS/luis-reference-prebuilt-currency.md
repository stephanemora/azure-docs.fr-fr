---
title: Entité prédéfinis de devise
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie currency dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 1dfa81ad7981578d4f296de1b421c7e064819718
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867287"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie currency pour une application LUIS
L’entité de devise prédéfinis détecte la devise dans de nombreux dénominations et pays/régions, quelle que soit la culture d’application LUIS. Étant donné que cette entité est déjà formée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant des devises aux intentions de l’application. L’entité currency est prise en charge dans de [nombreuses cultures](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Types d’entités currency
L’entité currency est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Résolution pour l’entité currency
L’exemple suivant montre la résolution de l’entité **builtin.currency**.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimension](luis-reference-prebuilt-dimension.md) [email](luis-reference-prebuilt-email.md). 
