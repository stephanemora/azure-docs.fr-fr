---
title: Kit de développement logiciel (SDK) Recherche d’actualités Bing
titleSuffix: Azure Cognitive Services
description: Kit de développement logiciel (SDK) Recherche d’actualités Bing à destination des applications de recherche sur le web.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: b1d9eaa35416adfa11647f2116171256f82fe095
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801231"
---
# <a name="bing-search-sdk"></a>SDK Recherche Bing
Les exemples d’API Recherche d’actualités Bing incluent les scénarios suivants :
1. Interroger les actualités en recherchant des termes avec les paramètres `market` et `count`, vérifier le nombre de résultats, puis afficher `totalEstimatedMatches`, le nom, l’URL, la description, l’heure de publication et le nom du fournisseur du premier résultat d’actualités.
2. Interroger les actualités les plus récentes en recherchant des termes avec les paramètres `freshness` et `sortBy`, vérifier le nombre de résultats, puis afficher `totalEstimatedMatches`, l’URL, la description, l’heure de publication et le nom du fournisseur du premier résultat d’actualités.
3. Interroger les actualités de catégorie pour `movie` et `TV entertainment` avec une recherche sécurisée, vérifier le nombre de résultats, puis afficher la catégorie, le nom, l’URL, la description, l’heure de publication et le nom du fournisseur du premier résultat d’actualités.
4. Interroger les rubriques tendance dans Bing, vérifier le nombre de résultats, puis afficher le nom, le texte de requête, `webSearchUrl`, `newsSearchUrl` et l’URL d’image du premier résultat d’actualités.

Les Kits de développement logiciel (SDK) pour Recherche Bing rendent les fonctionnalités de recherche sur le web directement accessibles dans les langages de programmation suivants :
* Bien commencer avec les [exemples .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [Package NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Voir aussi les [bibliothèques .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) pour les définitions et les dépendances.
* Bien commencer avec les [exemples Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Voir aussi les [bibliothèques Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) pour les définitions et les dépendances.
* Bien commencer avec les [exemples Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Voir aussi [bibliothèques Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) pour les définitions et les dépendances.
* Bien commencer avec les [exemples Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Voir aussi les [bibliothèques Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) pour les définitions et les dépendances.

Les exemples du SDK pour chaque langage incluent un fichier Lisez-moi avec des détails sur les prérequis et l’installation/exécution des exemples.