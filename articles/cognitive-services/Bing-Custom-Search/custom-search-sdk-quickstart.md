---
title: Démarrage rapide du SDK Recherche personnalisée C# | Microsoft Docs
titleSuffix: Cognitive Services
description: Configuration de l’application console du SDK C# de recherche personnalisée.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 59b208b53bec974433c50c0e2304dc96bd9bd09e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367869"
---
# <a name="custom-search-sdk-c-quickstart"></a>Démarrage rapide du SDK Recherche personnalisée C#

Le SDK Recherche personnalisée Bing fournit les fonctionnalités de l’API REST nécessaires pour rechercher des entités et analyser les résultats.

## <a name="application-dependencies"></a>Dépendances de l’application

Pour configurer une application console à l’aide du SDK Recherche personnalisée Bing, accédez à l’option `Manage NuGet Packages` à partir de l’Explorateur de solutions dans Visual Studio. Ajoutez le package `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

L’installation du package [Recherche personnalisée NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) installe également les dépendances, y compris les assemblys suivants :
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Client Recherche d’entités

Pour créer une instance du client CustomSearchAPI, ajoutez-la à l’aide de directives :
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Instanciez le client de recherche personnalisée : remplacez `YOUR-CUSTOM-SEARCH-KEY` et `YOUR-CUSTOM-CONFIG-ID` par votre clé d’accès et l’ID de configuration du point de terminaison de l’API affecté à [Mes instances](https://www.customsearch.ai/).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Utilisez le client pour effectuer une recherche avec un texte de requête :
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Analyser les résultats

La méthode `SearchAsync` renvoie un objet `WebData` qui contient `WebPages` si des pages sont trouvées pour la requête. Ce code trouve le premier résultat et obtient son nom (`Name`) et son URL (`URL`).
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

 //WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
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

```
## <a name="complete-console-application"></a>Application console complète

Le code suivant effectue une recherche sur la requête « Xbox » et imprime les éléments `Name` et `URL` pour le premier résultat Web.
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

            try
            {
                // This will look up a single query (Xbox).
                var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                Console.WriteLine("Searched for Query# \" Xbox \"");

                //WebPages
                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
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
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>Étapes suivantes

[Exemples du SDK .NET pour Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
