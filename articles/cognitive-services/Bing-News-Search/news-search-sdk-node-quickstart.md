---
title: 'Démarrage rapide : Kit de développement logiciel (SDK) Recherche d’actualités Bing, Node'
titleSuffix: Azure Cognitive Services
description: Configuration de l’application console du SDK Recherche d’actualités Bing
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 2279a6475ab8c39b3ff599f7244caea59d622651
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802999"
---
# <a name="quickstart-bing-news-search-sdk-with-node"></a>Démarrage rapide : Kit de développement logiciel (SDK) Java pour Recherche d’actualités Bing avec Node

Le SDK Recherche d’actualités fournit les fonctionnalités de l’API REST pour les recherche d’actualités et l’analyse des résultats. 

Le [code source des exemples du SDK Node Recherche d’actualités Bing](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) est disponible sur GitHub.

## <a name="application-dependencies"></a>Dépendances de l’application

Pour installer une application console à l’aide du SDK Recherche d’actualités Bing, exécutez `npm install azure-cognitiveservices-newssearch` dans votre environnement de développement.

## <a name="news-search-client"></a>Client de recherche d’actualités
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous *Recherche*. Créez une instance de `CognitiveServicesCredentials` :
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ensuite, instanciez le client :
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Utilisez le client pour rechercher un texte de requête, en l’occurrence «Winter Olympics » :
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
Le code envoie les éléments `result.value` à la console sans analyser le texte. Les résultats, le cas échéant par catégorie, incluent les éléments suivants :
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Étapes suivantes

[Exemples du SDK Node.js Cognitive Services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
