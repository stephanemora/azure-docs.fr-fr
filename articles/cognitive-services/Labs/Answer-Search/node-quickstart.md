---
title: Démarrage rapide Node pour Microsoft Cognitive Services, projet Recherche de réponses | Microsoft Docs
description: Prise en main du projet Recherche de réponses, Microsoft Cognitive Services sur Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 36b2709d39230aae7929164ba4c9306f57043b43
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369569"
---
# <a name="project-answer-search-node-quickstart"></a>Démarrage rapide Node du projet Recherche de réponses

L’exemple Node suivant crée une demande d’informations sur la baie du Mont Saint-Michel.

## <a name="prerequisites"></a>Prérequis

Obtenir une clé d’accès pour l’essai gratuit [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

Cet exemple utilise Node v8.9.4

## <a name="code-scenario"></a>Scénario de code 

Le code suivant obtient des réponses.
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
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

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
- [Exemple de code C#](c-sharp-quickstart.md)
- [Démarrage rapide de Java](java-quickstart.md)
- [Démarrage rapide Python](python-quickstart.md)