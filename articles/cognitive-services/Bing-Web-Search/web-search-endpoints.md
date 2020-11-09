---
title: Point de terminaison Recherche Web
titleSuffix: Azure Cognitive Services
description: Pour obtenir des résultats de recherche web, envoyez une requête `GET` au point de terminaison suivant. Les en-têtes et les paramètres d’URL définissent d’autres spécifications.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 25ecd1a753cb9a401408f7ed6605d53e5310df2b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075668"
---
# <a name="web-search-endpoint"></a>Point de terminaison Recherche Web

> [!WARNING]
> Les API Recherche Bing passent de Cognitive Services aux services de recherche Bing. À compter du **30 octobre 2020** , toutes les nouvelles instances de Recherche Bing doivent être provisionnées en suivant le processus documenté [ici](https://aka.ms/cogsvcs/bingmove).
> Les API Recherche Bing provisionnées à l’aide de Cognitive Services seront prises en charge les trois prochaines années ou jusqu’à la fin de votre Accord Entreprise, selon la première éventualité.
> Pour obtenir des instructions de migration, consultez [Services de recherche Bing](https://aka.ms/cogsvcs/bingmigration).

**L’API Recherche Web** renvoie des pages, des images, des vidéos et des [entités](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) web. Les entités disposent d’informations résumées sur une personne, un lieu ou un sujet.

## <a name="endpoint"></a>Point de terminaison

Pour obtenir des résultats de recherche web à l’aide de l’API Bing, envoyez une requête `GET` au point de terminaison suivant. Les en-têtes et les paramètres d’URL définissent d’autres spécifications.

**Point de terminaison :** renvoie des résultats de recherche web qui sont pertinents dans le cadre de la requête de recherche de l’utilisateur définie par `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Point de terminaison : Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, et plus encore, consultez la référence sur l’[API Recherche Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

## <a name="response-json"></a>Réponse JSON

La réponse à une requête de recherche web intègre tous les résultats sous forme d’objets JSON. Analyser les résultats nécessite des procédures qui traitent les éléments de chaque type. Consultez le [didacticiel](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) et le [code source](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) pour obtenir des exemples.

## <a name="next-steps"></a>Étapes suivantes

Les API **Bing** prennent en charge les actions de recherche qui renvoient des résultats en fonction de leur type.  Tous les points de terminaison de recherche renvoient des résultats en tant qu’objets de réponse JSON.  Tous les points de terminaison prennent en charge les requêtes qui retournent une langue et un emplacement spécifiques par longitude, latitude et rayon de recherche.

Pour plus d’informations sur les paramètres pris en charge par chaque point de terminaison, consultez les pages de référence pour chaque type.
Pour obtenir des exemples de requêtes de base à l’aide de l’API Recherche Web, consultez les [ démarrages rapides sur la recherche web](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
