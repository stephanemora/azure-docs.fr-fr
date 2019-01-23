---
title: Parcourir les résultats de la recherche d’actualités Bing
titlesuffix: Azure Cognitive Services
description: Découvrez comment parcourir les articles d’actualités renvoyés par l'API Recherche d'actualités Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 9acf0c26707dab9de443b06e7314de6d77913777
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258252"
---
# <a name="how-to-page-through-news-search-results"></a>Parcourir les résultats de la recherche d’actualités

Lorsque vous appelez l’API Recherche d’actualités Bing, Bing renvoie une liste de résultats pertinents pour votre requête. Pour obtenir une estimation du nombre total de résultats disponibles, accédez au champ [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) de l’objet de la réponse.  
  
L’exemple suivant illustre le champ `totalEstimatedMatches` qui est inclus dans une réponse d’actualités.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Pour paginer les articles disponibles, utilisez les paramètres de requête [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) et [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset).  
 

|Paramètre  |Description  |
|---------|---------|
|`count`     | Spécifie le nombre de résultats à renvoyer dans la réponse. Vous pouvez demander jusqu’à 100 résultats dans la réponse. La valeur par défaut est de 10. Le nombre réel renvoyé peut être inférieur à ce que vous avez demandé.        |
|`offset`     | Spécifie le nombre de résultats à ignorer. Le paramètre `offset` est basé sur zéro et doit être inférieur à (`totalEstimatedMatches` - `count`).          |

Par exemple, si vous souhaitez afficher 20 articles par page, vous devez définir `count` sur 20 et `offset` sur 0 pour obtenir la première page de résultats. Pour chaque page suivante, vous devez incrémenter le paramètre `offset` de 20 (par exemple, 20, 40).  
  
L’exemple suivant illustre une requête de 20 articles avec un paramètre offset de défini sur 40.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Si la valeur `count` par défaut est adaptée à votre implémentation, spécifiez uniquement le paramètre de requête `offset` comme indiqué dans l’exemple suivant :  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> La pagination s’applique uniquement à la recherche d’actualités (/news/search), mais pas aux rubriques populaires (news/trendingtopics) ni aux catégories d’actualités (/news).

> [!NOTE]
> Le champ `TotalEstimatedAnswers` est une estimation du nombre total de résultats de recherche que vous pouvez obtenir pour la requête actuelle.  Quand vous définissez les paramètres `count` et `offset`, le nombre `TotalEstimatedAnswers` peut changer. 