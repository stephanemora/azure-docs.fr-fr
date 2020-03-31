---
title: Entité prédéfinie phonenumber – LUIS
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations sur l’entité prédéfinie phonenumber dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270468"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie de numéro de téléphone pour une application LUIS
L’entité `phonenumber` extrait une série de numéros de téléphone, avec le code pays. Étant donné que cette entité est déjà formée, il est inutile d’ajouter des exemples d’énoncés à l’application. L’entité `phonenumber` est prise en charge uniquement dans la culture `en-us`.

## <a name="types-of-a-phone-number"></a>Types de numéros de téléphone
`Phonenumber` est gérée à partir du référentiel GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Résolution pour cette entité prédéfinie

Les objets entité suivants sont retournés pour la requête :

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[Réponse V3](#tab/V3)

Le code JSON suivant a le paramètre `verbose` défini sur `false` :

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Réponse détaillée V3](#tab/V3-verbose)
Le code JSON suivant a le paramètre `verbose` défini sur `true` :

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

L’exemple suivant montre la résolution de l’entité **builtin.phonenumber**.

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

En savoir plus sur les entités [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) et [temperature](luis-reference-prebuilt-temperature.md).
