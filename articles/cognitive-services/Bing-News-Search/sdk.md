---
title: Kit de développement logiciel (SDK) pour Recherche Bing | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Kit de développement logiciel (SDK) pour Recherche Bing, pour les applications qui recherchent sur le web.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: 4a40ea665e153536d2322706b455598902ce41eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370433"
---
# <a name="bing-search-sdk"></a>Kit de développement logiciel (SDK) pour Recherche Bing
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