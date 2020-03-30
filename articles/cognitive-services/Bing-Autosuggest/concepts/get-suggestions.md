---
title: Suggestion de termes de recherche avec l’API Suggestion automatique Bing
titleSuffix: Azure Cognitive Services
description: Cet article décrit le concept de suggérer des termes de requête à l’aide de l’API Suggestion automatique Bing et l’impact de la longueur d’une requête sur la pertinence.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 060dbd29ee4ddb78e8ae9b2ed4e7814da3c4eebf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072885"
---
# <a name="suggesting-query-terms"></a>Suggestion de termes de requête

En général, vous appelez l’API Suggestion automatique Bing chaque fois qu’un utilisateur tape un nouveau caractère dans la zone de recherche de votre application. L’exhaustivité de la chaîne de requête conditionne la pertinence des termes suggérés par l’API. Plus la chaîne de requête est complète, plus la liste de termes suggérés est pertinente. Par exemple, les suggestions que l’API peut retourner pour `s` sont probablement moins pertinentes que celles retournées pour `sailing dinghies` (dériveurs).

## <a name="example-request"></a>Exemple de requête

L’exemple suivant illustre une requête qui renvoie les chaînes de requête suggérées pour *sail* (voile). N’oubliez pas d’encoder en URL le terme de requête partiel de l’utilisateur quand vous définissez le paramètre de requête [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query). Par exemple, si l’utilisateur a tapé *sailing les*, affectez la valeur `q` ou `sailing+les` à `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

La réponse suivante contient une liste d’objets [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) contenant les termes de requête suggérés.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Utilisation des termes de requête suggérés

Chaque suggestion comporte les champs `displayText`, `query` et `url`. Le champ `displayText` correspond à la requête suggérée que vous utilisez pour remplir la liste déroulante de la zone de recherche. Vous devez afficher toutes les suggestions incluses dans la réponse, dans l’ordre indiqué.

L’exemple suivant montre une zone déroulante de recherche avec les termes de requête suggérés provenant de l’API Suggestion automatique Bing.

![Liste de zone de recherche déroulante Suggestion automatique](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Si l’utilisateur sélectionnait une suggestion de requête dans la liste déroulante, vous utiliseriez la chaîne de requête du champ `query` pour appeler l’[API Recherche Web Bing](../../bing-web-search/search-the-web.md) et afficher vous-même les résultats. Vous pourriez aussi utiliser l’URL du champ `url` pour rediriger plutôt l’utilisateur vers la page des résultats de la recherche Bing.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de l’API Suggestion automatique Bing](../get-suggested-search-terms.md)