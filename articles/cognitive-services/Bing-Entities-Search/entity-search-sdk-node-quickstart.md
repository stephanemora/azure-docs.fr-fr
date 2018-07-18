---
title: Démarrage rapide du SDK Recherche d’entités (Node) | Microsoft Docs
description: Programme d’installation de l’application console SDK Recherche d’entités.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 2904ecfed33334458f9b6a9ca2500cd0bfef13bc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370745"
---
# <a name="entity-search-sdk-node-quickstart"></a>Démarrage rapide du SDK Recherche d’entités (Node)

Le SDK Recherche d’entités Bing fournit les fonctionnalités de l’API REST nécessaires pour rechercher des entités et parcourir les résultats de la recherche. 

Le [code source des exemples du SDK Recherche d’entités Bing pour C#](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) est disponible sur GitHub.
## <a name="application-dependencies"></a>Dépendances de l’application

Pour installer une application console à l’aide du SDK Recherche d’entités Bing, exécutez `npm install azure-cognitiveservices-entitysearch` dans votre environnement de développement.

## <a name="entity-search-client"></a>Client Recherche d’entités
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous *Recherche*. Créez une instance de `CognitiveServicesCredentials` :
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Ensuite, instanciez le client et recherchez les résultats :
```
const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');

let entitySearchApiClient = new EntitySearchAPIClient(credentials);

entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
    console.log(result.queryContext);
    console.log(result.entities.value);
    console.log(result.entities.value[0].description);
}).catch((err) => {
    throw err;
});

```
Le code envoie les éléments `result.value` à la console sans analyser le texte.  Les résultats, le cas échéant par catégorie, incluent les éléments suivants :
- _type: 'Thing'
- _type: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Étapes suivantes

[Exemples du SDK Cognitive Services pour Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)