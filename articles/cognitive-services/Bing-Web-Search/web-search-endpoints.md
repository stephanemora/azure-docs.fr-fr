---
title: Point de terminaison de recherche Web | Microsoft Docs
description: Synthèse sur le point de terminaison de l’API Recherche Web.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 72fbe1a0eb4379ad032e649f7299e37a0214adfb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367945"
---
# <a name="web-search-endpoint"></a>Point de terminaison Recherche Web
**L’API Recherche Web** renvoie des pages, des images, des vidéos et des [entités](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) web. Les entités contiennent un résumé d’informations sur une personne, un lieu ou un sujet.
## <a name="endpoint"></a>Point de terminaison
Pour obtenir des résultats de recherche web à l’aide de l’API Bing, envoyez une requête `GET` au point de terminaison suivant. Les en-têtes et les paramètres d’URL définissent d’autres spécifications.

**Point de terminaison :** renvoie des résultats de recherche web qui sont pertinents dans le cadre de la requête de recherche de l’utilisateur définie par `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, etc., consultez [Web Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) (Référence sur l’API Recherche Web v7).

## <a name="response-json"></a>Réponse JSON
La réponse à une requête de recherche web intègre tous les résultats sous forme d’objets JSON. Analyser les résultats nécessite des procédures qui traitent les éléments de chaque type. Consultez le [didacticiel](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) et le [code source](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source) pour obtenir des exemples.

## <a name="next-steps"></a>Étapes suivantes
Les API **Bing** prennent en charge les actions de recherche qui renvoient des résultats en fonction de leur type. Tous les points de terminaison de recherche renvoient des résultats en tant qu’objets de réponse JSON.  Tous les points de terminaison prennent en charge les requêtes qui renvoient une langue et/ou un emplacement spécifique par longitude, latitude et rayon de recherche.

Pour plus d’informations sur les paramètres pris en charge par chaque point de terminaison, consultez les pages de référence pour chaque type.
Pour obtenir des exemples de requêtes de base à l’aide de l’API Recherche Web, consultez les [ démarrages rapides sur la recherche web](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).