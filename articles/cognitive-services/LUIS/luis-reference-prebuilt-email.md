---
title: Documentation de référence pour l’entité prédéfinie email de LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Cet article contient des informations sur l’entité prédéfinie email dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 51e6a5da0d757023bd7cd1f61c77387a77b77de4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869034"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie email pour une application LUIS
L’extraction d’e-mail inclut l’adresse e-mail complète d’un énoncé. Étant donné que cette entité est déjà formée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant un email aux intentions de l’application. L’entité email n’est prise en charge que dans la culture `en-us`. 

## <a name="resolution-for-prebuilt-email"></a>Résolution pour l’entité prédéfinie email
L’exemple suivant montre la résolution de l’entité **builtin.email**.

```json
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [number](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md) et [percentage](luis-reference-prebuilt-percentage.md). 
