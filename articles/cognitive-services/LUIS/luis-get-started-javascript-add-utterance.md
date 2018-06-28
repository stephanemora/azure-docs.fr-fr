---
title: 'Didacticiel : Apprendre à ajouter des énoncés à une application LUIS à l’aide de JavaScript | Microsoft Docs'
description: Dans ce didacticiel, vous allez apprendre à appeler une application LUIS à l’aide de JavaScript.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265457"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>Didacticiel : Ajouter des énoncés à une application à l’aide de JavaScript
Dans ce didacticiel, vous allez écrire un programme pour ajouter un énoncé à une intention à l’aide des API de création dans JavaScript.

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer un projet de console Visual Studio 
> * Ajouter la méthode pour appeler l’API LUIS afin d’ajouter l’énoncé et d’effectuer l’apprentissage de l’application
> * Ajouter le fichier JSON contenant des exemples d’énoncés pour l’intention BookFlight
> * Exécuter la console et observer l’état d’apprentissage des énoncés

Pour plus d’informations, consultez la documentation technique pour les API [d’ajout d’exemple d’énoncé à une intention](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [d’apprentissage](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) et [d’état d’apprentissage](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin de créer votre application LUIS.

## <a name="prerequisites"></a>Prérequis
* Votre [**clé de création**](luis-concept-keys.md#authoring-key) LUIS. 
* Votre **ID d’application** et **ID de version** LUIS existants. 
* Un fichier nommé projet `add-utterances.html` dans VSCode.

> [!NOTE] 
> Le fichier `add-utterances.html` complet est disponible dans le [référentiel Github **LUIS-Samples**](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html) (Exemples-LUIS).


## <a name="write-the-code"></a>Écrire le code
Créez `add-utterances.html` et ajoutez le code suivant :

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>Afficher dans le navigateur
1. Ouvrez le fichier dans un navigateur.

2. Ajoutez votre ID de création LUIS, votre ID d’application LUIS et modifiez la version si elle est différente de `0.1`.

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
Une fois que vous avez terminé ce didacticiel, supprimez l’application console et Visual Studio si vous n’en avez plus besoin. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Intégrer LUIS à un robot](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website