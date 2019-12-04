---
title: 'Démarrage rapide : Effectuer une recherche d’actualités avec le SDK pour Node.js - Recherche d’actualités Bing'
titleSuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour rechercher des actualités à l’aide du Kit de développement logiciel (SDK) Recherche d’actualités Bing pour Node.js et traiter la réponse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9bbad7685cf7d562189871bb5fa4a56d69287db4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383896"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Démarrage rapide : Effectuer une recherche d’actualités avec le Kit de développement logiciel (SDK) Recherche d’actualités Bing pour Node.js

Utilisez ce guide de démarrage rapide pour démarrer une recherche d’actualités avec le SDK Recherche d’actualités Bing pour Node.js. Si l’outil Recherche d’actualités Bing a une API REST compatible avec la plupart des langages de programmation, le SDK offre quant à lui un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Prérequis

* [Node.JS](https://nodejs.org/en/)

Pour configurer une application console à l’aide du SDK Recherche d’actualités Bing :
1. Exécutez `npm install ms-rest-azure` dans votre environnement de développement.
2. Exécutez `npm install azure-cognitiveservices-newssearch` dans votre environnement de développement.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez une instance de `CognitiveServicesCredentials`. Créez des variables pour votre clé d’abonnement et un terme de recherche.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. Instanciez le client :
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Envoyer une requête de recherche

1. Utilisez le client pour rechercher un terme de requête, en l’occurrence « Winter Olympics » :
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

Le code envoie les éléments `result.value` à la console sans analyser le texte. Les résultats, le cas échéant par catégorie, incluent les éléments suivants :

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](tutorial-bing-news-search-single-page-app.md)
