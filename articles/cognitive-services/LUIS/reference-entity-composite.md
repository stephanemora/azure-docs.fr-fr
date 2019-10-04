---
title: Type d’entité composite - LUIS
titleSuffix: Azure Cognitive Services
description: Une entité composite est constituée d’autres entités (prédéfinies, simples, expressions régulières et listes). Les entités distinctes forment une entité entière.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ea258275cf954bc6e06da03324c2ae93de0e7fde
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563231"
---
# <a name="composite-entity"></a>Entité composite 

Une entité composite est constituée d’autres entités (prédéfinies, simples, expressions régulières et listes). Les entités distinctes forment une entité entière. 

**Cette entité convient bien lorsque les données :**

* Sont associées. 
* Sont liés l’un à l’autre dans le contexte de l’énoncé.
* Utilisent divers types d’entités.
* Doivent être regroupées et traitées par l’application cliente en tant qu’unité d’informations.
* Ont divers énoncés d’utilisateur nécessitant un apprentissage automatique.

![entité composite](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Exemple JSON

Prenons l’exemple d’une entité composite `number` et `Location::ToLocation` prédéfinie avec l’énoncé suivant :

`book 2 tickets to paris`

Notez qu’entre `2`, le nombre, et `paris`, ToLocation, se trouvent des mots qui ne font partie d’aucune entité. Le soulignement vert, utilisé dans un énoncé étiqueté sur le site web [LUIS](luis-reference-regions.md), indique une entité composite.

![Entité composite](./media/luis-concept-data-extraction/composite-entity.png)

Les entités composites sont retournées dans un tableau `compositeEntities`, et toutes les entités qui la composent dans le tableau `entities` :

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|Objet de données|Nom de l’entité|Valeur|
|--|--|--|
|Entité prédéfinie – nombre|"builtin.number"|"2"|
|Entité prédéfinie - GeographyV2|"Location::ToLocation"|"paris"|

## <a name="next-steps"></a>Étapes suivantes

Dans ce [tutoriel](luis-tutorial-composite-entity.md), vous allez ajouter une **entité composite** pour regrouper les données extraites dans une seule entité contenante. Grâce au regroupement des données, l’application cliente peut extraire facilement les données associées dans différents types de données.
