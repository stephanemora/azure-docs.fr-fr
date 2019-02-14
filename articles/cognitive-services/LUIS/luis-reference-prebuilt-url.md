---
title: Entités prédéfinies d’URL
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie URL dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 3e5f6edf213838345c21598213b4fc2065e66335
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884135"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie URL pour une application LUIS
L’entité URL extrait des URL avec des noms de domaine ou des adresses IP. Étant donné que cette entité est déjà formée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant des URL à l’application. L’entité URL est prise en charge uniquement dans la culture `en-us`. 

## <a name="types-of-urls"></a>Types d’entités URL
L’entité URL est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Résolution pour une entité prédéfinie URL
L’exemple suivant montre la résolution de l’entité **builtin.url**.

```json
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [ordinal](luis-reference-prebuilt-ordinal.md), [number](luis-reference-prebuilt-number.md) et [temperature](luis-reference-prebuilt-temperature.md).
