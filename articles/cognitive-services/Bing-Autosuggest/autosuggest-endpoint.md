---
title: Point de terminaison Suggestion automatique Bing
titlesuffix: Azure Cognitive Services
description: Résumé du point de terminaison d’API Suggestion automatique Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 03f7ac11b08d9cad633e207337ff963114f2c68f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149235"
---
# <a name="bing-autosuggest-endpoint"></a>Point de terminaison Suggestion automatique Bing

L'**API Suggestion automatique Bing** inclut un point de terminaison, qui renvoie une liste de requêtes suggérées à partir d’un terme de recherche partielle.

## <a name="endpoint"></a>Point de terminaison

Pour obtenir des suggestions de requête à l’aide de l’API Bing, envoyez une requête `GET` au point de terminaison suivant. Utilisez les en-têtes et les paramètres d’URL pour définir d’autres spécifications.

**Point de terminaison :** Retourne les suggestions de recherche en tant que résultats JSON qui correspondent à l’entrée utilisateur définie par `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, etc., consultez la référence [API Suggestion automatique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference).

## <a name="response-json"></a>Réponse JSON

La réponse à une requête de suggestion automatique intègre les résultats sous forme d’objets JSON. Pour obtenir des exemples d’analyse des résultats, consultez le [didacticiel](tutorials/autosuggest.md) et le [code source](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Étapes suivantes

Les API **Bing** prennent en charge les actions de recherche qui renvoient des résultats en fonction de leur type. Tous les points de terminaison de recherche renvoient des résultats en tant qu’objets de réponse JSON.
Tous les points de terminaison prennent en charge les requêtes qui renvoient une langue et/ou un emplacement spécifique par longitude, latitude et rayon de recherche.

Pour plus d’informations sur les paramètres pris en charge par chaque point de terminaison, consultez les pages de référence pour chaque type.
Pour obtenir des exemples de requêtes de base à l’aide de l’API Suggestion automatique, consultez les [démarrages rapides de suggestion automatique](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).
