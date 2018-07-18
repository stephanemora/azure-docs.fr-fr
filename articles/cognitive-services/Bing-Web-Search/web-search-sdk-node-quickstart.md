---
title: Démarrage rapide du Kit de développement logiciel (SDK) Node pour Recherche Web | Microsoft Docs
description: Configuration de l’application console du Kit de développement logiciel (SDK) pour Recherche Web.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 44f7f97f6c442df3fbb1e5e08189b8db7d4b9db0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370453"
---
# <a name="web-search-sdk-node-quickstart"></a>Démarrage rapide du Kit de développement logiciel (SDK) Node pour Recherche Web

Le Kit de développement logiciel (SDK) Recherche Web Bing fournit les fonctionnalités de l’API REST pour les requêtes web et l’analyse des résultats.

Le [code source des exemples du Kit de développement logiciel (SDK) Node pour Recherche Web Bing](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) est disponible sur GitHub.

## <a name="application-dependencies"></a>Dépendances de l’application

Pour installer une application console à l’aide du Kit de développement logiciel (SDK) Recherche Web Bing, exécutez `npm install azure-cognitiveservices-websearch` dans votre environnement de développement.

## <a name="web-search-client"></a>Client Recherche Web
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous *Recherche*. Créez une instance de `CognitiveServicesCredentials` :
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ensuite, instanciez le client :
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
Recherchez des résultats :
```
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
Le code envoie les éléments `result.value` à la console sans analyser le texte.  Les résultats, le cas échéant par catégorie, incluent les éléments suivants :
- _type: 'ImageObject'
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObjectElementType'

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Étapes suivantes

[Exemples du Kit de développement logiciel (SDK) Node.js pour Cognitive Services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
