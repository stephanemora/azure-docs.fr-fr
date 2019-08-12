---
title: Type d’entité Pattern.any - LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any est un espace réservé à longueur variable utilisé uniquement dans le gabarit d’énoncé d’un modèle pour marquer où l’entité commence et se termine.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: cda6c724a36a73dc34c2bf8e7158e3e3ec92d46b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563228"
---
# <a name="patternany-entity"></a>Entité Pattern.any 

Pattern.any est un espace réservé à longueur variable utilisé uniquement dans le gabarit d’énoncé d’un modèle pour marquer où l’entité commence et se termine.  

Les entités pattern.any doivent être marquées dans les exemples de modèles [Pattern](luis-how-to-model-intent-pattern.md), et non dans les exemples d’utilisateurs d’intention.

**L’entité convient bien quand :**

* La fin de l’entité peut être confondue avec le reste du texte de l’énoncé. 

## <a name="usage"></a>Usage

Si une application cliente recherche des livres en fonction du titre, pattern.any extrait le titre complet. Un modèle d’énoncé utilisant pattern.any pour cette recherche de livre est `Was {BookTitle} written by an American this year[?]`. 

Dans le tableau suivant, chaque ligne contient deux versions de l’énoncé. L’énoncé principal est la manière dont LUIS a initialement pu voir l’énoncé. Il est clair que le titre du livre a un début et une fin. L’énoncé inférieur utilise une entité pattern.any pour marquer le début et la fin de l’entité. 

|Énoncé d’une entité en gras|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>**L’Homme qui prenait sa femme pour un chapeau** a-t-il été écrit par un Americain cette année ?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>**Nature morte avec pivert** a-t-il été par un Américain cette année ?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>La **singulière tristesse du gâteau au citron** est-il un roman écrit par un Américain cette année ?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**Le Petit Prince** a-t-il été écrit par un Américain cette année ?|
||

## <a name="example-json"></a>Exemple JSON

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce [tutoriel](luis-tutorial-pattern-any.md), vous allez utiliser l’entité **Pattern.any** pour extraire des données à partir d’énoncés correctement mis en forme et où la fin des données peut être facilement confondue avec les mots restants de l’énoncé.