---
title: 'Démarrage rapide : Vérifier l’orthographe avec l’API REST et Node.js - Vérification orthographique Bing'
titleSuffix: Azure Cognitive Services
description: Commencez à utiliser l’API REST Vérification orthographique Bing pour vérifier l’orthographe et la grammaire avec ce guide de démarrage rapide.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 69c391e6c3f93a998ade7c5721a528d895f8df76
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75382850"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Démarrage rapide : Vérifier l’orthographe avec l’API REST Vérification orthographique Bing et Node.js

Utilisez ce guide de démarrage rapide pour effectuer votre premier appel à l’API REST Vérification orthographique Bing. Cette simple application Node envoie une demande à l’API et retourne la liste des mots qu’elle n’a pas reconnus, suivie de suggestions de corrections. Bien que cette application soit écrite en Node.js, l’API est un service web RESTful compatible avec la plupart des langages de programmation. Le code source de cette application est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Prérequis

* [Node.js 6](https://nodejs.org/en/download/) ou version ultérieure.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Créer et initialiser un projet

1. Créez un fichier JavaScript dans votre IDE ou éditeur favori. Définissez la sévérité et exigez `https`. Créez ensuite des variables pour vos hôte du point de terminaison d’API, chemin et clé d’abonnement. Vous pouvez utiliser le point de terminaison global ci-dessous, ou le point de terminaison de [sous-domaine personnalisé](../../../cognitive-services/cognitive-services-custom-subdomains.md) affiché dans le portail Azure pour votre ressource.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Créez des variables pour vos paramètres de recherche et le texte à vérifier. Ajoutez le code de votre marché après `mkt=`. Le code du marché correspond au pays depuis lequel vous effectuez la demande. De plus, ajoutez votre mode de vérification orthographique après `&mode=`. Le mode est soit `proof` (détecte la plupart des erreurs d’orthographe et de grammaire) ou `spell` (détecte la plupart des erreurs d’orthographe mais pas autant d’erreurs de grammaire).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Créer les paramètres de demande

Créez vos paramètres de demande en créant un objet avec une méthode `POST`. Ajoutez votre chemin en ajoutant le chemin du point de terminaison et la chaîne de requête. Ajoutez votre clé d’abonnement dans l’en-tête `Ocp-Apim-Subscription-Key`.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Créer un gestionnaire de réponse

Créez une fonction nommée `response_handler` pour prendre la réponse JSON de l’API, puis imprimez-la. Créez une variable pour le corps de la réponse. Ajoutez la réponse quand un indicateur `data` est reçu, à l’aide de `response.on()`. Quand un indicateur `end` est reçu, imprimez le corps JSON dans la console.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>Envoyer la demande

Appelez l’API en utilisant `https.request()` avec vos paramètres de demande et le gestionnaire de réponse. Écrivez votre texte pour l’API et mettez par la suite fin à la demande.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Exécution de l'application

Créez et exécutez votre projet.

Si vous utilisez la ligne de commande, utilisez les commandes suivantes pour générer et exécuter l’application.

```bash
node <FILE_NAME>.js
```


## <a name="example-json-response"></a>Exemple de réponse JSON

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant :

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorials/spellcheck.md)

- [Qu’est-ce que l’API Vérification orthographique Bing ?](../overview.md)
- [Informations de référence sur l’API Vérification orthographique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
