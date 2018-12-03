---
title: 'Tutoriel : Résultats et zone de rognage de l’image - Recherche visuelle Bing'
description: Comment utiliser le SDK de recherche visuelle Bing pour obtenir les URL des images similaires à la zone de rognage de l’image chargée.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 27141c014c9ccdf9d62c9bde5c96bd31abfc025e
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447093"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Tutoriel : Résultats et zone de rognage de l’image du SDK de recherche visuelle Bing
Le SDK de recherche visuelle inclut une option pour sélectionner une zone d’une image et rechercher des images en ligne qui sont similaires à la zone de rognage de l’image plus grande.  Cet exemple spécifie la zone de rognage montrant une personne d’une image qui contient plusieurs personnes.  Le code envoie la zone de rognage et l’URL de l’image plus grande, puis retourne des résultats qui incluent les URL de recherche Bing et les URL des images similaires disponibles en ligne.

## <a name="prerequisites"></a>Prérequis

Vous avez besoin de [Visual Studio 2017](https://www.visualstudio.com/downloads/) pour que ce code s’exécute sous Windows. (La version Community Edition gratuite fonctionne.)

Pour ce tutoriel, vous devez démarrer un abonnement au niveau tarifaire S9 comme indiqué dans [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Pour démarrer un abonnement dans le portail Azure :
1. Entrez « BingSearchV7 » dans la zone de texte en haut du portail Azure indiquant `Search resources, services, and docs`.  
2. Sous Place de marché dans la liste déroulante, sélectionnez `Bing Search v7`.
3. Entrez `Name` pour la nouvelle ressource.
4. Sélectionnez l’abonnement `Pay-As-You-Go`.
5. Sélectionnez le niveau tarifaire `S9`.
6. Cliquez sur `Enable` pour démarrer l’abonnement.

## <a name="application-dependencies"></a>Dépendances de l’application
Pour configurer une application console à l’aide du kit Web Search SDK Bing, accédez à l’option Gérer les packages NuGet à partir de l’Explorateur de solutions dans Visual Studio. Ajoutez le package Microsoft.Azure.CognitiveServices.Search.VisualSearch.

L’installation du package du Kit de développement logiciel (SDK) Recherche Web NuGet installe également les dépendances, notamment :

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Image et zone de rognage
L’image suivante montre l’équipe des hauts dirigeants de Microsoft.  À l’aide du SDK de recherche visuelle, nous chargeons une zone de rognage de l’image et recherchons d’autres images et pages web qui incluent l’entité dans la zone sélectionnée de l’image plus grande.  Ici, l’entité est une personne.

![Équipe des hauts dirigeants de Microsoft](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Indiquer la zone de rognage comme ImageInfo dans VisualSearchRequest
Cet exemple utilise une zone de rognage de l’image précédente qui spécifie les coordonnées supérieure gauche et inférieure droite en pourcentage de l’ensemble de l’image.  Le code suivant crée un objet `ImageInfo` à partir de la zone de rognage et charge l’objet `ImageInfo` dans un `VisualSearchRequest`.  L’objet `ImageInfo` inclut également l’URL de l’image en ligne.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Rechercher des images similaires à la zone de rognage
`VisualSearchRequest` contient des informations de zone de rognage sur l’image et son URL.  La méthode `VisualSearchMethodAsync` obtient les résultats.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Obtenir les données d’URL d’ImageModuleAction
Les résultats de Recherche visuelle sont des objets `ImageTag`.  Chaque balise contient une liste d’objets `ImageAction`.  Chaque `ImageAction` contient un champ `Data` qui est une liste de valeurs qui varient selon le type d’action :

Vous pouvez obtenir les différents types avec le code suivant :
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
L’application complète retourne :

* ActionType : PagesIncluding WebSearchURL :
* ActionType : MoreSizes WebSearchURL :
* ActionType : VisualSearch WebSearchURL :
* ActionType : ImageById WebSearchURL : 
* ActionType : RelatedSearches  WebSearchURL :
* ActionType : Entity -> WebSearchUrl : https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType : TopicResults -> WebSearchUrl : https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType : ImageResults -> WebSearchUrl : https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

Comme indiqué dans la liste précédente, `ActionType` `Entity` contient une requête de recherche Bing qui retourne des informations sur une personne, un endroit ou une chose reconnaissable.  Les types `TopicResults` et `ImageResults` contiennent des requêtes pour les images associées. Les URL dans la liste sont liées aux résultats de recherche Bing.


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

## <a name="complete-code"></a>Code complet

Le code suivant exécute les exemples précédents. Il envoie un binaire d’image dans le corps de la requête POST ainsi qu’un objet cropArea et affiche les URL de recherche Bing pour chaque ActionType. Si l’ActionType est PagesIncluding, le code obtient les éléments ImageObject dans ImageObject Data.  Data contient une liste de valeurs, qui sont les URL des images dans les pages web.  Copiez et collez les URL de recherche visuelle obtenues dans le navigateur pour afficher les résultats. Copiez et collez les éléments ContentUrl dans le navigateur pour afficher les images.

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

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
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