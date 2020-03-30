---
title: Utilisation du classement pour afficher les résultats de la recherche
titleSuffix: Azure Cognitive Services
description: Montre comment utiliser la réponse RankingResponse Bing pour afficher les résultats de la recherche dans un ordre de classement.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 1c8e0bb136fddeb84dc991e63a761378b38cc470
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382325"
---
# <a name="build-a-console-app-search-client-in-c"></a>Générer un client de recherche d’application console en C#

Ce tutoriel montre comment générer une application console .NET Core simple qui permet aux utilisateurs d’interroger l’API Recherche web Bing et d’afficher les résultats classés.

Ce tutoriel montre comment :

- Effectuer une requête simple auprès de l’API Recherche web Bing
- Afficher les résultats de la requête dans un ordre de classement

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre le tutoriel, vous avez besoin des éléments suivants :

- Visual Studio. Si vous ne l’avez pas, [téléchargez et installez Visual Studio 2017 Community Edition, qui est gratuit](https://www.visualstudio.com/downloads/).
- Une clé d’abonnement pour l’API Recherche web Bing. Si vous n’en avez pas, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Créer un projet d’application console

Dans Visual Studio, créez un projet avec `Ctrl`+`Shift`+`N`.

Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Visual C# > Bureau classique Windows > Application console (.NET Framework)** .

Nommez l’application **MyConsoleSearchApp**, puis cliquez sur **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Ajouter le package NuGet Json.NET au projet

Json.NET vous permet d’utiliser les réponses JSON retournées par l’API. Ajoutez son package NuGet à votre projet :

- Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet...** .
- Sous l’onglet **Parcourir**, recherchez `Newtonsoft.Json`. Sélectionnez la version la plus récente, puis cliquez sur **Installer**.
- Cliquez sur le bouton **OK** dans la fenêtre **Examiner les modifications**.
- Fermez l’onglet Visual Studio intitulé **NuGet : MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Ajouter une référence à System.Web

Ce tutoriel s’appuie sur l’assembly `System.Web`. Ajoutez une référence à cet assembly à votre projet :

- Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Références**, puis sélectionnez **Ajouter une référence...**
- Sélectionnez **Assemblys > Framework**, puis faites défiler vers le bas et cochez **System.Web**
- Sélectionnez **OK**.

## <a name="add-some-necessary-using-statements"></a>Ajouter quelques instructions using nécessaires

Le code de ce tutoriel requiert trois instructions using supplémentaires. Ajoutez ces instructions sous les instructions `using` existantes en haut de **Program.cs** :

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Demander une requête à l’utilisateur

Dans **l’Explorateur de solutions**, ouvrez **Program.cs**. Mettez à jour la méthode `Main()` :

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Cette méthode :

- Demande une requête à l’utilisateur
- Appelle `RunQueryAndDisplayResults(userQuery)` pour exécuter la requête et afficher les résultats
- Attend une entrée d’utilisateur pour empêcher la fermeture immédiate de la fenêtre de console.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Rechercher les résultats de la requête à l’aide de l’API Recherche web Bing

Ajoutez ensuite une méthode qui interroge l’API et affiche les résultats :

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Cette méthode :

- Crée un `HttpClient` pour interroger l’API Recherche web
- Définit l’en-tête HTTP `Ocp-Apim-Subscription-Key` que Bing utilise pour authentifier la requête
- Exécute la requête et utilise Json.NET pour désérialiser les résultats
- Appelle `DisplayAllRankedResults(responseObjects)` pour afficher tous les résultats dans un ordre de classement

Veillez à définir la valeur de `Ocp-Apim-Subscription-Key` sur votre clé d’abonnement.

## <a name="display-ranked-results"></a>Afficher les résultats classés

Avant de voir comment afficher les résultats dans un ordre de classement, examinons un exemple de réponse de recherche web :

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

L’objet JSON `rankingResponse` ([documentation](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) décrit l’ordre d’affichage approprié pour les résultats de la recherche. Il contient un ou plusieurs des groupes suivants, classés par ordre de priorité :

- `pole` : Résultats de la recherche qui doivent être le plus visibles (par exemple, affichés au-dessus de la partie principale et de l’encadré).
- `mainline` : Résultats de la recherche à afficher dans la partie principale.
- `sidebar` : Résultats de la recherche à afficher dans l’encadré. En l’absence d’encadré, affichez les résultats sous la partie principale.

La réponse de classement JSON peut inclure un ou plusieurs des groupes.

Dans **Program.cs**, ajoutez la méthode suivante pour afficher les résultats dans un ordre de classement correct :

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Cette méthode :

- Effectue une boucle sur les groupes `rankingResponse` contenus dans la réponse
- Affiche les éléments dans chaque groupe en appelant `DisplaySpecificResults(...)`

Dans **Program.cs**, ajoutez les deux méthodes suivantes :

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Ces méthodes fonctionnent ensemble pour générer les résultats de la recherche dans la console.

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l'application. Le résultat doit être semblable à ce qui suit :

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’utilisation du classement pour afficher les résultats](rank-results.md).
