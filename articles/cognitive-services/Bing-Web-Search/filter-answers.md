---
title: 'Comment filtrer les résultats de la recherche : API Recherche Web Bing'
titleSuffix: Azure Cognitive Services
description: Découvrez comment filtrer et afficher les résultats de recherche obtenus avec l’API Recherche Web Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 8d8fd03d9c3d912788e9893377bbab3efac86f8a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66383832"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrage des correspondances contenues dans la réponse de recherche  

Lorsque vous interrogez le web, Bing renvoie tout le contenu qu’il juge pertinent pour la recherche. Par exemple, si la requête de recherche est « sailing+dinghies », la réponse peut contenir les correspondances suivantes :

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```
Vous pouvez filtrer les types de contenu que vous recevez (images, vidéos, actualités, par exemple) à l’aide du paramètre de requête [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter). Bing renvoie le contenu qu’il juge pertinent pour les réponses spécifiées. Le filtre de réponse est une liste de correspondances séparées par des virgules. 

Pour exclure de la réponse certains types de contenu, tels que des images, vous pouvez ajouter un caractère `-` au début de la valeur `responseFilter`. Vous pouvez séparer les types de contenu exclus par une virgule (`,`). Par exemple :

```
&responseFilter=-images,-videos
```


Le code suivant montre comment utiliser `responseFilter` pour demander des images, des vidéos et des actualités sur les dériveurs. Quand vous encodez la chaîne de requête, les virgules sont remplacées par %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Le code suivant affiche la réponse à la requête précédente. Bing n’ayant pas trouvé de résultats pertinents en matière de vidéos et d’actualités, la réponse ne les inclut pas.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Même si Bing n’a pas retourné de résultats en matière de vidéos et d’actualités dans la réponse précédente, cela ne signifie pas que ce contenu n’existe pas. Cela signifie simplement que la page ne contient pas ces résultats. Toutefois, si vous [parcourez](./paging-webpages.md) plus de résultats, il est probable qu’ils s’affichent dans les pages suivantes. En outre, si vous appelez directement les points de terminaison [d’API Recherche de vidéos](../bing-video-search/search-the-web.md) et [d’API Recherche d’actualités](../bing-news-search/search-the-web.md), la réponse contient probablement les résultats.

Il est déconseillé d’utiliser `responseFilter` pour obtenir les résultats à partir d’une seule API. Si vous souhaitez obtenir le contenu d’une seule API Bing, appelez cette API directement. Par exemple, pour recevoir uniquement des images, envoyez une demande au point de terminaison d’API Recherche d’images, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` ou à l’un des autres points de terminaison [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints). L’appel de l’API unique est important non seulement pour des raisons de performances, mais parce que les API spécifiques au contenu offrent des résultats plus riches. Par exemple, vous pouvez utiliser des filtres qui ne sont pas disponibles pour l’API Recherche web pour filtrer les résultats.  

Pour obtenir des résultats de recherche à partir d’un domaine spécifique, ajoutez l’opérateur de requête `site:` dans la chaîne de requête.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> En fonction de la requête, si vous utilisez l’opérateur de requête `site:`, il est possible que la réponse présente du contenu pour adultes, et ce quel que soit le paramètre [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) défini. Utilisez `site:` uniquement si vous connaissez le contenu du site et si votre scénario prend en charge le contenu pour adultes.

## <a name="limiting-the-number-of-answers-in-the-response"></a>Limitation du nombre de correspondances dans la réponse

Bing inclut des correspondances dans la réponse en fonction du classement. Par exemple, si votre requête porte sur *sailing+dinghies*, Bing retourne `webpages`, `images`, `videos` et `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Pour limiter le nombre de réponses retournées par Bing aux deux premières (pages web et images), affectez au paramètre de requête [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) la valeur 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

La réponse inclut uniquement `webPages` et `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Si vous ajoutez le paramètre de requête `responseFilter` à la requête précédente avec pour valeur les pages web et actualités, la réponse contient uniquement des pages web, car les actualités ne sont pas classées.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Promotion des correspondances qui ne sont pas classées

Si les correspondances en tête du classement retournées par Bing pour une requête sont des pages web, images, vidéos et relatedSearches, la réponse inclut ces correspondances. Si vous affectez à [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) la valeur deux (2), Bing retourne les deux correspondances en tête du classement : pages web et images. Si vous souhaitez que Bing ajoute des images et vidéos dans la réponse, spécifiez le paramètre de requête [promote](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) et définissez-le sur les images et vidéos.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Voici la réponse à la requête ci-dessus. Bing retourne les deux premières correspondances, pages web et images, et promeut les vidéos dans la correspondance.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Si vous définissez `promote` sur les actualités, la réponse n’inclut pas la correspondance actualités, car il ne s’agit pas d’une correspondance classée &mdash; vous pouvez promouvoir uniquement des correspondances classées.

Les correspondances que vous souhaitez promouvoir n’entrent pas dans la limite `answerCount`. Par exemple, si les correspondances classées sont des actualités, des images et des vidéos, et que vous affectez à `answerCount` la valeur 1 et définissez `promote` sur les actualités, la réponse contient des actualités et des images. Par ailleurs, si les correspondances classées sont des actualités, des images et des vidéos, la réponse contient des vidéos et des actualités.

Vous pouvez utiliser `promote` uniquement si vous spécifiez le paramètre de requête `answerCount`.
