---
title: 'Démarrage rapide : Kit SDK Recherche de vidéos Bing, Node'
titleSuffix: Azure Cognitive Services
description: Configuration de l’application console avec le kit SDK Recherche de vidéos Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 4dcc9220d4d38bfe34514edd6a3ad47c7a7d4ba8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225614"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>Démarrage rapide : Kit SDK Recherche de vidéos Bing avec Node

Le SDK Recherche de vidéos Bing fournit les fonctionnalités de l’API REST nécessaires pour rechercher des vidéos et analyser les résultats de la recherche. 

Le [code source des exemples du SDK Recherche de vidéos Bing pour Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) est disponible sur GitHub.

## <a name="application-dependencies"></a>Dépendances de l’application

Pour installer une application console à l’aide du SDK Recherche de vidéos Bing, exécutez `npm install azure-cognitiveservices-videosearch` dans votre environnement de développement.

## <a name="video-search-client"></a>Client Recherche de vidéos
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous *Recherche*. Créez une instance de `CognitiveServicesCredentials` :
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ensuite, instanciez le client :
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Recherchez les résultats.
```
client.videosOperations.search('Interstellar Trailer').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Étapes suivantes

[Exemples du SDK Node.js Cognitive Services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
