---
title: 'Démarrage rapide : Obtenir des insights sur les images à l’aide du SDK Recherche visuelle Bing pour Node.js'
titleSuffix: Azure Cognitive Services
description: Chargez une image à l’aide du SDK Recherche visuelle Bing pour récupérer des insights sur celle-ci.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: v-gedod
ms.openlocfilehash: 1dba0f3aacd0e51a9e4d8017a93f18928fd6b2ea
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744291"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>Démarrage rapide : Obtenir des insights sur les images à l’aide du SDK Recherche visuelle Bing pour Node.js

Utilisez ce guide de démarrage rapide pour commencer à obtenir des insights sur les images à partir du service Recherche visuelle Bing, à l’aide du SDK Node.js. Si l’outil Recherche visuelle Bing dispose d’une API REST compatible avec la plupart des langages de programmation, le SDK offre quant à lui un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

## <a name="prerequisites"></a>Prérequis
* [Node.JS](https://www.nodejs.org/)
* SDK Recherche visuelle Bing pour Node.js
    * Pour configurer une application console à l’aide du SDK Recherche visuelle Bing, exécutez les commandes suivantes :
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch`.


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier JavaScript dans votre éditeur ou IDE favori, puis ajoutez les exigences suivantes. Créez ensuite des variables pour votre clé d’abonnement, votre ID de configuration personnalisée et le chemin d’accès à l’image que vous souhaitez charger. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Instanciez le client.

    ```javascript
    let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);
    ```

## <a name="search-for-images"></a>Rechercher des images

1. Utilisez `fs.createReadStream()` pour lire le contenu de votre fichier image et créez des variables pour votre demande et vos résultats de recherche. Utilisez ensuite le client pour rechercher des images.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchApiClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Analysez les résultats de la requête précédente :

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](tutorial-bing-visual-search-single-page-app.md)
