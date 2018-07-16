---
title: Démarrage rapide de l’API Suggestion automatique Bing | Microsoft Docs
description: Prise en main de l’API Suggestion automatique Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1482E781-7352-4A3F-B1D5-B896381348C4
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a7b54a1fb0b7c76eb72097357a6b51aa02e6e2fd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369973"
---
# <a name="making-your-first-autosuggest-query"></a>Créer votre première requête Suggestion automatique

Avant de passer votre premier appel d’API, vous devez obtenir une clé d’abonnement Cognitive Services. Pour obtenir une clé, rendez-vous sur la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api).

Pour obtenir les résultats de la recherche sur le Web, vous devez envoyer une requête GET au point de terminaison suivant :

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> Point de terminaison V7 Preview :
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

Vous devez utiliser le protocole HTTPS pour la requête.

Nous vous recommandons de générer toutes les requêtes à partir d’un serveur. Si vous diffusez la clé dans le cadre d’une application client, vous prenez le risque qu’un tiers malveillant puisse y accéder. Par ailleurs, en appelant l’API à partir d’un serveur, vous disposez d’un unique point de mise à niveau pour les prochaines versions de l’API.

La requête doit indiquer le paramètre [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) qui contient le terme de recherche partiel de l’utilisateur. La requête peut également indiquer le paramètre [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) (facultatif), qui vous permet de choisir le marché dont proviennent les résultats. Pour obtenir la liste complète des paramètres de requête facultatifs, consultez la page [Paramètres de requête](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Toutes les valeurs des paramètres de requête doivent être codées au format URL.

La requête doit indiquer l’en-tête [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Nous vous conseillons également d’indiquer les en-têtes suivants (qui sont facultatifs) :

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Les en-têtes relatifs à l’IP et à l’emplacement du client sont importants, car ils permettent de renvoyer du contenu géolocalisé.

Pour obtenir la liste complète de tous les en-têtes de requête et de réponse, consultez la page [Headers (En-têtes)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

## <a name="the-request"></a>Requête

La requête doit inclure tous les paramètres et en-têtes suggérés. Vous devez appeler cette API à chaque fois que l’utilisateur saisit un nouveau caractère dans la zone de recherche. L’exhaustivité de la chaîne de requête conditionne la pertinence des termes suggérés par l’API. Plus la chaîne de requête est complète, plus la liste de termes suggérés est pertinente. Par exemple, il se peut que les suggestions renvoyées par l’API pour une recherche du caractère *s* soient moins pertinentes celles renvoyées pour la recherche du terme *sailing dinghies* (canot à voile). 

L’exemple suivant illustre une requête qui renvoie les chaînes de requête suggérées pour *sail* (voile).

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> Requête V7 Preview :

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Si vous appelez l’une des API Bing pour la première fois, n’incluez pas l’en-tête ID client. Indiquez uniquement l’en-tête ID client si vous avez précédemment appelé une API Bing et que Bing a retourné un ID client pour la combinaison utilisateur/appareil.

La réponse à la requête précédente est affichée ci-dessous. Elle inclut un groupe de suggestions web comportant une liste de suggestions pour la requête de recherche. Chaque suggestion comporte les champs `displayText`, `query` et `url`.

Le champ `displayText` correspond à la requête suggérée à utiliser dans la liste déroulante de la zone de recherche. Vous devez afficher toutes les suggestions incluses dans la réponse, dans l’ordre indiqué.  

Si l’utilisateur sélectionne une requête dans la liste déroulante, vous pouvez utiliser la chaîne de requête du champ `query` pour appeler l’[API Recherche Bing](../bing-web-search/search-the-web.md) et afficher vous-même les résultats. Vous pouvez également utiliser l’URL du champ `url` pour rediriger l’utilisateur vers la page des résultats de la recherche Bing.

```  
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Étapes suivantes

Essayez l’API. Accédez à la [console de test de l’API Suggestion automatique](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2).

Pour plus d’informations sur la consommation des objets de réponse, consultez la page [Obtenir des suggestions de termes de recherche](./get-suggested-search-terms.md).
