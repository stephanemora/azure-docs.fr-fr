---
title: Appeler un point de terminaison à l’aide de Node.js - Recherche personnalisée Bing - Microsoft Cognitive Services
description: Ce guide de démarrage rapide montre comment demander les résultats de la recherche à partir de votre instance de recherche personnalisée en utilisant Node.js pour appeler le point de terminaison Recherche personnalisée Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 48fc234e15ce3b9172d766f6fae11b51a017ce70
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370741"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Appeler le point de terminaison Recherche personnalisée Bing (Node.js)

Ce guide de démarrage rapide montre comment demander les résultats de la recherche à partir de votre instance de recherche personnalisée en utilisant Node.js pour appeler le point de terminaison Recherche personnalisée Bing. 

## <a name="prerequisites"></a>Prérequis
Pour suivre ce guide de démarrage rapide, les éléments suivants sont requis :

- Une instance de recherche personnalisée. Consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).

- [Node.js](https://www.nodejs.org/) (déjà installé).

-  Un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec les **API Recherche Bing**. Vous pouvez utiliser un [essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) pour suivre ce guide de démarrage rapide. Vous avez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou d’une clé d’un abonnement payant de votre tableau de bord Azure.

## <a name="run-the-code"></a>Exécuter le code

Pour appeler le point de terminaison Recherche personnalisée Bing, effectuez les étapes suivantes :

1. Créez un dossier pour votre code.
2. À partir d’un terminal ou d’une invite de commandes, accédez au dossier que vous venez de créer.
3. Installez le module du nœud **request** :
    <pre>
    npm install request
    </pre>
4. Créez le fichier BingCustomSearch.js et copiez-y le code ci-dessous.
5. Remplacez **YOUR-SUBSCRIPTION-KEY** et **YOUR-CUSTOM-CONFIG-ID** par votre clé et votre ID de configuration (voir l’étape 1).

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
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
    })
    ```
6. Exécutez le code à l’aide de la commande suivante.
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>Étapes suivantes
- [Configurer votre expérience d’interface utilisateur hébergée](./hosted-ui.md)
- [Utiliser des marqueurs d’ornement pour mettre en surbrillance du texte](./hit-highlighting.md)
- [Paginer des pages web](./page-webpages.md)