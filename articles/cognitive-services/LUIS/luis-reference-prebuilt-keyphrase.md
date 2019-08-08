---
title: Entité prédéfinie keyPhrase – LUIS
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations sur l’entité prédéfinie keyPhrase dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d7ecb579305cb17525e220bc16be430c77d25080
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563428"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie keyPhrase pour une application LUIS
KeyPhrase extrait une variété d’expressions clés d’un énoncé. Il est inutile d’ajouter des exemples d’énoncés contenant keyPhrase à l’application. L’entité keyPhrase est prise en charge dans de [nombreuses cultures](luis-language-support.md#languages-supported) dans les fonctionnalités [Analyse de texte](../text-analytics/overview.md). 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Résolution pour une entité prédéfinie keyPhrase

### <a name="api-version-2x"></a>API version 2.x

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
