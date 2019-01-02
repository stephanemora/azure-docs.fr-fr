---
title: Entité prédéfinie keyPhrase
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie keyPhrase dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 684bba0c2f0c6fbaf05ce25ce543da413f1b71e2
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141218"
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
