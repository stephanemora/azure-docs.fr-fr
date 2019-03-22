---
title: Entité prédéfinie PersonName
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations sur l’entité prédéfinie personName de l’API Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 7b748c507d5c848cc83a8a0c55cb7b05903bc542
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958523"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie PersonName pour une application LUIS
L’entité prédéfinie personName détecte le nom des personnes. Étant donné que cette entité est déjà entraînée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant personName aux intentions de l’application. L’entité personName est prise en charge pour les [cultures](luis-reference-prebuilt-entities.md) Anglais et Chinois.

## <a name="resolution-for-personname-entity"></a>Résolution de l’entité personName
L’exemple suivant montre la résolution de l’entité **builtin.personName**.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) et [ordinal](luis-reference-prebuilt-ordinal.md). 
