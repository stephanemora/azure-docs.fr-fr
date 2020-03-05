---
title: Entités prédéfinies URL – LUIS
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations sur l’entité prédéfinie URL dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 49f145ce3e9022826abad5d274dd611bb2cc6530
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270343"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie URL pour une application LUIS
L’entité URL extrait des URL avec des noms de domaine ou des adresses IP. Étant donné que cette entité est déjà formée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant des URL à l’application. L’entité URL est prise en charge uniquement dans la culture `en-us`.

## <a name="types-of-urls"></a>Types d’entités URL
L’entité URL est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Résolution pour une entité prédéfinie URL

Les objets entité suivants sont retournés pour la requête :

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[Réponse V3](#tab/V3)

Le code JSON suivant a le paramètre `verbose` défini sur `false` :

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Réponse détaillée V3](#tab/V3-verbose)

Le code JSON suivant a le paramètre `verbose` défini sur `true` :

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
                "length": 17,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Réponse V2](#tab/V2)

L’exemple suivant montre que la résolution de https://www.luis.ai est un excellent exemple de services cognitifs d’intelligence artificielle.

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

En savoir plus sur les entités [ordinal](luis-reference-prebuilt-ordinal.md), [number](luis-reference-prebuilt-number.md) et [temperature](luis-reference-prebuilt-temperature.md).
