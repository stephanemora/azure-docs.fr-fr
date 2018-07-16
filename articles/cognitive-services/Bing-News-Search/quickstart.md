---
title: Démarrage rapide de l’API Recherche d'actualités | Microsoft Docs
description: Prise en main de l’API Recherche d'actualités Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 9CF6EAF3-42D8-4321-983C-4AC3896E8E03
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2aa1b3de07bd61eebfc1d410cda76c32fc7c958e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370165"
---
# <a name="your-first-news-search-query"></a>Votre première requête de recherche d’actualités

Avant de pouvoir passer votre premier appel, vous devez obtenir une clé d’abonnement Cognitive Services. Pour obtenir une clé, consultez [Essayer Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api).

Pour obtenir les résultats de la recherche d’actualités uniquement, vous devez envoyer une requête GET au point de terminaison suivant :

```http
https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

La requête doit utiliser le protocole HTTPS.

Nous vous recommandons de générer toutes les requêtes à partir d’un serveur. Si vous diffusez la clé dans le cadre d’une application client, vous prenez le risque qu’un tiers malveillant puisse y accéder. Par ailleurs, en appelant l’API à partir d’un serveur, vous disposez d’un unique point de mise à niveau pour les prochaines versions de l’API.

La requête doit indiquer le paramètre [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query), qui contient le terme de recherche de l’utilisateur. La requête peut également indiquer le paramètre [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#mkt) (facultatif), qui vous permet de choisir le marché d’où proviennent les résultats. Pour obtenir la liste des paramètres de requête facultatifs tels que `freshness` et `textDecorations`, consultez la page [Paramètres de requête](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters). Toutes les valeurs de paramètres de requête doivent être codées au format URL.

La requête doit indiquer l’en-tête [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#subscriptionkey). Nous vous conseillons également d’indiquer les en-têtes suivants (qui sont facultatifs) :

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#location)

Les en-têtes d’emplacement et d’adresse IP client sont importants, car ils permettent de renvoyer du contenu géolocalisé.

Pour obtenir la liste complète des en-têtes de requête et de réponse, consultez la voir [En-têtes](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headers).

## <a name="the-request"></a>Requête

Vous trouverez ci-dessous une requête d’actualités qui inclut tous les paramètres de requête et les en-têtes suggérés. Si vous appelez l’une des API Bing pour la première fois, n’incluez pas l’en-tête d’ID client. Indiquez uniquement l’ID client si vous avez précédemment appelé une API Bing et que Bing a renviyé un ID client pour la combinaison utilisateur/appareil.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="the-response"></a>Réponse

Le code suivant affiche la réponse à la requête précédente. L’exemple montre également les en-têtes de réponse propres à Bing.

```http
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "Sailing College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },
    {
        "name" : "Reunion at Fabrikam Lakes Sailing Club, celebrates 50 years...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.38210...",
                "width" : 650,
                "height" : 366
            }
        },
        "description" : "The reunion on April 29, at Fabrikam Lakes Sailing...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T13:08:00"
    },
    {
        "name" : "Sailing Club to host Dinghy Sailing World...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.364AB41...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    },
    {
        "name" : "A 24-Carat Dinghy",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.6CC2...",
                "width" : 700,
                "height" : 466
            }
        },
        "description" : "“Hard dinghies are for purists, the kind of people who...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-03T12:14:00",
        "category" : "Politics"
    }]
}
```

## <a name="next-steps"></a>Étapes suivantes

Essayez l’API. Accédez à la [console de test de l’API Recherche d'actualités](https://dev.cognitive.microsoft.com/docs/services/56b43f72cf5ff8098cef380a/operations/56f02400dbe2d91900c68553).

Pour en savoir plus sur la consommation des objets de réponse, consultez la page [What is Bing News Search ?](./search-the-web.md) (Qu’est-ce que la recherche d'actualités Bing ?). Vous trouverez également plus d’informations sur les actions courantes suivantes :

- [Obtention des principales actualités du jour](./search-the-web.md#getting-todays-top-news)
- [Obtention des actualités par catégorie](./search-the-web.md#getting-news-by-category)
- [Obtention des actualités à la une](./search-the-web.md#getting-trending-news)
