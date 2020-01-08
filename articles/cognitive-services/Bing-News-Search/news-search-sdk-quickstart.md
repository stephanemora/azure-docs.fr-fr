---
title: 'Démarrage rapide : Effectuer une recherche d’actualités avec le SDK pour C# - Recherche d’actualités Bing'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour rechercher des actualités en utilisant le SDK Recherche d’actualités Bing pour C# et pour traiter la réponse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: fe1dbd132f1cbacac59b0a9055b698865c2f7090
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75383173"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-c"></a>Démarrage rapide : Effectuer une recherche d'actualités avec le Kit de développement logiciel (SDK) Recherche d'actualités Bing pour C#

Utilisez ce guide de démarrage rapide pour démarrer une recherche d’actualités avec le SDK Recherche d’actualités Bing pour C#. Si l’outil Recherche d’actualités Bing a une API REST compatible avec la plupart des langages de programmation, le SDK offre quant à lui un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch).

## <a name="prerequisites"></a>Conditions préalables requises

* N’importe quelle édition de [Visual Studio 2017 ou ultérieure](https://www.visualstudio.com/downloads/).
* Le framework [Json.NET](https://www.newtonsoft.com/json), disponible sous forme de package NuGet.
* Si vous utilisez Linux/MacOS, cette application peut être exécutée à l’aide de [Mono](https://www.mono-project.com/).

* Le [package NuGet du SDK Recherche d’actualités Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0). L’installation de ce package installe également les éléments suivants :
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Pour configurer une application console à l’aide du Kit de développement logiciel (SDK) pour Recherche d’actualités Bing, accédez à l’option `Manage NuGet Packages` à partir de l’Explorateur de solutions dans Visual Studio.  Ajoutez le package `Microsoft.Azure.CognitiveServices.Search.NewsSearch`.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Créer et initialiser un projet

1. Créez une solution console en C# dans Visual Studio. Ajoutez ensuite les éléments suivants dans le fichier de code principal.
    
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    ```

2. Créez une variable pour votre clé d’API et un terme de recherche, puis instanciez le client de recherche d’actualités.

    ```csharp
    var key = "YOUR-ACCESS-KEY";
    var searchTerm = "Quantum Computing";
    var client = new NewsSearchClient(new ApiKeyServiceClientCredentials(key));
    ```

## <a name="send-a-request-and-parse-the-result"></a>Envoyer une requête, puis analyser le résultat

1. Utilisez le client pour envoyer une requête de recherche au service Recherche d’actualités Bing :
    ```csharp
    var newsResults = client.News.SearchAsync(query: searchTerm, market: "en-us", count: 10).Result;
    ```

2. Si des résultats sont retournés, analysez-les :

    ```csharp
    if (newsResults.Value.Count > 0)
    {
        var firstNewsResult = newsResults.Value[0];
    
        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
        Console.WriteLine($"News result count: {newsResults.Value.Count}");
        Console.WriteLine($"First news name: {firstNewsResult.Name}");
        Console.WriteLine($"First news url: {firstNewsResult.Url}");
        Console.WriteLine($"First news description: {firstNewsResult.Description}");
        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
        Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
    }
    
    else
    {
        Console.WriteLine("Couldn't find news results!");
    }
    Console.WriteLine("Enter any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](tutorial-bing-news-search-single-page-app.md)
