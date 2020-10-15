---
title: Point de terminaison de l’API Recherche d’entités Bing
titleSuffix: Azure Cognitive Services
description: L’API Recherche d’entités Bing possède un point de terminaison qui renvoie des entités du Web en fonction d’une requête. Ces résultats de recherche sont retournés au format JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74072668"
---
# <a name="bing-entity-search-api-endpoint"></a>Point de terminaison de l’API Recherche d’entités Bing


L’API Recherche d’entités Bing possède un point de terminaison qui renvoie des entités du Web en fonction d’une requête. Ces résultats de recherche sont retournés au format JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Obtenir des résultats d’entité du point de terminaison

Pour obtenir des résultats d’entités à l’aide de l’**API Bing**, envoyez une requête `GET` au point de terminaison suivant. Utilisez les [en-têtes](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) et les [ paramètres de requête](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) pour personnaliser votre requête de recherche. Les requêtes de recherche peuvent être envoyées à l’aide du paramètre `?q=`.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Qu’est-ce que l’API Recherche d’entités Bing ?](overview.md)

## <a name="see-also"></a>Voir aussi 

Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, entre autres, voir l’article de référence sur l’[API Recherche d’entités Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
