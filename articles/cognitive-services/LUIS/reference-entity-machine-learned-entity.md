---
title: Type d’entité issue du Machine Learning - LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017206"
---
# <a name="machine-learned-entity"></a>Entité apprise par la machine 



**L’entité convient bien lorsque les données de texte :**


![entité de liste](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Exemple JSON

Supposons que l’application comporte une liste nommée `Cities`, permettant des variations de noms de ville : ville aéroportuaire (Sea-tac), indicatif d’aéroport (SEA), code postal (98101) et indicatif téléphonique régional (206).

|Élément de liste|Synonymes de l’élément|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Dans l’énoncé précédent, le mot `paris` est mappé à l’élément paris au sein de l’entité de liste `Cities`. Celle-ci est mise en correspondance avec le nom normalisé de l’élément ainsi que ses synonymes.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)


Il s’agit du JSON si `verbose=false` est défini dans la chaîne de requête :

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Il s’agit du JSON si `verbose=true` est défini dans la chaîne de requête :

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|Objet de données|Nom de l’entité|Valeur|
|--|--|--|
|Entité de liste|`Cities`|`paris`|


## <a name="next-steps"></a>Étapes suivantes

Découvrez les entités de [liste](reference-entity-list.md) et d’[expression régulière](reference-entity-regular-expression.md).