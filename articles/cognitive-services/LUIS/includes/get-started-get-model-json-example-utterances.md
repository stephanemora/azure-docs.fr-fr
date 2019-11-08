---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/19/2019
ms.author: diberry
ms.openlocfilehash: ba400beb13d0bd4b29eee0c60617ec7ad128dd77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505826"
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
