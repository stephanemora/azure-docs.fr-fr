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
ms.openlocfilehash: 73c31c7175bd4dfcb182fb76784937c176ac7702
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977874"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Appeler le point de terminaison Recherche personnalisée Bing (Node.js)

Ce guide de démarrage rapide montre comment demander les résultats de la recherche à partir de votre instance de recherche personnalisée en utilisant Node.js pour appeler le point de terminaison Recherche personnalisée Bing. 

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

- Une instance de recherche personnalisée prête à l’emploi. Consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).
- [Node.js](https://www.nodejs.org/) (déjà installé).
- Une clé d’abonnement Vous pouvez obtenir une clé d’abonnement quand vous activez votre [essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), ou vous pouvez utiliser une clé d’abonnement payant de votre tableau de bord Azure (voir [Compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    

## <a name="run-the-code"></a>Exécuter le code

Pour exécuter cet exemple, suivez ces étapes :

1. Créez un dossier pour votre code.  
  
2. À partir d’un terminal ou d’une invite de commandes, accédez au dossier que vous venez de créer.  
  
3. Installez le module du nœud **request** :
    <pre>
    npm install request
    </pre>  
    
4. Créez un fichier nommé BingCustomSearch.js dans le dossier que vous avez créé et copiez-y le code suivant. Remplacez **YOUR-SUBSCRIPTION-KEY** et **YOUR-CUSTOM-CONFIG-ID** par votre clé d’abonnement et votre ID de configuration.  
  
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
  
6. Exécutez le code à l’aide de la commande suivante :  
  
    ```    
    node BingCustomSearch.js
    ``` 

## <a name="next-steps"></a>Étapes suivantes
- [Configurer votre expérience d’interface utilisateur hébergée](./hosted-ui.md)
- [Utiliser des marqueurs d’ornement pour mettre en surbrillance du texte](./hit-highlighting.md)
- [Paginer des pages web](./page-webpages.md)
