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
ms.openlocfilehash: 985ddcff35a16c747fff34ed487c72744e1ee466
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313445"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>Démarrage rapide : Kit SDK Recherche de vidéos Bing avec Node

Le SDK Recherche de vidéos Bing fournit les fonctionnalités de l’API REST nécessaires pour rechercher des vidéos et analyser les résultats de la recherche. 

Le [code source des exemples du SDK Recherche de vidéos Bing pour Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) est disponible sur GitHub.

## <a name="application-dependencies"></a>Dépendances de l’application
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous **Recherche**.  Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Pour configurer une application console à l’aide du SDK Recherche de vidéos Bing :
* Exécutez `npm install ms-rest-azure` dans votre environnement de développement.
* Exécutez `npm install azure-cognitiveservices-videosearch` dans votre environnement de développement.

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
