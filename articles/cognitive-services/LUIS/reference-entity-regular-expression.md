---
title: Type d’entité d’expression régulière - LUIS
titleSuffix: Azure Cognitive Services
description: Une expression régulière est préférable à un texte d’énoncé brut. Elle ignore la casse et la variante culturelle.  La correspondance d’expression régulière est appliquée après les modifications de la vérification orthographique au niveau du caractère, et non au niveau du jeton.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 82cce359f2161800c53ccce7cdb0342bba759d43
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559939"
---
# <a name="regular-expression-entity"></a>Entité d’expression régulière 

Une entité d’expression régulière extrait une entité en fonction du modèle d’expression régulière que vous fournissez.

Une expression régulière est préférable à un texte d’énoncé brut. Elle ignore la casse et la variante culturelle.  La correspondance d’expression régulière est appliquée après les modifications de la vérification orthographique au niveau du caractère, et non au niveau du jeton. Si l’expression régulière est trop complexe (par exemple, si elle utilise de nombreux crochets), vous ne pouvez pas l’ajouter au modèle. Utilise une partie seulement de la bibliothèque [.Net Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions). 

**L’entité convient bien quand :**

* Les données sont constamment mis en forme avec toute variation également cohérente.
* L’expression régulière n’a pas besoin de plus de 2 niveaux d’imbrication. 

![Entité d’expression régulière](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considérations sur l’utilisation

Les expressions régulières peuvent correspondre à plus que ce à quoi vous vous attendez. La correspondance de mots numériques comme `one` et `two` en est un exemple. L’expression régulière suivante, qui fait correspondre le nombre `one` avec d’autres nombres, en est un exemple :

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Cette expression régulière fait également correspondre les mots qui se terminent par ces nombres, comme `phone`. Pour résoudre les problèmes de ce type, veillez à ce que les correspondances des expressions régulières prennent en compte les limites des mots. L’expression régulière pour utiliser les limites des mots pour cet exemple est utilisée dans l’expression régulière suivante :

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Exemple JSON

Dans le cas de l’utilisation de `kb[0-9]{6}` comme définition de l’entité d’expression régulière, la réponse JSON suivante est un exemple d’énoncé avec les entités d’expression régulière retournées pour la requête `When was kb123456 published?` :

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce [tutoriel](luis-quickstart-intents-regex-entity.md), créez une application pour extraire des données mises en forme de façon homogène à partir d’un énoncé avec l’entité **Expression régulière**.