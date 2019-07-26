---
title: Pagination des vidéos disponibles - Recherche de vidéos Bing
titleSuffix: Azure Cognitive Services
description: Découvrez comment paginer toutes les vidéos retournées par l’API Recherche de vidéos Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: c3570d8772734595c6707ca8103006867a8eb47a
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500722"
---
# <a name="paging-through-video-search-results"></a>Pagination des résultats de recherche de vidéos

L’API Recherche de vidéos Bing retourne une partie de tous les résultats de recherche trouvés par votre requête. En paginant ces résultats avec des appels ultérieurs à l’API, vous pouvez les obtenir et les afficher dans votre application.

> [!NOTE]
> La pagination s’applique uniquement aux recherches de vidéos (/videos/search). Elle ne s’applique pas aux informations sur les vidéos (/videos/details) ni aux vidéos populaires (/videos/trending).

## <a name="total-estimated-matches"></a>Estimation du nombre total de correspondances

Pour obtenir l’estimation du nombre de résultats de recherche trouvés, utilisez le champ [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) dans la réponse JSON.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Pagination de vidéos

Pour paginer les vidéos disponibles, utilisez les paramètres de requête [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) et [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) au moment d’envoyer votre requête.  
  

|Paramètre  |Description  |
|---------|---------|
|`count`     | Spécifie le nombre de résultats à renvoyer dans la réponse. Vous pouvez demander jusqu’à 100 résultats dans la réponse. La valeur par défaut est de 10. Le nombre réel renvoyé peut être inférieur à ce que vous avez demandé.        |
|`offset`     | Spécifie le nombre de résultats à ignorer. Le paramètre `offset` est basé sur zéro et doit être inférieur à (`totalEstimatedMatches` - `count`).          |

Par exemple, si vous souhaitez afficher 20 articles par page, vous devez définir `count` sur 20 et `offset` sur 0 pour obtenir la première page de résultats. Pour chaque page suivante, vous devez incrémenter le paramètre `offset` de 20 (par exemple, 20, 40).  
  
L’exemple suivant illustre une requête de 20 vidéos avec un paramètre offset de 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Si vous utilisez la valeur par défaut pour le paramètre [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count), vous devez uniquement spécifier le paramètre de requête `offset`, comme dans l’exemple suivant.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Si vous paginez 35 vidéos à la fois, vous devez définir le paramètre de requête `offset` avec la valeur 0 pour la première requête, puis incrémenter `offset` de 35 pour chaque requête ultérieure. Toutefois, deux réponses successives peuvent contenir des résultats en double. Par exemple, les deux premières vidéos d’une réponse peuvent être les mêmes que les deux dernières vidéos de la réponse précédente.

Pour éliminer les doublons, utilisez le champ [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset) de l’objet `Videos`.

Par exemple, si vous souhaitez paginer 30 vidéos à la fois, vous devez définir `count` avec la valeur 30 et `offset` avec la valeur 0 dans la première requête. Dans la requête suivante, vous devez définir le paramètre de requête `offset` avec la valeur `nextOffset`.

> [!NOTE]
> Le champ `TotalEstimatedAnswers` est une estimation du nombre total de résultats de recherche que vous pouvez obtenir pour la requête actuelle.  Quand vous définissez les paramètres `count` et `offset`, le nombre `TotalEstimatedAnswers` peut changer. 
  
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir des insights sur les vidéos](video-insights.md)
