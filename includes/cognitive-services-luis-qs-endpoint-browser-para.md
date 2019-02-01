---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 1b70c5e71c585524579171782dfdeda308592b30
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478700"
---
Pour comprendre ce que retourne un point de terminaison de prédiction LUIS, affichez un résultat de prédiction dans un navigateur web. Pour interroger une application publique, vous avez besoin de votre propre clé et de l’ID d’application. L’ID d’application IoT publique, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, est fournie dans le cadre de l’URL à l’étape 1.

Le format de l’URL pour une requête de point de terminaison **GET** est :

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. Le point de terminaison de l’application IoT publique a le format suivant : `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    Copiez l’URL et remplacez la valeur de `<YOUR_KEY>` par votre clé.

2. Collez l’URL dans une fenêtre de navigateur, puis appuyez sur Entrée. Le navigateur affiche un résultat JSON qui indique que LUIS a détecté l’intention `HomeAutomation.TurnOn` comme première intention et l’entité `HomeAutomation.Room` pourvue de la valeur `bedroom`.

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. Modifiez la valeur du paramètre `q=` dans l’URL et remplacez-la par `turn off the living room light`, puis appuyez sur Entrée. Le résultat indique maintenant que LUIS a détecté l’intention `HomeAutomation.TurnOff` comme première intention et l’entité `HomeAutomation.Room` pourvue de la valeur `living room`. 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
