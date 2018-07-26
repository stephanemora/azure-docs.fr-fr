---
title: Documentation de référence pour l’entité prédéfinie age de LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie age dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: f93acb8bece8c66c3ed7197f1c4530011aec3f29
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237398"
---
# <a name="age-entity"></a>Entité age
L’entité prédéfinie age capture la valeur d’âge à la fois sous forme numérique et en termes de jours, semaines, mois et années. Étant donné que cette entité est déjà formée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant un âge aux intentions de l’application. L’entité age est prise en charge dans de [nombreuses cultures](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Types d’entité age
L’entité age est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3)

## <a name="resolution-for-prebuilt-age-entity"></a>Résolution pour une entité prédéfinie age
L’exemple suivant montre la résolution de l’entité **builtin.age**.

```JSON
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [currency](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) et [dimension](luis-reference-prebuilt-dimension.md). 