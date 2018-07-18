---
title: Mise à niveau de l’API Recherche d'actualités Bing (v5 vers v7) | Microsoft Docs
description: Identifiez les parties de votre application que vous devez mettre à jour pour utiliser la version 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 5334C475-4841-4736-A66E-DC1E07CBCEC9
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: baed6f0091ddad40b4802c0fb52dc2ca1818cd03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368096"
---
# <a name="news-search-api-upgrade-guide"></a>Guide de mise à niveau de l’API Recherche d'actualités Bing

Ce guide de mise à niveau identifie les changements entre la version 5 et la version 7 de l’API Recherche d'actualités Bing. Utilisez ce guide pour identifier les parties de votre application que vous devez mettre à jour pour utiliser la version 7.

## <a name="breaking-changes"></a>Dernières modifications

### <a name="endpoints"></a>Points de terminaison

- Le numéro de version du point de terminaison a changé entre la version 5 et la version 7. Par exemple, https://api.cognitive.microsoft.com/bing/\*\*v7.0**/news/search.

### <a name="error-response-objects-and-error-codes"></a>Objets de réponse d’erreur et codes d’erreur

- Toutes les requêtes ayant échoué doivent maintenant inclure un objet `ErrorResponse` dans le corps de réponse.

- Ajout des champs suivants à l’objet `Error`.  
  - `subCode`&mdash; Partitionne le code d’erreur en compartiments discrets, si possible
  - `moreDetails`&mdash; Informations supplémentaires sur l’erreur décrite dans le champ `message`
   

- Remplacement des codes d’erreur v5 par les valeurs possibles `code` et `subCode` suivantes.

|Code|SubCode|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing renvoie ServerError à chaque fois que l’une des conditions de sous-code se produit. La réponse inclut ces erreurs si le code d’état HTTP est 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqué|Bing retourne InvalidRequest à chaque fois que la demande comporte une partie non valide. Par exemple, un paramètre obligatoire est manquant ou une valeur de paramètre n’est pas valide.<br/><br/>Si l’erreur est ParameterMissing ou ParameterInvalidValue, le code d’état HTTP est 400.<br/><br/>Si l’erreur est HttpNotAllowed, le code d’état HTTP est 410.
|RateLimitExceeded||Bing retourne RateLimitExceeded chaque fois que vous dépassez votre quota de requêtes par seconde (QPS) ou par mois (QPM).<br/><br/>Bing renvoie le code d’état HTTP 429 si vous dépassez votre quota de requêtes par seconde (QPS), ou le code d’état 403 si vous dépassez votre quota mensuel de requêtes (QPM).
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing retourne InvalidAuthorization s’il ne parvient pas à identifier l’appelant. Par exemple, l’en-tête `Ocp-Apim-Subscription-Key` est manquant ou la clé d’abonnement n’est pas valide.<br/><br/>Il y a redondance si plusieurs méthodes d’authentification sont spécifiées.<br/><br/>Si l’erreur est InvalidAuthorization, le code d’état HTTP est 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing retourne InsufficientAuthorization lorsque l’appelant n’est pas autorisé à accéder à la ressource. Cela peut se produire si la clé d’abonnement a été désactivée ou a expiré. <br/><br/>Si l’erreur est InsufficientAuthorization, le code d’état HTTP est 403.

- Le code suivant mappe les codes d’erreur précédents vers les nouveaux codes. Si vous avez établi une dépendance sur les codes d’erreur v5, mettez à jour votre code en conséquence.

|Code de la version 5|Version 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Désactivé|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Bloqué|InvalidRequest.Blocked

### <a name="object-changes"></a>Modifications d’objet

- Ajout du champ `contractualRules` à l’objet [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle). Le champ `contractualRules` contient une liste de règles que vous devez respecter (l’attribution d’article, par exemple). Vous devez appliquer l’attribution fournie dans `contractualRules` au lieu d’utiliser `provider`. L’article inclut `contractualRules` seulement lorsque la réponse de l’[API Recherche Web Bing](../bing-web-search/search-the-web.md) contient Actualités.

## <a name="non-breaking-changes"></a>Changements non cassants

### <a name="query-parameters"></a>Paramètres de requête

- Ajout de Produits comme valeur possible définissable pour le paramètre de requête [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category). Consultez [Catégories par marchés](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#categories-by-market).  
    
- Ajout du paramètre de requête [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortby), qui retourne les rubriques tendance triées par date en commençant par la plus récente.  
  
- Ajout du paramètre de requête [Since](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#since), qui renvoie les rubriques tendance découvertes par Bing pendant ou après le timestamp d’époque Unix.

### <a name="object-changes"></a>Modifications d’objet

- Ajout du champ `mentions` à l’objet [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle). Le champ `mentions` contient une liste d’entités (personnes ou lieux) trouvées dans l’article.  
  
- Ajout du champ `video` à l’objet [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle). Le champ `video` contient une vidéo en lien avec l’article. La vidéo est soit \<iframe\>, et vous pouvez l’intégrer, soit un vidéo miniature.   
  
- Ajout du champ `sort` à l’objet [News](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news). Le champ `sort` indique l’ordre de tri des articles. Par exemple, les articles sont triés par pertinence (par défaut) ou par date.  
  
- Ajout de l’objet [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortvalue), qui définit l’ordre de tri. Le champ `isSelected` indique si la réponse a utilisé l’ordre de tri. Si la valeur est **true**, la réponse a utilisé l’ordre de tri. Si `isSelected` est **false**, vous pouvez utiliser l’URL dans le champ `url` pour demander un ordre de tri différent.
