---
title: 'Démarrage rapide : Utiliser Node.js pour appeler l’API REST Analyse de texte'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment obtenir des informations et des exemples de code pour vous aider à commencer rapidement à utiliser l’API Analyse de texte dans Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.custom: seo-javascript-september2019
ms.openlocfilehash: c111937dbbea5e588e82bc9753a71d1d597ca767
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75378787"
---
# <a name="quickstart-use-nodejs-to-call-the-text-analytics-cognitive-service"></a>Démarrage rapide : Utiliser Node.js pour appeler le service cognitif Analyse de texte  
<a name="HOLTop"></a>

Cet article montre comment [détecter la langue](#Detect), [analyser les sentiments](#SentimentAnalysis), [extraire les expressions clés](#KeyPhraseExtraction) et [identifier les entités liées](#Entities) à l’aide des [API Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759711) avec Node.JS.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Détecter la langue

L’API Détection de langue détecte la langue d’un document texte à l’aide de la [méthode Detect Language](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Créez un projet Node.JS avec votre IDE favori, ou créez un dossier sur votre poste de travail.
1. Ajoutez le code fourni ci-dessous à un nouveau fichier `.js`.
1. Copiez votre clé et votre point de terminaison dans le code. 
1. Exécutez le programme à partir de votre IDE ou de la ligne de commande, par exemple `npm start` ou `node detect.js`.

```javascript
'use strict';

let https = require ('https');
subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v2.1/languages';

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

let get_language = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'text': 'This is a document written in English.' },
        { 'id': '2', 'text': 'Este es un document escrito en Español.' },
        { 'id': '3', 'text': '这是一个用中文写的文件' }
    ]
};

get_language(documents);
```

**Réponse de détection de langue**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analyser les sentiments

L’API Analyse des sentiments détecte les sentiments d’un ensemble d’enregistrements de texte à l’aide de la [méthode Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). L’analyse des sentiments peut être utilisée pour découvrir ce que les clients pensent de votre marque ou de votre thématique en analysant du texte brut pour trouver des indices sur les sentiments positifs ou négatifs. L’exemple suivant fournit des scores pour deux documents, un en anglais, l’autre en espagnol.

1. Créez un projet Node.JS avec votre IDE favori, ou créez un dossier sur votre poste de travail.
1. Ajoutez le code fourni ci-dessous à un nouveau fichier `.js`.
1. Copiez votre clé et votre point de terminaison Analyse de texte dans le code. 
1. Exécutez le programme à partir de votre IDE ou de la ligne de commande, par exemple `npm start` ou `node sentiment.js`.

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v2.1/sentiment';

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

let get_sentiments = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
        { 'id': '2', 'language': 'es', 'text': 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' },
    ]
};

get_sentiments(documents);
```

**Réponse d’analyse des sentiments**

Le résultat est mesuré comme positif si son score est plus proche de 1,0 et négatif s’il est plus proche de 0,0.
Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant :

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extraire des expressions clés

L’API Extraction de phrases clés extrait des phrases clés d’un document texte à l’aide de la [méthode Phrases clés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). L’extraction d’expressions clés est utilisée pour identifier rapidement les points principaux d’un document ou d’un texte. L’exemple suivant extrait les expressions clés du document en anglais et du document en espagnol.

1. Créez un projet Node.JS avec votre IDE favori, ou créez un dossier sur votre poste de travail.
1. Ajoutez le code fourni ci-dessous à un nouveau fichier `.js`.
1. Copiez votre clé et votre point de terminaison Analyse de texte dans le code. 
1. Exécutez le programme à partir de votre IDE ou de la ligne de commande, par exemple `npm start` ou `node key-phrases.js`.

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v2.1/keyPhrases';

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

let get_key_phrases = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
        { 'id': '2', 'language': 'es', 'text': 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
        { 'id': '3', 'language': 'en', 'text': 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' }
    ]
};

get_key_phrases(documents);
```

**Réponse d’extraction de phrases clés**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-linked-entities"></a>Identifier les entités liées

L’API Entités identifie les entités bien connues dans un document texte à l’aide de la [méthode Entités](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634). Les [entités](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) extraient les mots du texte, comme « United States », puis vous donnent le type et/ou un lien Wikipédia pour ce ou ces mots. Le type pour « United States » est `location`, alors que le lien Wikipédia est `https://en.wikipedia.org/wiki/United_States`.  L’exemple suivant identifie les entités dans les documents en anglais.

1. Créez un projet Node.JS avec votre IDE favori, ou créez un dossier sur votre poste de travail.
1. Ajoutez le code fourni ci-dessous à un nouveau fichier `.js`.
1. Copiez votre clé et votre point de terminaison Analyse de texte dans le code.
1. Exécutez le programme à partir de votre IDE ou de la ligne de commande, par exemple `npm start` ou `node entities.js`.

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v2.1/entities';

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

let get_entities = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'Microsoft is an It company.' }
    ]
};

get_entities(documents);
```

**Réponse de l’extraction d’entité**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant :

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)
