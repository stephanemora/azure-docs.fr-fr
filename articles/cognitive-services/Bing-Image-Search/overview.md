---
title: Qu’est-ce que la Recherche d’images Bing ? | Microsoft Docs
description: Découvrez comment utiliser l’API Recherche d’images Bing pour rechercher des images sur le web.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336517"
---
# <a name="what-is-bing-image-search"></a>Qu’est-ce que la Recherche d’images Bing ?

L’API Recherche d’images Bing offre une expérience similaire à [Bing Images](https://www.bing.com/images), en ce sens qu’elle vous permet d’envoyer une requête de recherche à Bing pour obtenir une liste d’images pertinentes.

Si vous souhaitez créer une page de résultats servant à afficher uniquement des images en rapport avec la requête de recherche d’un utilisateur, appelez cette API plutôt que [l’API Recherche Web](../bing-web-search/search-the-web.md) plus générale. Pour afficher des images et d’autres types de contenu, notamment des pages web, des actualités et des vidéos, appelez dans ce cas l’API Recherche Web.

## <a name="suggesting--using-search-terms"></a>Suggestion et utilisation de termes de recherche

Si vous fournissez une zone de recherche dans laquelle l’utilisateur entre son terme de recherche, utilisez [l’API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md) pour améliorer l’expérience. À mesure que l’utilisateur tape des termes de recherche, l’API suggère des chaînes de requête.

Quand l’utilisateur a terminé d’entrer son terme de recherche, encodez-le par URL avant de définir le paramètre de requête [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query). Par exemple, si l’utilisateur entre *sailing dinghies*, définissez `q` avec la valeur `sailing+dinghies` ou `sailing%20dinghies`.

## <a name="getting-images"></a>Obtention d’images

Pour obtenir des images web en rapport avec le terme de recherche de l’utilisateur, envoyez la demande GET suivante :

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Toutes les demandes doivent provenir d’un serveur. Vous ne pouvez pas effectuer d’appels à partir d’un client.

Si vous appelez l’une des API Bing pour la première fois, n’incluez pas l’en-tête ID client. Indiquez uniquement l’ID client si vous avez précédemment appelé une API Bing et que Bing a retourné un ID client pour la combinaison utilisateur/appareil.

Pour obtenir des images à partir d’un domaine spécifique, utilisez l’opérateur de requête [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

La réponse contient un objet [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) comprenant une liste d’images qui, selon Bing, ont un rapport avec la requête. Chaque objet [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) dans la liste inclut l’URL de l’image, sa taille, ses dimensions et son format d’encodage. L’objet Image comprend également l’URL et les dimensions d’une miniature de l’image.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Vous pouvez afficher un montage de toutes les images miniatures ou un sous-ensemble des miniatures. Si vous affichez un sous-ensemble, offrez à l’utilisateur l’option d’afficher les images restantes. Vous devez afficher les images dans l’ordre dans lequel elles apparaissent dans la réponse.

Vous pouvez également développer la miniature quand l’utilisateur pointe sur celle-ci. Veillez à attribuer l’image si vous la développez. Vous pouvez par exemple extraire l’hôte de [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) et l’afficher sous l’image. Pour plus d’informations sur le redimensionnement de la miniature, consultez [Redimensionnement et rognage de miniatures](./resize-and-crop-thumbnails.md).

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Si l’utilisateur clique sur la miniature, vous pouvez utiliser [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) pour afficher l’image en taille réelle. Veillez à attribuer l’image.

Si `shoppingSourcesCount` ou `recipeSourcesCount` est supérieur à zéro, ajoutez un badge à la miniature, par exemple un panier d’achat, pour indiquer que l’élément dans l’image est associé à des achats ou à des recettes.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Pour obtenir des insights sur l’image, par exemple les pages web dans lesquelles figure l’image ou les personnes reconnues dans l’image, utilisez [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Pour plus d’informations, consultez [Insights sur les images](./image-insights.md).

## <a name="filtering-images"></a>Filtrage des images

 Par défaut, l’API Recherche d’images retourne toutes les images ayant un rapport avec la requête. Mais si vous souhaitez que Bing retourne uniquement des images avec un arrière-plan transparent ou des images d’une taille spécifique, utilisez les paramètres de requête suivants pour filtrer les images.  

- [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) : filtre les images selon leurs proportions (par exemple, standard ou grand écran).
- [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) : filtre les images selon leur couleur (couleur dominante ou noir et blanc).
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) : filtre les images selon leur ancienneté (par exemple, celles découvertes par Bing la semaine dernière).
- [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) : filtre les images selon leur largeur et leur hauteur.
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) : filtre les images selon leur contenu (par exemple, celles qui montrent uniquement le visage d’une personne).
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) : filtre les images selon leur type (images clipart, images GIF animées, arrière-plans transparents, etc.).
- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) : filtre les images selon le type de contrat de licence associé au site.
- [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) : filtre les images selon leur taille (par exemple, petites images jusqu’à 200 x 200 pixels).

Pour obtenir des images à partir d’un domaine spécifique, utilisez l’opérateur de requête [site:](http://msdn.microsoft.com/library/ff795613.aspx).

> [!NOTE]
> En fonction de la requête, si vous utilisez l’opérateur `site:`, il est possible que la réponse présente du contenu pour adultes, et ce quel que soit le paramètre [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) défini. Utilisez `site:` uniquement si vous connaissez le contenu du site et si votre scénario prend en charge le contenu pour adultes.

L’exemple suivant montre comment obtenir les petites images découvertes par Bing la semaine dernière sur le site ContosoSailing.com.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>Création d’un tableau croisé dynamique à partir de la requête

Si Bing peut segmenter la requête de recherche d’origine, l’objet [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) contient le champ `pivotSuggestions`. Par exemple, Bing peut segmenter la requête d’origine *Microsoft Surface* en *Microsoft* et *Surface*.  

L’exemple suivant montre les suggestions de tableau croisé dynamique pour *Microsoft Surface*.  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Vous pouvez ensuite présenter à l’utilisateur les termes optionnels de la requête si ceux-ci peuvent lui être utiles.

Le champ `pivotSuggestions` contient la liste des segments (tableaux croisés dynamiques) composant la requête d’origine. Pour chaque tableau croisé dynamique, la réponse contient une liste d’objets [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) contenant les requêtes suggérées. Le champ `text` contient la requête suggérée, et `displayText` le terme qui remplace le tableau croisé dynamique dans la requête d’origine. Il peut s’agir par exemple de la date de lancement de Surface.

Vous pouvez utiliser les champs `text` et `thumbnail` pour présenter les chaînes de requête du tableau croisé dynamique à l’utilisateur si c’est vraiment ce qu’il recherche. Pour rendre la miniature et le texte interactifs, utilisez l’URL `webSearchUrl` ou l’URL `searchLink`. Utilisez `webSearchUrl` pour envoyer à l’utilisateur les résultats de la recherche Bing, ou `searchLink` si vous fournissez votre propre page de résultats.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>Développement de la requête

Si Bing peut développer la requête pour affiner la recherche d’origine, l’objet [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) contient le champ `queryExpansions`. Par exemple, avec *Microsoft Surface* comme requête d’origine, les requêtes développées peuvent être : Microsoft Surface **Pro 3**, Microsoft Surface **RT**, Microsoft Surface **Phone** et Microsoft Surface **Hub**.

L’exemple suivant montre les requêtes développées pour *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Le champ `queryExpansions` contient une liste d’objets [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj). Le champ `text` contient la requête développée, et `displayText` le terme de développement. Vous pouvez utiliser les champs `text` et `thumbnail` pour présenter les chaînes des requêtes développées à l’utilisateur si c’est vraiment ce qu’il recherche. Pour rendre la miniature et le texte interactifs, utilisez l’URL `webSearchUrl` ou l’URL `searchLink`. Utilisez `webSearchUrl` pour envoyer à l’utilisateur les résultats de la recherche Bing, ou `searchLink` si vous fournissez votre propre page de résultats.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->

## <a name="throttling-requests"></a>Demandes de limitation

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour configurer rapidement votre première demande, consultez [le guide de démarrage rapide C#](quickstarts/csharp.md).

Familiarisez-vous avec les informations de référence sur [l’API Recherche d’images Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference). Vous y trouverez la liste des points de terminaison, en-têtes et paramètres de requête à utiliser pour demander les résultats de la recherche. Vous y trouverez également les définitions des objets de réponse.

Pour améliorer l’expérience utilisateur dans la zone de recherche, consultez [API Suggestion automatique Bing](../bing-autosuggest/get-suggested-search-terms.md). À mesure que l’utilisateur entre son terme de requête, vous pouvez appeler cette API pour obtenir des termes pertinents utilisés par d’autres personnes.

Veillez à lire les [exigences relatives à l’affichage et à l’utilisation des API Bing](./useanddisplayrequirements.md) pour respecter les règles d’utilisation des résultats de la recherche.

Quand vous appelez l’API Recherche d’images Bing, Bing retourne une liste de résultats. La liste est un sous-ensemble du nombre total de résultats en rapport avec la requête. Le champ `totalEstimatedMatches` de la réponse contient une estimation du nombre d’images disponibles pour visualisation. Pour plus d’informations sur la navigation dans les images restantes, consultez [Navigation dans les images](./paging-images.md).