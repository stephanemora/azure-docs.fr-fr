---
title: Points de terminaison de recherche de vidéos | Microsoft Docs
description: Synthèse sur le point de terminaison de l’API Recherche de vidéos.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-gedod
ms.openlocfilehash: 9836d9928362ab37b0a81ff5043d99f9bf353f22
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367920"
---
# <a name="video-search-endpoints"></a>Points de terminaison Recherche de vidéos
**L’API Recherche de vidéos** inclut trois points de terminaison.  Le point de terminaison 1 renvoie des vidéos issues du web en fonction d’une requête. Le point de terminaison 2 renvoie des insights sur une vidéo basées sur le paramètre d’URL `modules`.  Le point de terminaison 3 renvoie des images populaires.

## <a name="endpoints"></a>Points de terminaison
Pour obtenir des résultats de vidéo à l’aide de l’API Bing, envoyez une requête `GET` à l’un des points de terminaison suivants. Utilisez les en-têtes et les paramètres d’URL pour définir d’autres spécifications.

**Point de terminaison 1 :** renvoie des vidéos qui sont pertinentes dans le cadre de la requête de recherche de l’utilisateur définie par `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```

**Point de terminaison 2 :** renvoie des insights sur une vidéo, par exemple des vidéos connexes. Ajoutez le [paramètre de requête](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters) `modules`, qui est une liste délimitée par des virgules des insights à demander.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details
```

**Point de terminaison 3 :** renvoie des vidéos qui sont populaires en fonction des requêtes de recherche effectuées par d’autres utilisateurs. Les vidéos sont séparées en différentes catégories, par exemple par personnes célèbres ou événements.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending
```

Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, etc., consultez [Video Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) (Référence sur l’API Recherche de vidéos v7).
## <a name="response-json"></a>Réponse JSON
La réponse à une requête de recherche de vidéos intègre les résultats sous forme d’objets JSON. Pour obtenir des exemples d’analyse des résultats, consultez le [didacticiel](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app) et le [code source](https://docs.microsoft.com/azure/cognitive-services/bing-video-search/tutorial-bing-video-search-single-page-app-source).

## <a name="next-steps"></a>Étapes suivantes
Les API **Bing** prennent en charge les actions de recherche qui renvoient des résultats en fonction de leur type. Tous les points de terminaison de recherche renvoient des résultats en tant qu’objets de réponse JSON.  Tous les points de terminaison prennent en charge les requêtes qui renvoient une langue et/ou un emplacement spécifique par longitude, latitude et rayon de recherche.

Pour plus d’informations sur les paramètres pris en charge par chaque point de terminaison, consultez les pages de référence pour chaque type.
Pour obtenir des exemples de requêtes de base à l’aide de l’API Recherche de vidéos, consultez les [démarrages rapides sur la recherche de vidéos](https://docs.microsoft.com/azure/cognitive-services/bing-video-search).