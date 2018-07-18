---
title: Point de terminaison Suggestion automatique | Microsoft Docs
description: Résumé du point de terminaison d’API Suggestion automatique.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 5aaddd09006cb6f1812bb6ae213a2f5e6720fb1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369916"
---
# <a name="autosuggest-endpoint"></a>Point de terminaison Suggestion automatique

**L’API Suggestion automatique** inclut un point de terminaison, qui retourne une liste de requêtes suggérées à partir d’un terme de recherche partielle.

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