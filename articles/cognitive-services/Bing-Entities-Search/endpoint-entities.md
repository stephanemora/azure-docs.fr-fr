---
title: Point de terminaison Recherche d’entités Bing
titlesuffix: Azure Cognitive Services
description: Synthèse du point de terminaison de l’API Recherche d’entités Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: d781a4b3cd0119f5624b4dd20b514894ea339414
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816580"
---
# <a name="entity-search-endpoint"></a>Point de terminaison Recherche d’entités
L’**API Recherche d’entités** inclut un point de terminaison qui renvoie des entités du Web en fonction d’une requête.

## <a name="endpoint"></a>Point de terminaison
Pour obtenir des résultats d’entités à l’aide de l’**API Bing**, envoyez une requête `GET` au point de terminaison suivant. Utilisez les en-têtes et les paramètres d’URL pour définir d’autres spécifications.

**Point de terminaison :** renvoie des entités qui sont pertinentes dans le cadre de la requête de recherche de l’utilisateur définie par `?q=""`.
```
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, etc., consultez la référence [API Recherche d’entités Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference).

## <a name="response-json"></a>Réponse JSON
La réponse à une requête de recherche d’entités intègre les résultats sous forme d’objets JSON. Pour obtenir des exemples de résultats, consultez le [guide de prise en main](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start).

## <a name="next-steps"></a>Étapes suivantes
Les API **Bing** prennent en charge les actions de recherche qui renvoient des résultats en fonction de leur type. Tous les points de terminaison de recherche renvoient des résultats en tant qu’objets de réponse JSON.  Tous les points de terminaison prennent en charge les requêtes qui renvoient une langue et/ou un emplacement spécifique par longitude, latitude et rayon de recherche.

Pour plus d’informations sur les paramètres pris en charge par chaque point de terminaison, consultez les pages de référence pour chaque type.
Pour obtenir des exemples d’utilisation de l’API Recherche d’entités, consultez le [guide de prise en main](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/quick-start) et [Redimensionner et rogner les miniatures](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/resize-and-crop-thumbnails).