---
title: Type d’entité simple - LUIS
titleSuffix: Azure Cognitive Services
description: Une entité simple est une entité générique qui décrit un concept unique et est apprise à partir d’un contexte issu de l’apprentissage automatique. Les entités simples étant généralement des noms tels que des noms de société, des noms de produits ou d’autres catégories de noms, ajoutez une liste de phrases lorsque vous utilisez une entité simple pour renforcer le signal des noms utilisés.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: c1514b6cd512924a162a524d11e888055fa06514
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563202"
---
# <a name="simple-entity"></a>Entité simple 

Une entité simple est une entité générique qui décrit un concept unique et est apprise à partir d’un contexte issu de l’apprentissage automatique. Les entités simples étant généralement des noms tels que des noms de société, des noms de produits ou d’autres catégories de noms, ajoutez une [liste de phrases](luis-concept-feature.md) lorsque vous utilisez une entité simple pour renforcer le signal des noms utilisés. 

**L’entité convient bien quand :**

* Les données ne sont mises en forme de façon cohérente, mais indiquent la même chose. 

![entité simple](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Exemple JSON

`Bob Jones wants 3 meatball pho`

Dans l’énoncé précédent, `Bob Jones` est marqué comme entité simple `Customer`.

Le point de terminaison retourne les données suivantes : nom de l’entité, texte découvert dans l’énoncé, emplacement de ce texte et score.

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Objet de données|Nom de l’entité|Valeur|
|--|--|--|
|Entité simple|`Customer`|`bob jones`|

## <a name="next-steps"></a>Étapes suivantes

Dans ce [tutoriel](luis-quickstart-primary-and-secondary-data.md), vous allez extraire les données de nom de poste de travail issues de l’apprentissage automatique à partir d’un énoncé utilisant l’**entité Simple**. Pour augmenter la précision de l’extraction, ajoutez une [liste d’expressions](luis-concept-feature.md) dont les termes sont spécifiques à l’entité simple.