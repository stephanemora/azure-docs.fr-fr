---
title: Point de terminaison Suggestion automatique Bing
titlesuffix: Azure Cognitive Services
description: Résumé du point de terminaison d’API Suggestion automatique Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: c2d1c97ad2af266558f9b664162526d5006d2092
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830442"
---
# <a name="bing-autosuggest-endpoint"></a>Point de terminaison Suggestion automatique Bing

L'**API Suggestion automatique Bing** inclut un point de terminaison, qui renvoie une liste de requêtes suggérées à partir d’un terme de recherche partielle.

## <a name="endpoint"></a>Point de terminaison

Pour obtenir des suggestions de requête à l’aide de l’API Bing, envoyez une requête `GET` au point de terminaison suivant. Utilisez les en-têtes et les paramètres d’URL pour définir d’autres spécifications.

**Point de terminaison :** renvoie les suggestions de recherche en tant que résultats JSON qui correspondent à l’entrée utilisateur définie par `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, etc., consultez la référence [API Suggestion automatique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference).

## <a name="response-json"></a>Réponse JSON

La réponse à une requête de suggestion automatique intègre les résultats sous forme d’objets JSON. Pour obtenir des exemples d’analyse des résultats, consultez le [didacticiel](tutorials/autosuggest.md) et le [code source](tutorials/autosuggest-source.md).

## <a name="next-steps"></a>Étapes suivantes

Les API **Bing** prennent en charge les actions de recherche qui renvoient des résultats en fonction de leur type. Tous les points de terminaison de recherche renvoient des résultats en tant qu’objets de réponse JSON.
Tous les points de terminaison prennent en charge les requêtes qui renvoient une langue et/ou un emplacement spécifique par longitude, latitude et rayon de recherche.

Pour plus d’informations sur les paramètres pris en charge par chaque point de terminaison, consultez les pages de référence pour chaque type.
Pour obtenir des exemples de requêtes de base à l’aide de l’API Suggestion automatique, consultez les [démarrages rapides de suggestion automatique](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).