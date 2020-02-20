---
title: Fichier include
description: Fichier include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: e879afdbd4c34e9d74405644de86421fb2cbab46
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279538"
---
Les exemples d’énoncés suivent un format spécifique.

Le champ `text` contient le texte de l’exemple d’énoncé. Le champ `intentName` doit correspondre au nom d’une intention existante dans l’application LUIS. Le champ `entityLabels` est obligatoire. Si vous ne souhaitez étiqueter aucune entité, fournissez un tableau vide.

Si le tableau entityLabels n’est pas vide, les éléments `startCharIndex` et `endCharIndex` doivent marquer l’entité concernée dans le champ `entityName`. L’index est de base zéro, ce qui signifie que 6 dans l’exemple du haut fait référence au « S » de Seattle, et non à l’espace avant le S majuscule. Si vous commencez ou finissez l’étiquette au niveau d’un espace dans le texte, l’appel d’API pour ajouter les énoncés échoue.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
