---
title: Type d’entité d’expression régulière - LUIS
description: Une expression régulière est préférable à un texte d’énoncé brut. Elle ignore la casse et la variante culturelle.  La correspondance d’expression régulière est appliquée après les modifications de la vérification orthographique au niveau du caractère, et non au niveau du jeton.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: e53d21cc4e28f6c388b4e4f980a3352021fc2bcb
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108803401"
---
# <a name="regular-expression-entity"></a>Entité d’expression régulière

Une entité d’expression régulière extrait une entité en fonction du modèle d’expression régulière que vous fournissez.

Une expression régulière est préférable à un texte d’énoncé brut. Elle ignore la casse et la variante culturelle.  La correspondance d’expression régulière est appliquée après les modifications de la vérification orthographique au niveau du jeton. Si l’expression régulière est trop complexe (par exemple, si elle utilise de nombreux crochets), vous ne pouvez pas l’ajouter au modèle. Utilise une partie seulement de la bibliothèque [.Net Regex](/dotnet/standard/base-types/regular-expressions).

**L’entité convient bien quand :**

* Les données sont constamment mis en forme avec toute variation également cohérente.
* L’expression régulière n’a pas besoin de plus de 2 niveaux d’imbrication.

![Entité d’expression régulière](./media/luis-concept-entities/regex-entity.png)

## <a name="example-json"></a>Exemple JSON

Dans le cas de l’utilisation de `kb[0-9]{6}` comme définition de l’entité d’expression régulière, la réponse JSON suivante est un exemple d’énoncé avec les entités d’expression régulière retournées pour la requête :

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)


Il s’agit du JSON si `verbose=false` est défini dans la chaîne de requête :

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Il s’agit du JSON si `verbose=true` est défini dans la chaîne de requête :

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités :

* [Concepts](luis-concept-entity-types.md)
* [Création](luis-how-to-add-entities.md)
