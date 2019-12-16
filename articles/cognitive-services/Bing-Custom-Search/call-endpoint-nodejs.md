---
title: 'Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec Node.js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utiliser ce guide de démarrage rapide pour commencer à demander des résultats de recherche à partir de votre instance Recherche personnalisée Bing, avec Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 97bf62c61aee63ce97496e54b99ad641d6150876
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972697"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec Node.js

Utilisez ce guide de démarrage rapide pour commencer à demander des résultats de recherche à partir de votre instance Recherche personnalisée Bing. Si d’un côté cette application est écrite en JavaScript, de l’autre, l’API Recherche personnalisée Bing constitue un service web RESTful compatible avec la plupart des langages de programmation. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Prérequis

- Une instance Recherche personnalisée Bing. Consultez [Démarrage rapide : Créer votre première instance Recherche personnalisée Bing](quick-start.md) pour de plus amples informations.

- [Node.JS](https://www.nodejs.org/)

- La [bibliothèque de requêtes JavaScript](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier JavaScript dans votre éditeur ou IDE favori, puis ajoutez une instruction `require()` pour la bibliothèque de requêtes. Créez des variables pour votre clé d’abonnement, votre ID de configuration personnalisée et un terme de recherche. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Envoyer et recevoir une requête de recherche 

1. Créez une variable pour stocker les informations qui sont envoyées dans votre requête. Construisez l’URL de requête en ajoutant votre terme de recherche au paramètre de requête `q=`, et l’ID de configuration personnalisée de votre instance de recherche à `customconfig=`. Séparez les paramètres par un caractère `&`. Vous pouvez utiliser le point de terminaison global ci-dessous, ou le point de terminaison de [sous-domaine personnalisé](../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Utilisez la bibliothèque de requêtes JavaScript pour envoyer une requête de recherche à votre instance Recherche personnalisée Bing et afficher des informations sur les résultats, notamment son nom, l’URL et la date à laquelle la page web a été analysée pour la dernière fois.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web Recherche personnalisée](./tutorials/custom-search-web-page.md)
