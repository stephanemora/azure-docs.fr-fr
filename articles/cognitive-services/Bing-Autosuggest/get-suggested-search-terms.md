---
title: Qu’est-ce que la Suggestion automatique Bing ?
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API Suggestion automatique Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: 18658af90bd1ff71e84a5b2332db7e30bc23f69a
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829390"
---
# <a name="what-is-bing-autosuggest"></a>Qu’est-ce que la Suggestion automatique Bing ?

Si vous envoyez des requêtes à l’une des API Recherche Bing, vous pouvez utiliser l’API Suggestion automatique Bing pour améliorer votre expérience avec la zone de recherche. L’API Suggestion automatique Bing renvoie une liste de requêtes suggérées en fonction d’une chaîne de requête partielle saisie par l’utilisateur dans la zone de recherche. Affichez les suggestions dans la liste déroulante de la zone de recherche. Les termes suggérés sont basés sur l’intention de l’utilisateur et sur des requêtes suggérées que d’autres utilisateurs ont recherchées.

En général, vous appelez cette API chaque fois que l’utilisateur tape un nouveau caractère dans la zone de recherche. L’exhaustivité de la chaîne de requête conditionne la pertinence des termes suggérés par l’API. Plus la chaîne de requête est complète, plus la liste de termes suggérés est pertinente. Par exemple, il se peut que les suggestions renvoyées par l’API pour une recherche du caractère *s* soient moins pertinentes celles renvoyées pour la recherche du terme *sailing dinghies* (canot à voile).

## <a name="getting-suggested-search-terms"></a>Obtention de suggestions de termes recherchés

L’exemple suivant illustre une requête qui renvoie les chaînes de requête suggérées pour *sail* (voile). N’oubliez pas d’encoder en URL le terme de requête partiel de l’utilisateur quand vous définissez le paramètre de requête [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query). Par exemple, si l’utilisateur a tapé *sailing les*, affectez la valeur `sailing+les` ou `sailing%20les` à `q`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

La réponse suivante contient une liste d’objets [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction) contenant les termes de requête suggérés.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

Chaque suggestion comporte les champs `displayText`, `query` et `url`. Le champ `displayText` correspond à la requête suggérée que vous utilisez pour remplir la liste déroulante de la zone de recherche. Vous devez afficher toutes les suggestions incluses dans la réponse, dans l’ordre indiqué.

Voici un exemple de zone de recherche déroulante avec des suggestions de termes de requête.

![Liste de zone de recherche déroulante Suggestion automatique](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Si l’utilisateur sélectionnait une suggestion de requête dans la liste déroulante, vous utiliseriez la chaîne de requête du champ `query` pour appeler l’[API Recherche Web Bing](../bing-web-search/search-the-web.md) et afficher vous-même les résultats. Vous pourriez aussi utiliser l’URL du champ `url` pour rediriger plutôt l’utilisateur vers la page des résultats de la recherche Bing.

## <a name="throttling-requests"></a>Demandes de limitation

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour configurer rapidement votre première requête, consultez [Création de votre première requête](quickstarts/csharp.md).

Familiarisez-vous avec les informations de référence sur [l’API Suggestion automatique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). Vous y trouverez la liste des points de terminaison, en-têtes et paramètres de requête à utiliser pour demander des suggestions de termes de requête, ainsi que les définitions des objets de réponse.

Découvrez comment effectuer des recherches sur le web à l’aide de l’[API Recherche Web Bing](../bing-web-search/search-the-web.md).

Prenez soin de lire les [exigences relatives à l’affichage et à l’utilisation de Bing](./useanddisplayrequirements.md) pour respecter les règles d’utilisation des résultats de la recherche.