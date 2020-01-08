---
title: 'Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec le kit SDK C#'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour commencer à demander des résultats de recherche depuis votre instance Recherche personnalisée Bing, avec le kit SDK C#.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: scottwhi
ms.openlocfilehash: 5e932b1ff597fc0170a6bb83841bc6ca7306693a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448711"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing avec le kit SDK C# 

Utilisez ce guide de démarrage rapide pour commencer à demander des résultats de recherche depuis votre instance Recherche personnalisée Bing, avec le kit SDK C#. Si d’un côté la Recherche personnalisée Bing dispose d’une API REST compatible avec la plupart des langages de programmation, de l’autre, le kit SDK Recherche personnalisée Bing offre un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

## <a name="prerequisites"></a>Conditions préalables requises

- Une instance Recherche personnalisée Bing. Consultez [Démarrage rapide : Créer votre première instance Recherche personnalisée Bing](quick-start.md) pour plus amples informations.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- N’importe quelle édition de [Visual Studio 2017 ou ultérieure](https://www.visualstudio.com/downloads/)
- Si vous utilisez Linux/MacOS, cette application peut être exécutée à l’aide de [Mono](https://www.mono-project.com/).
- Le package NuGet de [Recherche personnalisée Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - Dans l’**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur votre projet et sélectionnez **Gérer les packages NuGet** dans le menu. Installez le package `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. L’installation du package NuGet Custom Search installe aussi les assemblys suivants :
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez une application console C# dans Visual Studio. Ajoutez ensuite les packages suivants à votre projet.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. Dans la méthode principale de votre application, instanciez le client de recherche avec votre clé API.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Envoyer la requête de recherche et recevoir une réponse
    
1. Envoyez une requête de recherche à l’aide de la méthode `SearchAsync()` de votre client et enregistrez la réponse. Veillez à remplacer votre `YOUR-CUSTOM-CONFIG-ID` par l’ID de configuration de votre instance (vous trouvez l’ID dans le [portail Recherche personnalisée Bing](https://www.customsearch.ai/)). Cet exemple recherche « Xbox ».

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. La méthode `SearchAsync()` retourne un objet `WebData`. Utilisez l’objet pour itérer au sein des `WebPages` qui ont été trouvés. Ce code recherche le premier résultat de page web et affiche le `Name` et l’`URL` de la page web.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
