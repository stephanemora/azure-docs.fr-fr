---
title: Qu’est-ce que la Recherche de vidéos Bing ?
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API Recherche de vidéos Bing pour rechercher des vidéos sur le web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: overview
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: cf37db9bffa8b2a54a6327c29ec806e0eefc8c91
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225410"
---
# <a name="what-is-bing-video-search"></a>Qu’est-ce que la Recherche de vidéos Bing ?

L’API Recherche de vidéos Bing offre une expérience similaire (mais pas identique) à [Bing Vidéos](https://www.bing.com/videos). L’API Recherche de vidéos Bing vous permet d’envoyer une requête de recherche à Bing et d’obtenir une liste de vidéos pertinentes.

Si vous souhaitez créer une page de résultats servant à afficher uniquement des vidéos en rapport avec la requête de recherche d’un utilisateur, appelez cette API plutôt que [l’API Recherche Web Bing](../bing-web-search/search-the-web.md) qui est plus générale. Pour afficher des vidéos et d’autres types de contenu, notamment des pages web, des actualités et des images, appelez l’API Recherche Web Bing.

## <a name="suggesting--using-search-terms"></a>Suggestion et utilisation de termes de recherche

Si vous fournissez une zone de recherche dans laquelle l’utilisateur entre son terme de recherche, utilisez [l’API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md) pour améliorer l’expérience. À mesure que l’utilisateur tape des termes de recherche, l’API suggère des chaînes de requête.

Une fois que l’utilisateur a entré son terme de recherche, encodez-le sous forme d’URL avant de définir le paramètre de requête [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query). Par exemple, si l’utilisateur entre *sailing dinghies*, définissez `q` avec la valeur `sailing+dinghies` ou `sailing%20dinghies`.

## <a name="getting-videos"></a>Obtention de vidéos

Pour obtenir des vidéos web en rapport avec le terme de recherche de l’utilisateur, envoyez la requête GET suivante :

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Toutes les demandes doivent provenir d’un serveur.

Si vous appelez l’une des API Bing pour la première fois, n’incluez pas l’en-tête ID client. Indiquez uniquement l’ID client si vous avez précédemment appelé une API Bing et si Bing a renvoyé un ID client pour la combinaison utilisateur/appareil.

Pour obtenir des vidéos à partir d’un domaine spécifique, utilisez l’opérateur de requête [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

La réponse contient un objet [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) comprenant une liste de vidéos qui, selon Bing, ont un rapport avec la requête. Chaque objet [Video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) de la liste inclut, entre autres attributs, l’URL de la vidéo, sa durée, ses dimensions et son format d’encodage. L’objet vidéo comprend également l’URL et les dimensions d’une miniature de la vidéo.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

Vous pouvez afficher un montage de toutes les vidéos miniatures, ou une partie des miniatures. Si vous affichez une partie, donnez à l’utilisateur la possibilité d’afficher les vidéos restantes. Vous devez afficher les vidéos dans l’ordre suivant lequel elles apparaissent dans la réponse. Pour plus d’informations sur le redimensionnement de la miniature, consultez [Redimensionnement et rognage de miniatures](./resize-and-crop-thumbnails.md). 

Vous pouvez utiliser [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-motionthumbnailurl) pour lire une version miniature de la vidéo lorsque l’utilisateur pointe sur la miniature. Veillez à attribuer la vidéo miniature lorsque vous l’affichez.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Les options d’affichage vidéo suivantes sont disponibles lorsque l’utilisateur clique sur la miniature :

- Utilisez [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-hostpageurl) pour afficher la vidéo sur le site web hôte (par exemple, YouTube)
- Utilisez [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-websearchurl) pour afficher la vidéo dans le navigateur vidéo Bing
- Utilisez [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-embedhtml) pour incorporer la vidéo à votre propre expérience 

Veillez à utiliser l’éditeur et le créateur pour attribuer la vidéo lors de sa lecture.

Pour en savoir plus sur l’utilisation de [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) et obtenir des informations sur la vidéo, consultez [Informations sur les vidéos](./video-insights.md).

## <a name="filtering-videos"></a>Filtrage de vidéos

Par défaut, l’API Recherche de vidéos retourne toutes les vidéos ayant un rapport avec la requête. Si vous souhaitez uniquement obtenir des vidéos gratuites ou d’une durée inférieure à cinq minutes, préférez l’utilisation des paramètres de requête de filtre suivants :

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#pricing)&mdash;Filtre les vidéos par prix (par exemple, celles qui sont gratuites ou celles pour lesquelles vous devez payer)
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#resolution)&mdash;Filtre les vidéos par résolution (par exemple, celles qui ont une résolution de 720p ou supérieure)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videolength)&mdash;Filtre les vidéos par durée de vidéo (par exemple, celles dont la durée est inférieure à cinq minutes)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#freshness)&mdash;Filtre les vidéos selon leur ancienneté (par exemple, celles découvertes par Bing la semaine dernière)

Pour obtenir des vidéos à partir d’un domaine spécifique, ajoutez l’opérateur de requête [site:](http://msdn.microsoft.com/library/ff795613.aspx) dans la chaîne de requête.

> [!NOTE]
> En fonction de la requête, si vous utilisez l’opérateur de requête `site:`, il est possible que la réponse présente du contenu pour adultes, et ce quel que soit le paramètre [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#safesearch) défini. Utilisez `site:` uniquement si vous connaissez le contenu du site et si votre scénario prend en charge le contenu pour adultes.

L’exemple suivant montre comment obtenir des vidéos gratuites, à partir de ContosoSailing.com, qui affichent une résolution de 720p minimum et que Bing a découvert le mois dernier.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Développement de la requête

Si Bing peut développer la requête pour affiner la recherche d’origine, l’objet [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) contient le champ `queryExpansions`. Par exemple, si la requête était le *nettoyage de gouttières*, les requêtes développées pourraient être : **outils** pour le nettoyage de gouttières, nettoyage de gouttières **à partir du sol**, **appareil** pour le nettoyage de gouttières et nettoyage de gouttières **facile**.

L’exemple suivant montre les requêtes développées pour le *nettoyage de gouttières* (Cleaning Gutters).

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

Le champ `queryExpansions` contient une liste d’objets [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj). Le champ `text` contient la requête développée, et `displayText` le terme de développement. Vous pouvez utiliser les champs text et thumbnail pour montrer les chaînes des requêtes développées à l’utilisateur si c’est vraiment ce qu’il recherche. Pour rendre la miniature et le texte interactifs, utilisez l’URL `webSearchUrl` ou l’URL `searchLink`. Utilisez `webSearchUrl` pour envoyer à l’utilisateur les résultats de la recherche Bing, ou `searchLink` si vous fournissez votre propre page de résultats.

## <a name="pivoting-the-query"></a>Création d’un tableau croisé dynamique à partir de la requête

Si Bing peut segmenter la requête de recherche d’origine, l’objet [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) contient le champ `pivotSuggestions`. Par exemple, Bing peut segmenter la requête d’origine *Nettoyage de gouttières* en *Nettoyage* et *Gouttières*.

L’exemple suivant montre les suggestions de tableau croisé dynamique pour *Nettoyage de gouttières*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Pour chaque tableau croisé dynamique, la réponse contient une liste d’objets [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj) contenant les requêtes suggérées. Le champ `text` contient la requête suggérée, et `displayText` le terme qui remplace le tableau croisé dynamique dans la requête d’origine. Par exemple, le nettoyage de fenêtres.

Vous pouvez utiliser les champs `text` et `thumbnail` pour présenter les chaînes des requêtes développées à l’utilisateur si c’est vraiment ce qu’il recherche. Pour rendre la miniature et le texte interactifs, utilisez l’URL `webSearchUrl` ou l’URL `searchLink`. Utilisez `webSearchUrl` pour envoyer à l’utilisateur les résultats de la recherche Bing, ou `searchLink` si vous fournissez votre propre page de résultats.

## <a name="throttling-requests"></a>Demandes de limitation

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour configurer rapidement votre première requête, consultez [Création de votre première requête](./quick-start.md).

Pour obtenir votre clé d’abonnement, consultez [Clés d’abonnement](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api).

Familiarisez-vous avec les informations de référence sur [l’API Recherche de vidéos Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference). Vous y trouverez la liste des points de terminaison, en-têtes et paramètres de requête à utiliser pour demander les résultats de la recherche. Vous y trouverez également les définitions des objets de réponse. 

Pour améliorer l’expérience utilisateur dans la zone de recherche, consultez [API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md). À mesure que l’utilisateur entre son terme de requête, vous pouvez appeler cette API pour obtenir des termes pertinents utilisés par d’autres personnes.

Prenez soin de lire les [exigences relatives à l’affichage et à l’utilisation de Bing](./useanddisplayrequirements.md) pour respecter les règles d’utilisation des résultats de la recherche.

Lorsque vous appelez l’API Recherche de vidéos Bing, Bing retourne une liste de résultats. La liste est un sous-ensemble du nombre total de résultats en rapport avec la requête. Le champ `totalEstimatedMatches` de la réponse contient une estimation du nombre de vidéos disponibles pour être regardées. Pour plus d’informations sur la navigation dans les vidéos restantes, consultez [Navigation dans les vidéos](./paging-videos.md).

Pour en savoir plus sur l’obtention d’informations relatives à une vidéo, consultez [Informations sur les vidéos](./video-insights.md).

Pour plus d’informations sur l’obtention des vidéos du moment, consultez [Vidéos populaires](./trending-videos.md).