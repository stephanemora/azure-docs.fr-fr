---
title: 'Démarrage rapide : Envoyer une requête à l’API avec Node.js - Recherche d’entreprises locales Bing'
titleSuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour commencer à envoyer des demandes à l’API Recherche d’entreprises locales Bing, un service Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: d0ee55c0baa6d7ec2ff86aa0e37b10ca4c973b77
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408133"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Démarrage rapide : Envoyer une requête à l’API Recherche d’entreprises locales Bing avec Node.js

Utilisez ce guide de démarrage rapide pour découvrir comment envoyer des demandes à l’API Recherche d’entreprises locales Bing, un service Azure Cognitive Services. Bien que cette application simple soit écrite en Node.js, l’API est un service web RESTful compatible avec tous les langages de programmation capables de formuler des requêtes HTTP et d’analyser du JSON.

Cet exemple d’application récupère des données de réponse locales auprès de l’API pour une requête de recherche.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* La dernière version de [Node.js](https://nodejs.org/en/download/).
* La [bibliothèque de requêtes JavaScript](https://github.com/request/request).
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Créer une ressource Recherche Bing"  target="_blank">créez une ressource Recherche Bing<span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.


## <a name="code-scenario"></a>Scénario de code

Le code suivant définit et envoie la requête, qui est implémentée dans les étapes suivantes :

1. Déclarez les variables pour spécifier le point de terminaison par hôte et par chemin d’accès.
2. Spécifiez la requête et ajoutez le paramètre de requête.
3. Créez une fonction de gestionnaire pour la réponse.
4. Définissez la fonction de recherche qui crée la requête et ajoute l’en-tête `Ocp-Apim-Subscription-Key`.
5. Exécutez la fonction de recherche.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide C# de la Recherche d’entreprises locales](local-quickstart.md)
* [Démarrage rapide Java de la Recherche d’entreprises locales](local-search-java-quickstart.md)
* [Démarrage rapide Python de la Recherche d’entreprises locales](local-search-python-quickstart.md)
