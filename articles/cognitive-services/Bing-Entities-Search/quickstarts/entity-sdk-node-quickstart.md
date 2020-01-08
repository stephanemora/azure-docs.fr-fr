---
title: 'Démarrage rapide : Envoyer une requête de recherche avec le SDK pour Node.js - Recherche d’entités Bing'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour rechercher des entités avec le SDK Recherche d’entités Bing pour Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 9fdb1e68a413adcbf5d27ec3a90cd79fdeb29f21
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384380"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Démarrage rapide : Envoyer une requête de recherche avec le SDK Recherche d’entités Bing pour Node.js

Utilisez ce guide de démarrage rapide pour commencer à rechercher des entités avec le SDK Recherche d’entités Bing pour Node.js. Si l’outil Recherche d’entités Bing a une API REST compatible avec la plupart des langages de programmation, le SDK offre quant à lui un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Conditions préalables requises

* La dernière version de [Node.js](https://nodejs.org/en/download/).

* Le [SDK Recherche d’entités Bing pour Node.js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Pour installer le SDK Recherche d’entités Bing :

1. Exécutez `npm install ms-rest-azure` dans votre environnement de développement.
2. Exécutez `npm install azure-cognitiveservices-entitysearch` dans votre environnement de développement.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier JavaScript dans votre éditeur ou IDE favori, puis ajoutez les exigences suivantes. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Créez une instance de `CognitiveServicesCredentials` à l’aide de votre clé d’abonnement. Ensuite, créez une instance du client de recherche avec cette dernière.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Envoyer une requête et recevoir une réponse

1. Envoyez une requête de recherche d’entités avec `entitiesOperations.search()`. Après avoir reçu une réponse, imprimez le `queryContext`, le nombre de résultats retournés et la description du premier résultat.
      
    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorial-bing-entities-search-single-page-app.md)

* [Qu’est-ce que l’API Recherche d’entités Bing ?](../overview.md )