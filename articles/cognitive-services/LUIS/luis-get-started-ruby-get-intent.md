---
title: Démarrage rapide pour Ruby - Prédire une intention - LUIS
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, utilisez une application LUIS publique disponible pour déterminer l’intention d’un utilisateur à partir du texte conversationnel. À l’aide de Ruby, envoyez l’intention de l’utilisateur sous forme de texte au point de terminaison de prédiction HTTP de l’application publique. Au niveau du point de terminaison, LUIS applique le modèle de l’application publique pour analyser le sens du texte en langage naturel, déterminer l’intention globale et extraire les données pertinentes pour le domaine de l’application.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: b8ef763a0b2379115150c88becc2ad43ba9cde89
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030929"
---
# <a name="quickstart-get-intent-using-ruby"></a>Démarrage rapide : Reconnaître une intention à l’aide de Ruby

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Prérequis

* Langage de programmation [Ruby](https://www.ruby-lang.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

<a name="create-luis-subscription-key"></a>

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Reconnaître une intention avec le navigateur

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation

Vous pouvez utiliser Ruby pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente. 

1. Copiez le code qui suit et enregistrez-le dans un fichier nommé `endpoint-call.rb` :

   [!code-ruby[Ruby code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/ruby/endpoint-call.rb)]

2. Remplacez `"YOUR-KEY"` par votre clé de point de terminaison.

3. Exécutez l’application Ruby dans la ligne de commande avec `ruby endpoint-call.rb`. Des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur s’affichent.

    ```
    LUIS query: turn on the left light
    
    Request URI: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn+on+the+left+light&timezoneOffset=0&verbose=false&spellCheck=false&staging=false
    
    JSON Response:
    
    {
      "query": "turn on the left light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.933549
      },
      "entities": [
        {
          "entity": "left",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 15,
          "score": 0.540835142
        }
      ]
    }
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Supprimez le fichier Ruby.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des énoncés](luis-get-started-ruby-add-utterance.md)