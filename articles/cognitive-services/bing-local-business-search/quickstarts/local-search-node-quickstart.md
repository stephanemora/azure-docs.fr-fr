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
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: beab594126ce292ea1fc47e399a12274dbb31aa3
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665693"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Démarrage rapide : Envoyer une requête à l’API Recherche d’entreprises locales Bing avec Node.js

Utilisez ce démarrage rapide pour commencer à envoyer des demandes à l’API Recherche d’entreprises locales Bing, un service Azure Cognitive Services. Alors que cette application simple est écrite dans Node.js, l’API est un service web RESTful compatible avec tous les langages de programmation capables de formuler des requêtes HTTP et d’analyser du JSON.

Cet exemple d’application récupère des données de réponse locales auprès de l’API pour la requête de recherche `hotel in Bellevue`.

## <a name="prerequisites"></a>Prérequis

* La dernière version de [Node.js](https://nodejs.org/en/download/).

* La [bibliothèque de requêtes JavaScript](https://github.com/request/request).

Vous devrez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec les API Bing. [L’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) est suffisant pour suivre ce guide de démarrage rapide. Utilisez la clé d’accès fournie par l’essai gratuit.  Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Scénario de code

Le code suivant récupère, définit et envoie la demande. Il est implémenté lors des étapes suivantes :

1. Déclarez les variables pour spécifier le point de terminaison par hôte et par chemin d’accès.
2. Spécifiez la requête et ajoutez le paramètre de requête.
3. Créez une fonction de gestionnaire pour la réponse.
4. Définissez la fonction de recherche qui crée la requête et ajoute l’en-tête Ocp-Apim-Subscription-Key.
5. Exécutez la fonction de recherche.

Voici le code complet pour cette démonstration :

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

* [Démarrage rapide de la Recherche d’entreprises locales](local-quickstart.md)
* [Démarrage rapide Java de la Recherche d’entreprises locales](local-search-java-quickstart.md)
* [Démarrage rapide Python de la Recherche d’entreprises locales](local-search-python-quickstart.md)
