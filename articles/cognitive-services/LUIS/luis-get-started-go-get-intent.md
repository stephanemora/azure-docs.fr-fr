---
title: Reconnaître l’intention, Go
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dans ce démarrage rapide Go, utilisez une application LUIS publique disponible pour déterminer l’intention d’un utilisateur à partir du texte conversationnel.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: e77bd2abcb5810810debc7782b717db47620baaa
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847456"
---
# <a name="quickstart-get-intent-using-go"></a>Démarrage rapide : Reconnaître l’intention à l’aide de Go

Dans ce démarrage rapide, vous allez transmettre des énoncés à un point de terminaison LUIS et obtenir en retour une intention et des entités.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Prérequis

* Langage de programmation [GO](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Reconnaître une intention avec le navigateur

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Reconnaître une intention par programmation

Vous pouvez utiliser GO pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente. 

1. Créez un nouveau fichier appelé `endpoint.go`. Ajoutez le code suivant :
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. À partir d’une invite de commandes dans le répertoire où vous avez créé le fichier, saisissez `go build endpoint.go` pour compiler le fichier GO. L’invite de commande ne retourne pas d’informations pour une génération réussie.

3. Exécutez l’application GO à partir de la ligne de commande en entrant le texte suivant dans l’invite de commandes : 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    Remplacez `<add-your-key>` par la valeur de votre clé.  
    
    La réponse de l’invite de commandes est : 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
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
    
## <a name="luis-keys"></a>Clés LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources
Fermez le fichier GO et supprimez-le du système de fichiers. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Ajouter des énoncés](luis-get-started-go-add-utterance.md)