---
title: Entité prédéfinie keyPhrase
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie keyPhrase dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 5ef7ccb58533161d8397ad42e70de1999908dc36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473261"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie keyPhrase pour une application LUIS
KeyPhrase extrait une variété d’expressions clés d’un énoncé. Il est inutile d’ajouter des exemples d’énoncés contenant keyPhrase à l’application. L’entité keyPhrase est prise en charge dans de [nombreuses cultures](luis-language-support.md#languages-supported) dans les fonctionnalités [Analyse de texte](../text-analytics/overview.md). 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Résolution pour une entité prédéfinie keyPhrase
L’exemple suivant montre la résolution de l’entité **builtin.keyPhrase**.

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) et [age](luis-reference-prebuilt-age.md).
