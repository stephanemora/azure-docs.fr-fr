---
title: 'Démarrage rapide : URL Preview de projet, Node.js'
titlesuffix: Azure Cognitive Services
description: Prise en main avec l’aperçu d’URL dans Microsoft Cognitive Services.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: b8410127ed9a0444ab400eb3242868a85bd5b2c9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883565"
---
# <a name="quickstart-url-preview-with-nodejs"></a>Démarrage rapide : URL Preview avec Node.js 

L’exemple Node suivant crée un aperçu d’Url pour le site web de SwiftKey : https://swiftkey.com/en.

## <a name="prerequisites"></a>Prérequis

Obtenir une clé d’accès pour l’essai gratuit [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scénario de code 

Le code suivant obtient des donnés d’aperçu d’URL.
Il est implémenté lors des étapes suivantes :
1. Déclarez les variables pour spécifier le point de terminaison par hôte et par chemin d’accès.
2. Spécifiez l’URL de requête pour afficher un aperçu et ajoutez le paramètre de requête.  
3. Créez une fonction de gestionnaire pour la réponse.
4. Définissez la fonction de recherche qui crée la requête et ajoute l’en-tête *Ocp-Apim-Subscription-Key*.
5. Exécutez la fonction de recherche. 

Voici le code complet pour cette démonstration :

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

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

````

## <a name="next-steps"></a>Étapes suivantes
- [Exemple de code C#](csharp.md)
- [Démarrage rapide de Java](java-quickstart.md)
- [Démarrage rapide JavaScript](javascript.md)
- [Démarrage rapide Python](python-quickstart.md)