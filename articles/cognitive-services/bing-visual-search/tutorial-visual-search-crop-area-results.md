---
title: 'Didacticiel : Rogner une image avec le SDK Recherche visuelle Bing'
description: Utilisez le SDK Recherche visuelle Bing pour obtenir des informations à partir d’une zone spécifique d’une image.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/03/2019
ms.author: rosh
ms.openlocfilehash: a6a98d9ddf76e86feb35c6d3d71f7777b0edd51d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59491132"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Didacticiel : Rogner une image avec le SDK Recherche visuelle Bing pour C#

Le SDK de recherche Bing Visual vous permet de rogner une image avant de trouver des images en ligne similaires. Cette application rogne une seule personne à partir d’une image contenant plusieurs personnes, puis retourne les résultats de recherche contenant des images similaires disponibles en ligne.

Le code source complet de cette application est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs) avec une gestion des erreurs et des annotations supplémentaires.

Le didacticiel illustre les actions suivantes :

> [!div class="checklist"]
> * Envoyer une requête à l’aide du SDK Recherche visuelle Bing
> * Rogner une zone d’image pour effectuer une recherche avec Recherche visuelle Bing
> * Recevoir et gérer la réponse
> * Trouver les URL des éléments d’action dans la réponse

## <a name="prerequisites"></a>Conditions préalables

* N’importe quelle édition de [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Si vous utilisez Linux/MacOS, cette application peut être exécutée à l’aide de [Mono](https://www.mono-project.com/).
* Package [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) installé.
    - À partir de l’Explorateur de solutions dans Visual Studio, cliquez sur votre projet, puis sélectionnez **gérer les Packages NuGet** à partir du menu. Installez le package `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. L’installation du package NuGet Custom Search installe aussi les assemblys suivants :
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Spécifier la zone d'image à rogner

Cette application rogne une zone de cette image de l’Équipe des hauts dirigeants de Microsoft. Cette zone de la culture est définie à l’aide des coordonnées supérieur gauche et inférieur droit, représentées sous forme de pourcentage de l’image entière :  

![Équipe des hauts dirigeants de Microsoft](./media/MS_SrLeaders.jpg)

Cette image est rognée par la création d’un objet `ImageInfo` à partir de la zone de rognage et par le chargement de l’objet `ImageInfo` dans un `VisualSearchRequest`. Le `ImageInfo` objet inclut également l’URL de l’image :

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Rechercher des images similaires à la zone de rognage

La variable `VisualSearchRequest` contient des informations sur la zone de rogner l’image et son URL. Le `VisualSearchMethodAsync()` méthode obtient les résultats :

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Obtenir les données d’URL à partir de `ImageModuleAction`

Les résultats de Recherche visuelle Bing sont des objets `ImageTag`. Chaque balise contient une liste d’objets `ImageAction`. Chaque `ImageAction` contient un `Data` champ, qui est une liste de valeurs qui varient selon le type d’action.

Vous pouvez imprimer les différents types avec le code suivant :

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

L’application complète retourne :

|ActionType  |URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entity -> WebSearchUrl     | https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Comme indiqué ci-dessus, l’ActionType `Entity` contient une requête de recherche Bing qui retourne des informations sur une personne, un endroit ou une chose reconnaissable. Les types `TopicResults` et `ImageResults` contiennent des requêtes pour les images associées. Les URL dans la liste sont liées aux résultats de recherche Bing.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Obtenir les URL pour `PagesIncluding` `ActionType` images

L’obtention des URL des images réelles nécessite un cast qui lit un `ActionType` comme `ImageModuleAction`, qui contient un élément `Data` avec une liste de valeurs. Chaque valeur est l’URL d’une image. Les conversions suivantes le `PagesIncluding` type d’action à `ImageModuleAction` et lit les valeurs :

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Créer une application web de page unique de recherche visuelle](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Voir aussi
> [Qu’est l’API de recherche visuelle Bing ?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
