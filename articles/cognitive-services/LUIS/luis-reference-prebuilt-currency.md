---
title: Documentation de référence pour l’entité prédéfinie currency de LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie currency dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: b383e21f870d15818c540b79a9a56c1dd65fa342
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236820"
---
# <a name="currency-entity"></a>Entité currency
L’entité prédéfinie currency détecte une devise dans un grand nombre de  dénominations et de pays, quelle que soit la culture de l’application LUIS. Étant donné que cette entité est déjà formée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant des devises aux intentions de l’application. L’entité currency est prise en charge dans de [nombreuses cultures](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Types d’entités currency
L’entité currency est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Résolution pour l’entité currency
L’exemple suivant montre la résolution de l’entité **builtin.currency**.

```JSON
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