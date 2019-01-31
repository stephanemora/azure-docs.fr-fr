---
title: Recherche de vidéos populaires sur le web - Recherche de vidéos Bing
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API Recherche de vidéos Bing pour rechercher des vidéos populaires sur le web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 71dd888199e9e630835c4916d35f6308431bab62
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203553"
---
# <a name="get-trending-videos"></a>Obtenir des vidéos populaires  

Pour obtenir des vidéos qui sont populaires à l’heure actuelle, envoyez la requête GET suivante :  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

Les marchés suivants prennent en charge les vidéos populaires.  
 
-   en-AU (anglais, Australie)  
-   en-CA (anglais, Canada)  
-   en-GB (anglais, Grande-Bretagne)  
-   en-ID (anglais, Indonésie)  
-   en-IE (anglais, Irlande)  
-   en-IN (anglais, Inde)  
-   en-NZ (anglais, Nouvelle-Zélande)  
-   en-PH (anglais, Philippines)  
-   en-SG (anglais, Singapour)  
-   en-US (anglais, États-Unis)  
-   en-WW (anglais, code agrégé mondial)  
-   en-ZA (anglais, Afrique du Sud)  
-   zh-CN (chinois, Chine)

  
L’exemple illustre une réponse comportant les vidéos populaires.  

```  
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```  
La réponse contient une liste de vidéos classées par catégorie et sous-catégorie. Par exemple, si la liste de catégories contient une catégorie de vidéos musicales et que l’une de ses sous-catégories correspond aux vidéos les plus regardées, vous pouvez créer une catégorie correspondant aux vidéos musicales les plus regardées dans votre expérience utilisateur. Ensuite, vous pouvez utiliser les champs `thumbnailUrl`, `displayText`, et `webSearchUrl` pour créer une vignette interactive sous chaque catégorie (par exemple, vidéos musicales les plus regardées). Lorsque l’utilisateur clique sur la vignette, il accède au navigateur de vidéo Bing où il peut regarder la vidéo.

La réponse contient également les vidéos bannières, qui correspondent aux vidéos populaires les plus regardées. Les vidéos bannières peuvent provenir d’une ou plusieurs catégories.  
  
