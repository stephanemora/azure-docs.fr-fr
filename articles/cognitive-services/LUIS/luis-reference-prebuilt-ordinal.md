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
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 4992ca9d1a09fa751697d6608400eb4dda688108
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473166"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie ordinal pour une application LUIS
Un nombre ordinal est une représentation numérique d’un objet à l’intérieur d’un ensemble : `first`, `second`, `third`. Étant donné que cette entité est déjà entraînée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant un ordinal aux intentions de l’application. L’entité ordinale est prise en charge dans de [nombreuses cultures](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Types d’ordinaux
L’entité ordinal est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Résolution pour une entité ordinal prédéfinie
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

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [percentage](luis-reference-prebuilt-percentage.md), [phonenumber](luis-reference-prebuilt-phonenumber.md) et [temperature](luis-reference-prebuilt-temperature.md). 
