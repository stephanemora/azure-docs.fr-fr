---
title: 'Démarrage rapide : Apprendre à ajouter des énoncés à une application LUIS à l’aide de JavaScript - Azure Cognitive Services | Microsoft Docs'
description: Dans ce démarrage rapide, vous allez apprendre à appeler une application LUIS à l’aide de JavaScript.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: 0920a194d3e9c93883b88b7131f7e81dc8fb3302
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159688"
---
# <a name="quickstart-change-model-using-javascript"></a>Démarrage rapide : Modifier un modèle à l’aide de JavaScript

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Exemples d’énoncés de fichier JSON

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>Créer un code de démarrage rapide

Créez `add-utterances.html` et ajoutez le code suivant :

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>Exécuter le code

1. Ouvrez le fichier dans un navigateur.

2. Ajoutez votre ID de création LUIS, votre ID d’application LUIS.

3. Modifiez le **tableau d’énoncés** à ajouter à votre application. Ils sont stockés dans la variable utteranceJSON. Modifiez ces valeurs en fonction de vos besoins en matière d’énoncés et de domaines. 

    ```json
    // example batch utterances
    var utteranceJSON = [
        {
            "text": "go to Seattle",
            "intentName": "BookFlight",
            "entityLabels": [
                {
                    "entityName": "Location::LocationTo",
                    "startCharIndex": 6,
                    "endCharIndex": 12
                }
            ]
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. Sélectionnez le bouton `Upload utterance`. Les résultats LUIS sont affichés sous les boutons.

5. Sélectionnez le bouton `Train model` pour effectuer l’apprentissage de votre application avec ces nouveaux énoncés.

6. Sélectionnez le bouton `Train Status` pour afficher l’état d’apprentissage. 

    ![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
Une fois le démarrage rapide terminé, supprimez tous les fichiers créés pour ce démarrage rapide. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Intégrer LUIS à un robot](luis-csharp-tutorial-build-bot-framework-sample.md)
