---
title: 'Démarrage rapide : Bibliothèque de client JavaScript Recherche d’actualités Bing'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 858e6b9e0e40ab988a4cdf04b31580c1ca28d40a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503524"
---
Utilisez ce guide de démarrage rapide pour démarrer une recherche d’actualités avec la bibliothèque de client Recherche d’actualités Bing pour JavaScript. Si l’outil Recherche d’actualités Bing dispose d’une API REST compatible avec la plupart des langages de programmation, la bibliothèque de client offre quant à elle un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Prérequis

* [Node.JS](https://nodejs.org/en/)

Pour configurer une application console à l’aide de la bibliothèque de client Recherche d’actualités Bing
1. Exécutez `npm install ms-rest-azure` dans votre environnement de développement.
2. Exécutez `npm install azure-cognitiveservices-newssearch` dans votre environnement de développement.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

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
> [Créer une application web monopage](../../tutorial-bing-news-search-single-page-app.md)
