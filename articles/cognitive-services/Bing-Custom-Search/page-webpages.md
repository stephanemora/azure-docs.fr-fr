---
title: Parcourir les pages web disponibles – Recherche personnalisée Bing
titleSuffix: Azure Cognitive Services
description: Découvrez comment parcourir toutes les pages web que Recherche personnalisée Bing peut retourner.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 13b4cef624c636b8935897338badf3349f27c7f5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405046"
---
# <a name="paging-webpages"></a>Pagination des pages web 

Lorsque vous appelez l’API Recherche personnalisée, Bing renvoie une liste de résultats. La liste est un sous-ensemble du nombre total de résultats susceptibles d’être en rapport avec la requête. Pour obtenir une estimation du nombre total de résultats disponibles, accédez au champ [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#totalestimatedmatches) de l’objet de la réponse.  
  
L’exemple suivant illustre le champ `totalEstimatedMatches` qui est inclus dans une réponse web.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
Pour paginer les pages web disponibles, utilisez les paramètres de requête [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#count) et [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#offset).  
  
Le paramètre `count` spécifie le nombre de résultats à renvoyer dans la réponse. Vous pouvez demander jusqu’à 50 résultats dans la réponse. La valeur par défaut est de 10. Le nombre réel renvoyé peut être inférieur à ce que vous avez demandé.

Le paramètre `offset` spécifie le nombre de résultats à ignorer. Le paramètre `offset` est basé sur zéro et doit être inférieur à (`totalEstimatedMatches` - `count`).  
  
Si vous souhaitez afficher 15 pages web par page, vous devez définir `count` sur 15 et `offset` sur 0 pour obtenir la première page de résultats. Pour chaque page suivante, vous devez incrémenter le paramètre `offset` de 15 (par exemple, 15, 30).  
  
L’exemple suivant illustre une requête de 15 pages web, avec un paramètre offset défini sur 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Si la valeur par défaut `count` est adaptée à votre implémentation, vous devez uniquement spécifier le paramètre de requête `offset`.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Le champ `TotalEstimatedAnswers` est une estimation du nombre total de résultats de recherche que vous pouvez obtenir pour la requête actuelle.  Quand vous définissez les paramètres `count` et `offset`, le nombre `TotalEstimatedAnswers` peut changer. 

