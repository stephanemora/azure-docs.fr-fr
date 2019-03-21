---
title: Entité prédéfinie geographyV2
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations sur l’entité prédéfinie geographyV2 dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 17f612f2ee6c7d27dcec9f72ed3df1ed418eb3d2
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961603"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie geographyV2 pour une application LUIS
L’entité prédéfinie geographyV2 détecte les emplacements géographiques. Étant donné que cette entité est déjà entraînée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant geographyV2 aux intentions de l’application. L’entité geographyV2 est prise en charge pour la [culture](luis-reference-prebuilt-entities.md) Anglais.

## <a name="subtypes"></a>Sous-types
Les emplacements géographiques ont des sous-types :

|Subtype|Objectif|
|--|--|
|`poi`|Point d’intérêt|
|`city`|Nom de la ville|
|`countryRegion`|Nom du pays ou de la région|
|`continent`|Nom du continent|
|`state`|Nom de l’état ou de la province|


## <a name="resolution-for-geographyv2-entity"></a>Résolution de l’entité geographyV2
L’exemple suivant montre la résolution de l’entité **builtin.geographyV2**.

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) et [ordinal](luis-reference-prebuilt-ordinal.md). 
