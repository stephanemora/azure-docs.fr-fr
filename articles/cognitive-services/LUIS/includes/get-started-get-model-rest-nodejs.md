---
title: Obtenir un modèle avec un appel REST en C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: a262db04e51015edb760a8b04952dfa24b2ad63a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748808"
---
## <a name="prerequisites"></a>Conditions préalables requises

* Une clé de démarrage.
* Importez l’application [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) à partir du dépôt GitHub cognitive-services-language-understanding.
* L’ID d’application LUIS pour l’application TravelAgent importée. L’ID d’application est indiqué dans le tableau de bord de l’application.
* L’ID de version dans l’application qui reçoit les énoncés. L’ID par défaut est « 0.1 ».
* Langage de programmation [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Exemples d’énoncés de fichier JSON

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Changer le modèle programmatiquement

Utilisez Go pour ajouter à l’application une [API](https://aka.ms/luis-apim-v3-authoring) d’entité issue du Machine Learning. 

1. Créez un nouveau fichier appelé `model.js`. Ajoutez le code suivant :

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    
    const LUIS_authoringKey = "YOUR-KEY";
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;
    
    const utterances = [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ];
    
    const main = async() =>{
    
    
        await addUtterance();
        await train("POST");
        await trainStatus("GET");
    
    }
    const addUtterance = async () => {
    
        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };
    
        const response = await requestpromise(options)
        console.log(response.body);
    }
    const train = async (verb) => {
    
        const options = {
            uri: addTrainURI,
            method: verb, 
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };
    
        const response = await requestpromise(options)
        console.log(response.body);
    }
    
    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```
1. Remplacez les valeurs suivantes :

    * `YOUR-KEY` par votre clé de démarrage
    * `YOUR-ENDPOINT` par votre point de terminaison, par exemple, `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` par l’ID de votre application

1. Avec une invite de commandes située dans le même répertoire que celui où vous avez créé le fichier, entrez la commande suivante pour exécuter le fichier :

    ```console
    node model.js
    ```  

## <a name="luis-keys"></a>Clés LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous aurez fini de suivre ce guide de démarrage rapide, supprimez le fichier du système de fichiers. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Meilleures pratiques pour une application](../luis-concept-best-practices.md)