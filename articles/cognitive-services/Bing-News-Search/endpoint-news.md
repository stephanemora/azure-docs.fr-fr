---
title: Points de terminaison Recherche d’actualités Bing | Microsoft Docs
description: Synthèse sur le point de terminaison de l’API Recherche d’actualités.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: ab892e947566adf025499382b213a52ed3e96e35
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433682"
---
# <a name="news-search-endpoints"></a>Points de terminaison Recherche d’actualités
**L’API Recherche d’actualités** renvoie des articles, des pages web, des images, des vidéos et des [entités](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) en lien avec des actualités. Les entités contiennent un résumé d’informations sur une personne, un lieu ou un sujet.
## <a name="endpoints"></a>Points de terminaison
Pour obtenir des résultats de recherche d'actualités à l’aide de l’API Bing, envoyez une requête `GET` à l’un des points de terminaison suivants. Les en-têtes et les paramètres d’URL définissent d’autres spécifications.

**Point de terminaison 1 :** renvoie les éléments d’actualités en fonction de la requête de recherche de l’utilisateur. Si la requête de recherche est vide, l’appel renvoie les articles en lien avec des actualités. L’option `?q=""` de requête peut aussi être utilisée avec l’URL `/news`. Pour la disponibilité, consultez [Supported markets for news search endpoint](language-support.md#supported-markets-for-news-search-endpoint) (Marchés pris en charge pour le point de terminaison Recherche d’actualités).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

**Point de terminaison 2 :** renvoie les meilleurs articles en lien avec des actualités par catégorie. Vous pouvez demander spécifiquement les meilleurs articles en lien avec l’économie, le sport ou le divertissement à l’aide de `category=business`, `category=sports` ou `category=entertainment`.  Le paramètre `category` peut uniquement être utilisé avec l’URL `/news`. Certaines conditions s’appliquent pour spécifier des catégories ; reportez-vous à `category` dans la documentation sur le [paramètre de requête](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters). Pour la disponibilité, consultez [Supported markets for news search endpoint](language-support.md#supported-markets-for-news-endpoint) (Marchés pris en charge pour le point de terminaison Recherche d’actualités).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**Point de terminaison 3 :** renvoie des sujets en lien avec des actualités qui sont actuellement tendance sur les réseaux sociaux. Lorsque l’option `/trendingtopics` est incluse, la recherche Bing ignore plusieurs autres paramètres, tels que `freshness` et `?q=""`. Pour la disponibilité, consultez [Supported markets for news search endpoint](language-support.md#supported-markets-for-news-trending-endpoint) (Marchés pris en charge pour le point de terminaison Recherche d’actualités).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, etc., consultez [News Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) (Référence sur l’API Recherche d’actualités Bing v7).
## <a name="response-json"></a>Réponse JSON
La réponse à une requête de recherche d’actualités intègre les résultats sous forme d’objets JSON. Analyser les résultats nécessite des procédures qui traitent des éléments de chaque type. Consultez le [didacticiel](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) et le [code source](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source) pour obtenir des exemples.

## <a name="next-steps"></a>Étapes suivantes
Les API **Bing** prennent en charge les actions de recherche qui renvoient des résultats en fonction de leur type. Tous les points de terminaison de recherche renvoient des résultats en tant qu’objets de réponse JSON.  Tous les points de terminaison prennent en charge les requêtes qui renvoient une langue et/ou un emplacement spécifique par longitude, latitude et rayon de recherche.

Pour plus d’informations sur les paramètres pris en charge par chaque point de terminaison, consultez les pages de référence pour chaque type.
Pour obtenir des exemples de requêtes de base à l’aide de l’API Recherche d’actualités, consultez les [démarrages rapides sur l’API Recherche d'actualités Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
