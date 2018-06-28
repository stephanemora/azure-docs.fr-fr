---
title: Démarrage rapide du SDK Recherche de vidéos (Node) | Microsoft Docs
description: Programme d’installation de l’application console SDK Recherche de vidéos.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 5718c750288e0a5605db3296d2911cca5e03375c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370753"
---
# <a name="video-search-sdk-node-quickstart"></a>Démarrage rapide du SDK Recherche de vidéos (Node)

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

[Exemples du SDK Cognitive Services pour Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
