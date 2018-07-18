---
title: Mettre à niveau l’API Recherche de vidéos Bing de v5 vers v7 | Microsoft Docs
description: Identifiez les parties de votre application que vous avez besoin de mettre à jour pour utiliser la version 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: FA7DDF07-97AC-4EBE-8C50-A9737D43B38E
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 62646d026e141d0549c68e18f9318fa32d3e00df
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368013"
---
# <a name="video-search-api-upgrade-guide"></a>Guide de mise à niveau de l’API Recherche de vidéos Bing

Ce guide de mise à niveau identifie les changements entre la version 5 et la version 7 de l’API Recherche de vidéos Bing. Utilisez ce guide pour identifier les parties de votre application que vous avez besoin de mettre à jour pour utiliser la version 7.

## <a name="breaking-changes"></a>Dernières modifications

### <a name="endpoints"></a>Points de terminaison

- Le numéro de version du point de terminaison a changé entre la version 5 et la version 7. Par exemple, https://api.cognitive.microsoft.com/bing/\*\*v7.0**/videos/search.

### <a name="error-response-objects-and-error-codes"></a>Objets de réponse d’erreur et codes d’erreur

- Toutes les requêtes ayant échoué doivent maintenant inclure un objet `ErrorResponse` dans le corps de réponse.

- Ajout des champs suivants à l’objet `Error`.  
  - `subCode`&mdash;Partitionne le code d’erreur en compartiments discrets, si possible
  - `moreDetails`&mdash;Informations supplémentaires sur l’erreur décrite dans le champ `message`
   

- Remplacement des codes d’erreur v5 par les valeurs possibles `code` et `subCode` suivantes.

|Code|SubCode|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing renvoie ServerError chaque fois qu’une des conditions de sous-code se produit. La réponse inclut ces erreurs si le code d’état HTTP est 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqué|Bing renvoie InvalidRequest chaque fois qu’une partie de la requête n’est pas valide. Par exemple, un paramètre obligatoire est manquant ou une valeur de paramètre n’est pas valide.<br/><br/>Si l’erreur est ParameterMissing ou ParameterInvalidValue, le code d’état HTTP est 400.<br/><br/>Si l’erreur est HttpNotAllowed, le code d’état HTTP est 410.
|RateLimitExceeded||Bing renvoie RateLimitExceeded chaque fois que vous dépassez votre quota de requêtes par seconde (QPS) ou votre quota mensuel de requêtes (QPM).<br/><br/>Bing renvoie le code d’état HTTP 429 si vous dépassez votre quota de nombre de requête par seconde (QPS), ou le code d’état 403 si vous dépassez votre quota mensuel de requête (QPM).
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing renvoie InvalidAuthorization s’il ne peut pas authentifier l’appelant. Par exemple, l’en-tête `Ocp-Apim-Subscription-Key` est manquant ou la clé de l’abonnement n’est pas valide.<br/><br/>Une redondance se produit si vous spécifiez plusieurs méthodes d’authentification.<br/><br/>Si l’erreur est InvalidAuthorization, le code d’état HTTP est 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing renvoie InsufficientAuthorization lorsque l’appelant n’est pas autorisé à accéder à la ressource. Cela peut se produire si la clé de l’abonnement a été désactivée ou a expiré. <br/><br/>Si l’erreur est InvalidAuthorization, le code d’état HTTP est 403.

- Le code suivant mappe les codes d’erreur précédents vers les nouveaux codes. Si vous avez établi une dépendance sur les codes d’erreur v5, mettez à jour votre code en conséquence.

|Code de la version 5|Code code.subCode de la version 7
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

### <a name="query-parameters"></a>Paramètres de requête

- Le paramètre de requête `modulesRequested` a été renommé en [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Modifications d’objet

- Le champ `nextOffsetAddCount` de [Vidéos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) a été renommé en `nextOffset`. La façon d’utiliser le décalage a également changé. Auparavant, vous devez définir le paramètre de requête de [décalage](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) sur la valeur `nextOffset`, ainsi que la valeur de décalage précédente et le nombre de vidéos dans le résultat. À présent, définissez simplement le paramètre de requête `offset` sur la valeur `nextOffset`.  
  
- Changement du type de données du champ `relatedVideos` de `Video[]` à [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule) (voir [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails)).

