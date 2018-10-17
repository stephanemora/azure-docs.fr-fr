---
title: 'Démarrage rapide : API Recherche d’entités Bing'
description: Montre comment prendre en main l’API Recherche d’entités Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: ffc9ebb21c6646b1a39af4659053adf4157d204b
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48813955"
---
# <a name="quickstart-making-your-first-bing-entity-search-request"></a>Démarrage rapide : votre première demande de recherche d’entités Bing

L’API Recherche d’entités Bing envoie une requête de recherche à Bing et obtient des résultats comprenant des entités et des lieux. Les résultats de lieux incluent les restaurants, les hôtels et d’autres commerces locaux. Pour les lieux, la requête peut spécifier le nom du commerce local ou elle peut demander une liste (par exemple, les restaurants dans ma zone). Les résultats d’entités incluent des personnes, des lieux ou d’autres éléments. Dans ce contexte, les lieux correspondent à des sites touristiques, des États, des pays, etc. 

## <a name="first-steps"></a>Premières étapes

Avant de pouvoir passer votre premier appel, vous devez obtenir une clé d’abonnement Cognitive Services. Pour obtenir une clé, consultez [Essayer Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api). (Si l’API Recherche d’entités n’est pas disponible dans la partie supérieure, cliquez sur l’onglet **Recherche** et faites défiler la page jusqu’à ce que vous la trouviez.)

## <a name="the-endpoint"></a>Point de terminaison

Pour obtenir les résultats de la recherche d’entités et de lieux, vous devez envoyer une requête GET au point de terminaison suivant :  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Vous devez utiliser le protocole HTTPS pour la requête.

Nous vous recommandons de générer toutes les requêtes à partir d’un serveur. Si vous diffusez la clé dans le cadre d’une application client, vous prenez le risque qu’un tiers malveillant puisse y accéder. Par ailleurs, en appelant l’API à partir d’un serveur, vous disposez d’un unique point de mise à niveau pour les prochaines versions de l’API.

## <a name="specifying-query-parameters-and-headers"></a>Indiquer les paramètres de requête et les en-têtes

La requête doit indiquer le paramètre [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) qui contient le terme de recherche de l’utilisateur. La requête doit également indiquer le paramètre [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt) qui vous permet de choisir le marché dont proviennent les résultats. Pour obtenir la liste complète des paramètres de requête facultatifs, consultez la page [Paramètres de requête](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters). L’URL encode tous les paramètres de requête.  
  
La requête doit indiquer l’en-tête [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey). Nous vous conseillons également d’indiquer les en-têtes suivants (qui sont facultatifs) :  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

Les en-têtes d’emplacement et d’adresse IP client sont importants, car ils permettent de renvoyer du contenu géolocalisé.  

Pour obtenir la liste complète des en-têtes de requête et de réponse, consultez la page [En-têtes](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>Requête

Le code suivant illustre une requête d’entités qui inclut tous les paramètres de requête et les en-têtes suggérés. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Si vous appelez l’une des API Bing pour la première fois, n’incluez pas l’en-tête ID client. Indiquez uniquement l’ID client si vous avez précédemment appelé une API Bing et que Bing a renviyé un ID client pour la combinaison utilisateur/appareil.

## <a name="the-response"></a>Réponse

Le code suivant affiche la réponse à la requête précédente. L’exemple montre également les en-têtes de réponse spécifiques à Bing. Pour plus d’informations sur l’objet de réponse, consultez la page [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```



## <a name="next-steps"></a>Étapes suivantes

Essayez l’API. Accédez à la [console de test de l’API Recherche d’entités](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/). 

Pour en savoir plus sur la consommation des objets de réponse, consultez la page [Recherche d’entités et de lieux sur le Web](./search-the-web.md).

Veillez à lire les [exigences relatives à l’affichage et à l’utilisation des API Bing](./use-display-requirements.md) pour respecter les règles d’utilisation des résultats de la recherche.
