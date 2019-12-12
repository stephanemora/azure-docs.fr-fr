---
title: 'Démarrage rapide : Rechercher des images - Kit de développement logiciel (SDK) Recherche d’images Bing pour Node.js'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour effectuer votre première recherche d’images à l’aide du SDK Recherche d’images Bing, qui est un wrapper de l’API et contient les mêmes fonctionnalités. Cette application Node.js simple envoie une requête de recherche d’image, analyse la réponse JSON et affiche l’URL de la première image retournée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: e41c3409ac5b81fe9e099ab34abd7256ef39d330
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930571"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-nodejs"></a>Démarrage rapide : Rechercher des images avec SDK Recherche d’images Bing pour Node.js

Utilisez ce guide de démarrage rapide pour effectuer votre première recherche d’images à l’aide du SDK Recherche d’images Bing, qui est un wrapper de l’API et contient les mêmes fonctionnalités. Cette application JavaScript simple envoie une requête de recherche d’image, analyse la réponse JSON et affiche l’URL de la première image retournée.

Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) avec la gestion des erreurs et des annotations supplémentaires.

## <a name="prerequisites"></a>Prérequis

* Le [SDK Recherche d’images Cognitive Services pour Node.js](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Installer à l’aide de `npm install azure-cognitiveservices-imagesearch`
* Le module [Node.js Azure Rest](https://www.npmjs.com/package/ms-rest-azure)
    * Installer à l’aide de `npm install ms-rest-azure`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier JavaScript dans l’éditeur ou IDE de votre choix et définissez la sévérité, https et autres exigences.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. Dans la méthode principale de votre projet, créez des variables pour votre clé d’abonnement valide, les résultats d’image devant être retournés par Bing et un terme de recherche. Ensuite, instanciez le client de recherche d’image à l’aide de la clé.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Créer une fonction d’assistance asynchrone

1. Créez une fonction pour appeler le client de façon asynchrone et retournez la réponse du service Recherche d’images Bing.  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
   ## <a name="send-a-query-and-handle-the-response"></a>Envoyer une requête et gérer la réponse

1. Appelez la fonction d’assistance et gérez son élément `promise` pour analyser les résultats d’image retournés dans la réponse.

    Si la réponse contient des résultats de recherche, stockez le premier résultat et imprimez ses détails, tels qu’une URL de miniature, l’URL d’origine, ainsi que le nombre total d’images retournées.  
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel d’une application monopage Recherche d’images Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que la Recherche d’images Bing ?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Essayez une démonstration interactive en ligne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obtenir une clé d’accès Cognitive Services gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Exemples Node.js pour le SDK Azure Cognitive Services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Documentation Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Informations de référence sur l’API Recherche d’images Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
