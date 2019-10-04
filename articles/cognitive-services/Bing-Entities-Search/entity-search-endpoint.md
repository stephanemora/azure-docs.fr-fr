---
title: Point de terminaison de l’API Recherche d’entités Bing
titleSuffix: Azure Cognitive Services
description: Apprenez-en davantage sur le point de terminaison de l’API Recherche d’entités Bing et envoyez-lui des requêtes.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9d08091d0ea6869d13e294e60454f85a84f672ad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424055"
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
