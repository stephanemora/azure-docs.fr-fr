---
title: Envoi de requêtes à l’API Recherche d’images Bing | Microsoft Docs
description: Découvrez comment envoyer et personnaliser des requêtes de recherche envoyées à l’API Recherche d’images Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: d74f59ffcf095e639686a3ada3b09dac988fc544
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082577"
---
# <a name="sending-queries-to-the-bing-image-search-api"></a>Envoi de requêtes à l’API Recherche d’images Bing

L’API Recherche d’images Bing offre une expérience similaire à celle de Bing.com/Images, en ce sens qu’elle permet d’envoyer une requête de recherche à Bing pour obtenir une liste d’images pertinentes.

## <a name="using-and-suggesting-search-terms"></a>Utilisation et suggestion de termes de recherche

Lorsqu’un terme de recherche est entré, encodez-le par URL avant de définir le paramètre de requête [**q**](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query). Par exemple, si un utilisateur entre *sailing dinghies*, définissez `q` avec la valeur `sailing+dinghies` ou `sailing%20dinghies`.

Si votre application comprend une zone de recherche où les termes de recherche sont entrés, vous pouvez utiliser l’[API Suggestion automatique Bing](../../bing-autosuggest/get-suggested-search-terms.md) pour améliorer l’expérience en affichant des suggestions de termes en temps réel. L’API suggère des chaînes de requête basés sur des termes de recherche partiels et Azure Cognitive Services.

## <a name="pivoting-the-query"></a>Création d’un tableau croisé dynamique à partir de la requête

Si Bing peut segmenter la requête de recherche d’origine, l’objet [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) retourné contiendra `pivotSuggestions`, qui peut être présenté à l’utilisateur comme des termes de recherche facultatifs. Par exemple, Bing peut segmenter la requête d’origine *Microsoft Surface* en *Microsoft* et *Surface* et suggérer des tableaux croisés dynamiques pour chacun. Ceux-ci peuvent être présentés à l’utilisateur comme des termes de requête facultatifs.

L’exemple suivant montre les suggestions de tableau croisé dynamique pour *Microsoft Surface* :  

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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous n’avez pas déjà essayé l’API Recherche d’images Bing, essayez un [démarrage rapide](../quickstarts/csharp.md). Si vous recherchez quelque chose de plus complexe, suivez le didacticiel de création d’une [application web à une seule page](../tutorial-bing-image-search-single-page-app.md).
