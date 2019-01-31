---
title: Pagination des vidéos disponibles - Recherche de vidéos Bing
titlesuffix: Azure Cognitive Services
description: Découvrez comment paginer toutes les vidéos renvoyées par Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 26e706b054653b8f0ad1ea14d0a9c2ca97cd227f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181790"
---
# <a name="paging-videos"></a>Pagination de vidéos

Lorsque vous appelez l’API Recherche de vidéos Bing, Bing retourne une liste de résultats. La liste est un sous-ensemble du nombre total de résultats en rapport avec la requête. Pour obtenir une estimation du nombre total de résultats disponibles, accédez au champ [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) de l’objet de la réponse.  
  
L’exemple suivant illustre le champ `totalEstimatedMatches` qui est inclus dans une réponse de vidéos.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Pour paginer les vidéos disponibles, utilisez les paramètres de requête [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) et [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset).  
  
Le paramètre `count` spécifie le nombre de résultats à retourner dans la réponse. Vous pouvez demander jusqu’à 105 résultats dans la réponse. La valeur par défaut est 35. Le nombre réel retourné peut être inférieur à ce que vous avez demandé.

Le paramètre `offset` spécifie le nombre de résultats à ignorer. Le paramètre `offset` est basé sur zéro et doit être inférieur à (`totalEstimatedMatches` - `count`).  
  
Si vous souhaitez afficher 20 vidéos par page, vous devez définir `count` sur 20 et `offset` sur 0 pour obtenir la première page de résultats. Pour chaque page suivante, vous devez incrémenter le paramètre `offset` de 20 (par exemple, 20, 40).  

L’exemple suivant illustre une requête de 20 vidéos, avec un paramètre offset défini sur 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Si la valeur par défaut `count` est adaptée à votre implémentation, vous devez uniquement spécifier le paramètre de requête `offset`.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

En règle générale, si vous paginez 35 vidéos à la fois, vous devez définir le paramètre de requête `offset` sur 0 pour la première requête, puis incrémenter `offset` de 35 pour chaque requête ultérieure. Toutefois, certains résultats de la réponse suivante peuvent être des doublons de la réponse précédente. Par exemple, les deux premières vidéos de la réponse peuvent être les mêmes que les deux dernières vidéos de la réponse précédente.

Pour éliminer les doublons, utilisez le champ [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) de l’objet `Videos`.

Par exemple, si vous souhaitez paginer 30 vidéos à la fois, vous devez définir `count` sur 30 et `offset` sur 0 dans la première requête. Dans la requête suivante, vous devez définir le paramètre de requête `offset` sur la valeur `nextOffset`.


> [!NOTE]
> La pagination s’applique uniquement aux recherches de vidéos (/videos/search). Elle ne s’applique pas aux informations sur les vidéos (/videos/details) ni aux vidéos populaires (/videos/trending).

> [!NOTE]
> Le champ `TotalEstimatedAnswers` est une estimation du nombre total de résultats de recherche que vous pouvez obtenir pour la requête actuelle.  Quand vous définissez les paramètres `count` et `offset`, le nombre `TotalEstimatedAnswers` peut changer. 
