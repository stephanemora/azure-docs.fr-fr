---
title: Mise à niveau de l’API Vérification orthographique Bing (v5 vers v7) | Microsoft Docs
description: Identifiez les parties de votre application que vous devez mettre à jour pour utiliser la version 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7DC8FB29-4732-47D8-824B-CF2D7AEBA07B
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: article
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 1b0406641053fac8a4b3f4721728ad3b6c313ba1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367889"
---
# <a name="spell-check-api-upgrade-guide"></a>Guide de mise à niveau de l’API Vérification orthographique

Ce guide de mise à niveau identifie les changements entre la version 5 et la version 7 de l’API Vérification orthographique Bing. Utilisez ce guide pour identifier les parties de votre application que vous devez mettre à jour pour utiliser la version 7.

## <a name="breaking-changes"></a>Dernières modifications

### <a name="endpoints"></a>Points de terminaison

- Le numéro de version du point de terminaison a changé entre la version 5 et la version 7. Par exemple, https://api.cognitive.microsoft.com/bing/\*\*v7.0**/spellcheck.

### <a name="error-response-objects-and-error-codes"></a>Objets de réponse d’erreur et codes d’erreur

- Toutes les requêtes ayant échoué doivent maintenant inclure un objet `ErrorResponse` dans le corps de réponse.

- Ajout des champs suivants à l’objet `Error`.  
  - `subCode`&mdash; Partitionne le code d’erreur en compartiments discrets, si possible
  - `moreDetails`&mdash; Informations supplémentaires sur l’erreur décrite dans le champ `message`
   

- Remplacement des codes d’erreur v5 par les valeurs possibles `code` et `subCode` suivantes.  
  
|Code|SubCode|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing renvoie ServerError à chaque fois que l’une des conditions de sous-code se produit. La réponse inclut ces erreurs si le code d’état HTTP est 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqué|Bing renvoie InvalidRequest à chaque fois qu’une partie de la requête n’est pas valide. Par exemple, un paramètre obligatoire est manquant ou une valeur de paramètre n’est pas valide.<br/><br/>Si l’erreur est ParameterMissing ou ParameterInvalidValue, le code d’état HTTP est 400.<br/><br/>Si l’erreur est HttpNotAllowed, le code d’état HTTP est 410.
|RateLimitExceeded||Bing renvoie RateLimitExceeded à chaque fois que vous dépassez votre quota de requêtes par seconde (QPS) ou votre quota mensuel de requêtes (QPM).<br/><br/>Bing renvoie le code d’état HTTP 429 si vous dépassez votre quota de requêtes par seconde (QPS), ou le code d’état 403 si vous dépassez votre quota mensuel de requêtes (QPM).
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing renvoie InvalidAuthorization si aucune authentification de l’appelant n’est possible. Par exemple, l’en-tête `Ocp-Apim-Subscription-Key` est manquant ou la clé d’abonnement n’est pas valide.<br/><br/>Une redondance se produit si vous spécifiez plusieurs méthodes d’authentification.<br/><br/>Si l’erreur est InvalidAuthorization, le code d’état HTTP est 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing renvoie InsufficientAuthorization lorsque l’appelant n’est pas autorisé à accéder à la ressource. Cela peut se produire si la clé d’abonnement a été désactivée ou a expiré. <br/><br/>Si l’erreur est InvalidAuthorization, le code d’état HTTP est 403.

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

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Conditions d’utilisation et d’affichage](./UseAndDisplayRequirements.md)
