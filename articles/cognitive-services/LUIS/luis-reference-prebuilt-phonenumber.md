---
title: Entités prédéfinies de numéro de téléphone
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie phonenumber dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6b6474375b0f62f80903feb4eeee47f03016786b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208229"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie phonenumber pour une application LUIS
L’entité `phonenumber` extrait une série de numéros de téléphone, avec le code pays. Étant donné que cette entité est déjà formée, il est inutile d’ajouter des exemples d’énoncés à l’application. L’entité `en-us` est prise en charge uniquement dans la culture `phonenumber`. 

## <a name="types-of-phonenumber"></a>Types d’entités phonenumber
L’entité phonenumber est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Résolution pour l’entité prédéfinie phonenumber
L’exemple suivant montre la résolution de l’entité **builtin.phonenumber**.

```json
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) et [temperature](luis-reference-prebuilt-temperature.md). 
