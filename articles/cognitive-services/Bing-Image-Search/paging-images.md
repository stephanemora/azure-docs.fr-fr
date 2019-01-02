---
title: Parcourir les images retournées par l’API Recherche d’images Bing
titleSuffix: Azure Cognitive Services
description: Parcourez les différentes pages d’images retournées par l’API Recherche d’images Bing.
services: cognitive-services
author: swhite-msft
manager: cgonlun
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 69829066e543e5ab3badc4d1d3f77ff3c1d16ffd
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259747"
---
# <a name="page-through-the-images-results"></a>Parcourir les images retournées dans les résultats

Quand vous appelez l’API Recherche d’images, Bing retourne une liste de résultats. La liste est un sous-ensemble du nombre total de résultats en rapport avec la requête. Pour obtenir une estimation du nombre total de résultats disponibles, accédez au champ [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) de l’objet de la réponse.  

L’exemple suivant illustre le champ `totalEstimatedMatches` qui est inclus dans une réponse d’images.  

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  

Pour paginer les images disponibles, utilisez les paramètres de requête [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) et [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset).  

Le paramètre `count` spécifie le nombre de résultats à renvoyer dans la réponse. Vous pouvez demander jusqu’à 150 résultats dans la réponse. La valeur par défaut est 35. Le nombre réel retourné peut être inférieur à ce que vous avez demandé.

Le paramètre `offset` spécifie le nombre de résultats à ignorer. Le paramètre `offset` est basé sur zéro et doit être inférieur à (`totalEstimatedMatches` - `count`).  

Si vous souhaitez afficher 20 images par page, vous devez définir `count` sur 20 et `offset` sur 0 pour obtenir la première page de résultats. L’exemple suivant illustre une requête de 20 images, avec un paramètre offset défini sur 40.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Si la valeur par défaut `count` est adaptée à votre implémentation, vous devez uniquement spécifier le paramètre de requête `offset`.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

En règle générale, si vous paginez 35 images à la fois, vous devez définir le paramètre de requête `offset` sur 0 pour la première requête, puis incrémenter `offset` de 35 pour chaque requête ultérieure. Toutefois, certains résultats de la réponse suivante peuvent être des doublons de la réponse précédente. Par exemple, les deux premières images de la réponse peuvent être les mêmes que les deux dernières images de la réponse précédente.

Pour éliminer les doublons, utilisez le champ [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) de l’objet `Images`. Le champ `nextOffset` indique le paramètre `offset` à utiliser pour la requête suivante. Par exemple, si vous souhaitez paginer 30 images à la fois, vous devez définir `count` sur 30 et `offset` sur 0 dans la première requête. Dans la requête suivante, vous devez définir `count` sur 30 et `offset` sur la valeur du paramètre `nextOffset` de la réponse précédente. Pour paginer en sens inverse, nous vous suggérons de maintenir une pile des décalages précédents et de dépiler les plus récents.

> [!NOTE]
> La pagination s’applique uniquement à la recherche d’images (/images/search), mais pas aux informations d’images ni aux images populaires (/images/trending).

> [!NOTE]
> Le champ `TotalEstimatedAnswers` est une estimation du nombre total de résultats de recherche que vous pouvez obtenir pour la requête actuelle.  Quand vous définissez les paramètres `count` et `offset`, le nombre `TotalEstimatedAnswers` peut changer. 
