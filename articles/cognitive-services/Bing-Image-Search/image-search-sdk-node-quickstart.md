---
title: 'Démarrage rapide : Demander et filtrer des images à l’aide du Kit de développement logiciel (SDK) dans Node.js'
description: Dans ce démarrage rapide, vous allez demander et filtrer les images retournées par Recherche d’images Bing, à l’aide de Node.js.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e88c045b220192a617e6b8caf5d8d53f70a25b5e
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2018
ms.locfileid: "41929815"
---
# <a name="quickstart-request-and-filter-images-using-the-sdk-and-nodejs"></a>Démarrage rapide : Demander et filtrer des images à l’aide du Kit de développement logiciel (SDK) et de Node.js

Le Kit de développement logiciel (SDK) pour Recherche d’images Bing fournit les fonctionnalités de l’API REST nécessaires pour rechercher des images et analyser les résultats. 

Le [code source des exemples du Kit de développement logiciel (SDK) Node pour Recherche d’images Bing](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) est disponible sur GitHub.

## <a name="application-dependencies"></a>Dépendances de l’application

Pour installer une application console à l’aide du Kit de développement logiciel (SDK) pour Recherche d’images Bing, exécutez `npm install azure-cognitiveservices-imagesearch` dans votre environnement de développement.

## <a name="image-search-client"></a>Client Recherche d’images
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous *Recherche*. Créez une instance de `CognitiveServicesCredentials` :
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ensuite, instanciez le client :
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Utilisez le client pour rechercher un texte de requête, en l’occurrence « El Capitan » :
```
client.imagesOperations.search('El Capitan', function (err, result, request, response) {
    if (err) throw err;
    console.log(result.value);
});

```
<!-- Need to sanitize result
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'ImageObjectElementType'

![Imageresults](media/node-sdk-quickstart-image-results.png)
-->

## <a name="next-steps"></a>Étapes suivantes

[Exemples du SDK Node.js Cognitive Services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)