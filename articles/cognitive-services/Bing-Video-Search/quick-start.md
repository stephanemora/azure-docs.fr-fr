---
title: 'Démarrage rapide : API Recherche de vidéos Bing'
titlesuffix: Azure Cognitive Services
description: Prise en main de l’API Recherche de vidéos Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: abeeec95755a566216ac65b2edf5c831a8ab93b6
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225512"
---
# <a name="quickstart-your-first-video-search-query"></a>Démarrage rapide : Votre première requête de recherche de vidéos

Avant de passer votre premier appel d’API, vous devez obtenir une clé d’abonnement Recherche Bing Cognitive Services. Pour obtenir une clé, consultez [Essayer Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api).

Pour obtenir les résultats de la recherche de vidéo, vous devez envoyer une requête GET au point de terminaison suivant :  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
La requête doit utiliser le protocole HTTPS.

Nous vous recommandons de générer toutes les requêtes à partir d’un serveur. Si vous diffusez la clé dans le cadre d’une application client, vous prenez le risque qu’un tiers malveillant puisse y accéder. Par ailleurs, en appelant l’API à partir d’un serveur, vous disposez d’un unique point de mise à niveau pour les prochaines versions de l’API.

  
La requête doit indiquer le paramètre [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query), qui contient le terme de recherche de l’utilisateur. La requête peut également indiquer le paramètre [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#mkt) (facultatif), qui vous permet de choisir le marché d’où proviennent les résultats. Pour obtenir la liste des paramètres de requête facultatifs tels que `pricing`, consultez la page [Paramètres de la requête](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters). Toutes les valeurs de paramètres de requête doivent être codées au format URL.  
  
La requête doit indiquer l’en-tête [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#subscriptionkey). Nous vous conseillons également d’indiquer les en-têtes suivants (qui sont facultatifs) :  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#location)  

Les en-têtes d’emplacement et d’adresse IP client sont importants, car ils permettent de renvoyer du contenu géolocalisé.  

Pour obtenir la liste complète des en-têtes de requête et de réponse, consultez la page [En-têtes](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#headers).


## <a name="the-request"></a>La requête

Vous trouverez ci-dessous une requête de recherche qui inclut tous les paramètres de requête et les en-têtes suggérés. Si vous appelez l’une des API Bing pour la première fois, n’incluez pas l’en-tête ID client. Indiquez uniquement l’ID client si vous avez précédemment appelé une API Bing et si Bing a renvoyé un ID client pour la combinaison utilisateur/appareil. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Voici la réponse à la requête précédente. L’exemple montre également les en-têtes de réponse propres à Bing.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Essayez l’API. Accédez à la [console de test de l’API Recherche de vidéos](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8). 

Pour plus d’informations sur la consommation des objets de réponse, consultez [Recherche de vidéos sur le web](./search-the-web.md).

Pour plus d’informations sur l’obtention d’informations sur une vidéo telles que des recherches connexes, consultez [Video Insights](./video-insights.md) (Informations sur la vidéo).  
  
Pour plus d’informations sur les vidéos populaires sur les réseaux sociaux, consultez [Vidéos populaires](./trending-videos.md).  
