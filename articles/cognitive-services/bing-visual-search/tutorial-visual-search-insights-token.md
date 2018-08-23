---
title: Tutoriel concernant le jeton d’informations de l’image (ImageInsightsToken) du Kit de développement logiciel (SDK) de Recherche visuelle Bing | Microsoft Docs
description: Comment utiliser le Kit de développement logiciel (SDK) de Recherche visuelle Bing pour obtenir les URL des images spécifiées par ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 8f6e7f7e88ae78fe7e8a9be4adefd689dd26d0f9
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2018
ms.locfileid: "41929765"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Tutoriel : résultats et jeton ImageInsightsToken du Kit de développement logiciel (SDK) de Recherche visuelle Bing
Le Kit de développement logiciel (SDK) de Recherche visuelle comprend une option pour rechercher des images en ligne à partir d’une recherche précédente renvoyant un `ImageInsightsToken`.  Cet exemple obtient un `ImageInsightsToken` et utilise le jeton dans une recherche ultérieure.  Le code envoie le `ImageInsightsToken` à Bing, puis retourne des résultats qui incluent les URL de recherche Bing et les URL des images similaires disponibles en ligne.

## <a name="prerequisites"></a>Prérequis
Visual Studio 2017. Si nécessaire, vous pouvez télécharger la version communautaire gratuite à partir d’ici : https://www.visualstudio.com/vs/community/.
Une clé d’API Cognitive Services est requise pour authentifier les appels du Kit de développement logiciel (SDK). Inscrivez-vous pour obtenir une clé d’essai gratuit. La clé d’essai convient pour sept jours avec un appel par seconde. Pour les scénarios de production, achetez une clé d’accès. Consultez aussi les informations de tarification.
La possibilité d’exécuter des kits SDK .NET Core ainsi que des applications .NET Core 1.1. Vous pouvez obtenir CORE, Framework et Runtime à partir d’ici : https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Dépendances de l’application
Pour configurer une application console à l’aide du kit Web Search SDK Bing, accédez à l’option Gérer les packages NuGet à partir de l’Explorateur de solutions dans Visual Studio. Ajoutez :
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage packages.

L’installation du package du Kit de développement logiciel (SDK) Recherche Web NuGet installe également les dépendances, notamment :

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Obtention du jeton ImageInsightsToken à partir de la Recherche d’images
Cet exemple utilise un `ImageInsightsToken` obtenu par la méthode suivante.  Pour plus d’informations sur cet appel, consultez [Démarrage rapide C# du SDK Recherche d’images](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Le code recherche des résultats sur une requête pour « Canadian Rockies » et obtient un jeton ImageInsightsToken. Il imprime le premier jeton insights de l’image, l’URL de la miniature et celle du contenu d’image.  La méthode renvoie le `ImageInsightsToken` pour une utilisation dans une requête de Recherche visuelle ultérieure.

```
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }
```

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Spécifier le jeton ImageInsightsToken pour la requête de Recherche visuelle
Cet exemple utilise le jeton insights renvoyé par la méthode précédente. Le code suivant crée un objet `ImageInfo` à partir de `ImageInsightsToken` et charge l’objet ImageInfo dans un `VisualSearchRequest`. Spécifier `ImageInsightsToken` dans un `ImageInfo` pour le `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Utiliser la Recherche visuelle pour trouver des images à partir d’un jeton ImageInsightsToken
Le `VisualSearchRequest` contient des informations sur l’image à rechercher dans l’objet `ImageInfo`.  La méthode `VisualSearchMethodAsync` obtient les résultats.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Obtenir les données d’URL d’ImageModuleAction
Les résultats de Recherche visuelle sont des objets `ImageTag`.  Chaque balise contient une liste d’objets `ImageAction`.  Chaque `ImageAction` contient un champ `Data` qui est une liste de valeurs qui varient selon le type d’action :

Vous pouvez obtenir les différents types avec le code suivant :
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
L’application complète retourne :

* ActionType : MoreSizes -> WebSearchUrl :
* ActionType : VisualSearch -> WebSearchUrl :
* ActionType : ImageById -> WebSearchUrl :
* ActionType : RelatedSearches -> WebSearchUrl :
* ActionType : DocumentLevelSuggestions -> WebSearchUrl :
* ActionType : TopicResults -> WebSearchUrl : https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1
* ActionType : ImageResults -> WebSearchUrl : https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1

Comme vu dans la liste précédente, les types `TopicResults` et `ImageResults` contiennent des requêtes pour les images associées. Les URL dans la liste sont liées aux résultats de recherche Bing.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>URL d’ActionType PagesIncluding des images trouvées par la recherche visuelle

L’obtention des URL des images réelles nécessite un cast qui lit un `ActionType` comme `ImageModuleAction`, qui contient un élément `Data` avec une liste de valeurs.  Chaque valeur est l’URL d’une image.  Le code suivant convertit le type d’action `PagesIncluding` en `ImageModuleAction` et lit les valeurs.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Pour plus d’informations sur ces types de données, consultez [Images : Recherche visuelle](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).
## <a name="complete-code"></a>Code complet

Le code suivant exécute les exemples précédents. Il envoie le `ImageInsightsToken` dans une requête POST. Il imprime ensuite les URL de Recherche Bing pour chaque ActionType. Si l’ActionType est `PagesIncluding`, le code obtient les éléments `ImageObject` dans `Data`.  Le `Data` contient une liste de valeurs, qui sont les URL des images dans les pages web.  Copiez et collez les URL de recherche visuelle obtenues dans le navigateur pour afficher les résultats. Copiez et collez les éléments ContentUrl dans le navigateur pour afficher les images.

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            insightsToken = ImageResults(subscriptionKey);

            VisualSearchInsightsToken(subscriptionKey, insightsToken);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        // Searches for results on query (Canadian Rockies) and gets an ImageInsightsToken.
        // Also prints first image insights token, thumbnail url, and image content url.
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }


        // This method will get Visual Search results from an imageInsightsToken obtained from the return value of the ImageResults method.
        // The method includes imageInsightsToken the in a knowledgeRequest parameter, along with a cropArea object. 
        // It prints out the imageInsightsToken uploaded in the request.
        // Finally the example prints URLs of images found using the imageInsightsToken.

        public static void VisualSearchInsightsToken(string subscriptionKey, string insightsTok)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object.
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);

                // An image binary is not necessary here, as the image is specified by insights token.
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with imageInsightsToken and knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {insightsTok}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

                    if (visualSearchResults.Tags.Count > 0)
                    {
                        // List of tags
                        foreach (ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            {
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                                    {
                                        Console.WriteLine("ContentURL: " + o.ContentUrl);
                                    }
                                }
                            }
                        }
                    }

                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }

                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
}

```
## <a name="next-steps"></a>Étapes suivantes
[Réponse de recherche visuelle](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
