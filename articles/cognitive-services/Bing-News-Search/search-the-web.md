---
title: Qu’est-ce que Recherche d’actualités Bing ?
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API Recherche d’actualités Bing pour rechercher des actualités sur le web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: overview
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 7a5df20358198e7ccb44ccc60be3ab7716f71ba6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227856"
---
# <a name="what-is-bing-news-search"></a>Qu’est-ce que Recherche d’actualités Bing ?

L’API Recherche d’actualités Bing offre une expérience similaire (mais pas identique) à [Bing Actualités](https://www.bing.com/news). L’API Recherche d’actualités Bing vous permet d’envoyer une requête de recherche à Bing et d’obtenir une liste d’articles d’actualités pertinents.

Si vous souhaitez créer une page de résultats servant à afficher uniquement des actualités en rapport avec la requête de recherche d’un utilisateur, appelez cette API plutôt que [l’API Recherche Web Bing](../bing-web-search/search-the-web.md) plus générale. Pour afficher des actualités et d’autres types de contenu, notamment des pages web, des images et des vidéos, appelez l’API Recherche Web Bing.

## <a name="suggesting--using-search-terms"></a>Suggestion et utilisation de termes de recherche

Si vous fournissez une zone de recherche dans laquelle l’utilisateur entre son terme de recherche, utilisez [l’API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md) pour améliorer l’expérience. À mesure que l’utilisateur tape des termes de recherche, l’API suggère des chaînes de requête.

Une fois que l’utilisateur a entré son terme de recherche, encodez-le sous forme d’URL avant de définir le paramètre de requête [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query). Par exemple, si l’utilisateur entre *sailing dinghies*, définissez `q` avec la valeur `sailing+dinghies` ou `sailing%20dinghies`.

## <a name="getting-general-news"></a>Obtention d’actualités générales

Pour obtenir des articles d’actualités généraux à partir du web en rapport avec le terme de recherche de l’utilisateur, envoyez la requête GET suivante :

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Si vous appelez l’une des API Bing pour la première fois, n’incluez pas l’en-tête ID client. Indiquez uniquement l’ID client si vous avez précédemment appelé une API Bing et si Bing a renvoyé un ID client pour la combinaison utilisateur/appareil.

Pour obtenir des actualités à partir d’un domaine spécifique, utilisez l’opérateur de requête [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Le code suivant affiche la réponse à la requête précédente. Vous devez afficher chaque article d’actualités dans l’ordre dans lequel il apparaît dans la réponse. Si l’article comporte un cluster d’articles, vous devez indiquer qu’il existe des articles associés et les afficher si l’utilisateur demande à les voir.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
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
    }]
}
```

La réponse de type [actualités](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#news) répertorie les articles d’actualités que Bing considère comme pertinents vis-à-vis de la requête. Le champ `totalEstimatedMatches` contient une estimation du nombre d’articles disponibles pour visualisation. Pour plus d’informations sur la pagination des articles, consultez [Pagination des actualités](./paging-news.md).

Chaque [article d’actualités](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#newsarticle) de la liste inclut le nom de l’article, une description et l’URL de l’article sur le site web de l’hôte. Si l’article contient une image, l’objet inclut une miniature de l’image. Utilisez `name` et `url` pour créer un lien hypertexte permettant à l’utilisateur d’accéder à l’article d’actualité sur le site de l’hôte. Si l’article inclut une image, rendez-la également interactive en utilisant `url`. Veillez à utiliser `provider` pour attribuer l’article.

Si Bing peut déterminer la catégorie d’un article d’actualités, l’article inclut le champ `category`.

## <a name="getting-todays-top-news"></a>Obtention des principales actualités du jour

Pour obtenir les principales actualités du jour, vous devez effectuer la même requête que pour obtenir des actualités générales, mais sans définir `q`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

La réponse à une requête portant sur les principales actualités est presque identique à celle concernant les actualités générales. Toutefois, la réponse `news` n’inclut pas le champ `totalEstimatedMatches`, car il existe un nombre fixe de résultats. Le nombre d’articles d’actualités principales peut varier en fonction du cycle d’actualités. Veillez à utiliser `provider` pour attribuer l’article.

## <a name="getting-news-by-category"></a>Obtention des actualités par catégorie

Pour obtenir des articles d’actualités par catégorie, par exemple les principaux articles de sport ou de divertissement, envoyez la requête GET suivante à Bing :

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Utilisez le paramètre de requête [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) pour spécifier la catégorie d’articles à obtenir. Pour connaître la liste des catégories d’actualités que vous pouvez spécifier, consultez [News Categories by Market](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-categories-by-market).

La réponse à une requête portant sur les actualités par catégorie est presque identique à celle concernant les actualités générales. Toutefois, les articles appartiennent tous à la catégorie spécifiée.

## <a name="getting-headline-news"></a>Obtention des titres de l’actualité

Pour demander des articles sur les titres de l’actualité et recevoir des articles de toutes les catégories d’actualités, envoyez la requête GET suivante à Bing :

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

N’incluez pas le paramètre de requête [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category).

La réponse à une requête portant sur les titres de l’actualité est identique à celle concernant les principales actualités du jour. Si l’article est un article de « gros titre », son champ `headline` est défini sur **true**.

Par défaut, la réponse inclut jusqu’à 12 articles de gros titre. Pour changer le nombre d’articles de gros titre à retourner, spécifiez le paramètre de requête [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headlinecount). La réponse inclut également jusqu’à quatre articles « hors gros titre » par catégorie d’actualités.

La réponse comptabilise les clusters comme un seul article. Un cluster pouvant avoir plusieurs articles, la réponse peut inclure plus de 12 articles de gros titre et plus de quatre articles hors gros titre par catégorie.

## <a name="getting-trending-news"></a>Obtention des actualités tendance

Pour obtenir les sujets d’actualité tendance sur les réseaux sociaux, envoyez la requête GET suivante à Bing :

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> Les rubriques tendance sont disponibles uniquement sur les marchés en-US et zh-CN.

Le code JSON suivant est la réponse à la requête précédente. Chaque article d’actualités tendance inclut une image associée, l’indicateur « Dernières nouvelles » et une URL vers les résultats de recherche Bing pour l’article. Utilisez l’URL du champ `webSearchUrl` pour diriger l’utilisateur vers la page des résultats de la recherche Bing. Ou utilisez le texte de requête pour appeler l’[API Recherche Web](../bing-web-search/search-the-web.md) afin d’afficher les résultats vous-même.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>Obtention des actualités associées

S’il existe d’autres articles associés à un article d’actualités, celui-ci peut-être inclure le champ [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle-clusteredarticles). L’exemple suivant montre un article avec des articles en cluster.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>Demandes de limitation

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour configurer rapidement votre première requête, consultez [Création de votre première requête](./quickstart.md).

Familiarisez-vous avec les informations de référence sur [l’API Recherche d’actualités Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference). Vous y trouverez la liste des points de terminaison, en-têtes et paramètres de requête à utiliser pour demander les résultats de la recherche. Vous y trouverez également les définitions des objets de réponse.

Pour améliorer l’expérience utilisateur dans la zone de recherche, consultez [API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md). À mesure que l’utilisateur entre son terme de requête, vous pouvez appeler cette API pour obtenir des termes pertinents utilisés par d’autres personnes.

Prenez soin de lire les [exigences relatives à l’affichage et à l’utilisation de Bing](./useanddisplayrequirements.md) pour respecter les règles d’utilisation des résultats de la recherche.