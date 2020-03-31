---
title: Points de terminaison pour l’API Recherche d’images Bing
titleSuffix: Azure Cognitive Services
description: L’API Recherche d’images inclut trois points de terminaison. Le point de terminaison 1 retourne des images à partir du web. Le point de terminaison 2 renvoie les informations ImageInsights. Le point de terminaison 3 renvoie des images populaires.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072624"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Points de terminaison pour l’API Recherche d’images Bing

L’**API Recherche d’images** inclut trois points de terminaison.  Le point de terminaison 1 renvoie des images du Web grâce à une requête. Le point de terminaison 2 renvoie les informations [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Le point de terminaison 3 renvoie des images populaires.

## <a name="endpoints"></a>Points de terminaison

Pour obtenir des résultats d’images à l’aide de l’API Bing, envoyez une requête à l’un des points de terminaison suivants. Utilisez les en-têtes et les paramètres d’URL pour définir d’autres spécifications.

**Point de terminaison 1 :** retourne des images qui sont pertinentes dans le cadre de la requête de recherche de l’utilisateur définie par `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Point de terminaison 2 :** retourne des informations à propos d’une image, à l’aide des requêtes `GET` ou `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Une requête GET renvoie des informations à propos d’une image, comme les pages Web où figure l’image. Intégrez le paramètre [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) avec une requête `GET`.

Vous pouvez aussi inclure une image binaire dans le corps d’une requête `POST` et définir le paramètre de [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) sur `RecognizedEntities`. Cette opération renvoie un jeton [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) à utiliser en tant que paramètre dans une requête `GET` ultérieure afin de récupérer des informations sur les personnes de l’image.  Définissez `modules` sur `All` pour obtenir toutes les informations, hormis `RecognizedEntities` dans les résultats de la requête `POST`, sans avoir à effectuer d’autres appels via `insightsToken`.


**Point de terminaison 3 :** retourne des images qui sont populaires en fonction des requêtes de recherche effectuées par d’autres utilisateurs. Les images sont séparées en différentes catégories, par exemple par personnes célèbres ou événements.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Pour obtenir la liste des marchés sur lesquels les images populaires sont prises en charge, consultez [Images populaires](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, etc., consultez la référence [Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) (API Recherche d’images Bing v7).
## <a name="response-json"></a>Réponse JSON
La réponse à une requête de recherche d’images intègre les résultats sous forme d’objets JSON. Pour obtenir des exemples d’analyse des résultats, consultez le [didacticiel](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) et le [code source](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Étapes suivantes
Les API **Bing** prennent en charge les actions de recherche qui renvoient des résultats en fonction de leur type. Tous les points de terminaison de recherche renvoient des résultats en tant qu’objets de réponse JSON.  Tous les points de terminaison prennent en charge les requêtes qui renvoient une langue et/ou un emplacement spécifique par longitude, latitude et rayon de recherche.

Pour plus d’informations sur les paramètres pris en charge par chaque point de terminaison, consultez les pages de référence pour chaque type.
Pour obtenir des exemples de requêtes de base à l’aide de l’API Recherche d’images, consultez les [guides de démarrage rapide sur la recherche d’images](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
