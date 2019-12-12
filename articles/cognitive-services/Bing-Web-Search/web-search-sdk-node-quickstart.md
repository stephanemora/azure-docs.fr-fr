---
title: 'Démarrage rapide : Utiliser le kit SDK Recherche Web Bing pour Node.js'
titleSuffix: Azure Cognitive Services
description: Le kit de développement logiciel de l’API Recherche Web Bing simplifie l’intégration de l’API dans votre application Node.js. Dans ce démarrage rapide, vous allez apprendre à instancier un client, envoyer une requête et imprimer la réponse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 059862255ca2cf0ce435496bf22d866a37c8be71
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977163"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Démarrage rapide : Utiliser le kit SDK Recherche Web Bing pour Node.js

Le kit de développement logiciel de l’API Recherche Web Bing simplifie l’intégration de l’API dans votre application Node.js. Dans ce démarrage rapide, vous allez apprendre à instancier un client, envoyer une requête et imprimer la réponse.

Vous voulez voir le code tout de suite ? Des [exemples de kit de développement logiciel de l’API Recherche Web Bing pour Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) sont disponibles sur GitHub.

## <a name="prerequisites"></a>Prérequis
Voici quelques points dont vous aurez besoin avant d’exécuter ce démarrage rapide :

* [Node.js 6](https://nodejs.org/en/download/) ou version ultérieure
* Une clé d’abonnement  

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

Commençons par la configuration de l’environnement de développement pour notre projet Node.js.

1. Créez un nouveau répertoire pour votre projet :

    ```console
    mkdir YOUR_PROJECT
    ```

1. Créez un nouveau fichier de package :

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. Maintenant, nous allons installer certains modules Azure et les ajouter à `package.json` :

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Créer un projet et déclarer les modules requis

Dans le même répertoire que votre `package.json`, créez un projet Node.js à l’aide de votre IDE ou éditeur favori. Par exemple : `sample.js`.

Ensuite, copiez ce code dans votre projet. Il charge les modules installés dans la section précédente.

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Instancier le client

Ce code instancie un client et l’utilisation du module `azure-cognitiveservices-websearch`. Assurez-vous que vous entrez une clé d’abonnement valide pour votre compte Azure avant de continuer.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Effectuer une requête et imprimer les résultats

Utilisez le client pour envoyer une requête de recherche pour l’API Recherche Web Bing. Si la réponse comprend des résultats pour les éléments dans le tableau `properties`, le `result.value` est imprimé à la console.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Exécuter le programme

L’étape finale consiste à exécuter votre programme !

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous avez terminé ce projet, veillez à supprimer votre clé d’abonnement dans le code du programme.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exemples du SDK Node.js Cognitive Services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Voir aussi

* [Référence du SDK Node Azure](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/)
