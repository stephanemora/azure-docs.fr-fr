---
title: Mettre à niveau l’API Recherche d’images Bing v5 vers v7
titleSuffix: Azure Cognitive Services
description: Ce guide de mise à niveau décrit les changements entre la version 5 et la version 7 de l’API Recherche d’images Bing. Utilisez-le pour identifier les parties de votre application que vous avez besoin de mettre à jour pour utiliser la version 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: a1d233273ced4891c9987ff8ba50e5491ae6071c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084457"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Guide de mise à niveau de l’API Recherche d’images Bing v7

> [!WARNING]
> Les API Recherche Bing passent de Cognitive Services aux services de recherche Bing. À compter du **30 octobre 2020** , toutes les nouvelles instances de Recherche Bing doivent être provisionnées en suivant le processus documenté [ici](https://aka.ms/cogsvcs/bingmove).
> Les API Recherche Bing provisionnées à l’aide de Cognitive Services seront prises en charge les trois prochaines années ou jusqu’à la fin de votre Accord Entreprise, selon la première éventualité.
> Pour obtenir des instructions de migration, consultez [Services de recherche Bing](https://aka.ms/cogsvcs/bingmigration).

Ce guide de mise à niveau identifie les changements entre la version 5 et la version 7 de l’API Recherche d’images Bing. Utilisez-le pour identifier les parties de votre application que vous avez besoin de mettre à jour pour utiliser la version 7.

## <a name="breaking-changes"></a>Changements cassants

### <a name="endpoints"></a>Points de terminaison

- Le numéro de version du point de terminaison a changé entre la version 5 et la version 7. Par exemple, https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Objets de réponse d’erreur et codes d’erreur

- Toutes les requêtes ayant échoué doivent maintenant inclure un objet `ErrorResponse` dans le corps de la réponse.

- Ajout des champs suivants à l’objet `Error`.  
  - `subCode`&mdash;Partitionne le code d’erreur en compartiments discrets, si possible
  - `moreDetails`&mdash;Informations supplémentaires sur l’erreur décrite dans le champ `message`


- Remplacement des codes d’erreur de la version 5 par les valeurs `code` et `subCode` suivantes.

|Code|SubCode|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing renvoie ServerError à chaque fois que l’une des conditions de sous-code se produit. La réponse inclut ces erreurs si le code d’état HTTP est 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqué|Bing retourne InvalidRequest à chaque fois que la demande comporte une partie non valide. Par exemple, un paramètre obligatoire est manquant ou une valeur de paramètre n’est pas valide.<br/><br/>Si l’erreur est ParameterMissing ou ParameterInvalidValue, le code d’état HTTP est 400.<br/><br/>Si l’erreur est HttpNotAllowed, le code d’état HTTP est 410.
|RateLimitExceeded||Bing retourne RateLimitExceeded chaque fois que vous dépassez votre quota de requêtes par seconde (QPS) ou par mois (QPM).<br/><br/>Bing renvoie le code d’état HTTP 429 si vous dépassez votre quota de requêtes par seconde (QPS), ou le code d’état 403 si vous dépassez votre quota mensuel de requêtes (QPM).
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing retourne InvalidAuthorization s’il ne parvient pas à identifier l’appelant. Par exemple, l’en-tête `Ocp-Apim-Subscription-Key` est manquant ou la clé d’abonnement n’est pas valide.<br/><br/>Il y a redondance si plusieurs méthodes d’authentification sont spécifiées.<br/><br/>Si l’erreur est InvalidAuthorization, le code d’état HTTP est 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing retourne InsufficientAuthorization lorsque l’appelant n’est pas autorisé à accéder à la ressource. Cela peut se produire si la clé d’abonnement a été désactivée ou a expiré. <br/><br/>Si l’erreur est InsufficientAuthorization, le code d’état HTTP est 403.

- Le code suivant mappe les codes d’erreur précédents vers les nouveaux codes. En cas de dépendance vis-à-vis des codes d’erreur de la version 5, modifiez votre code en conséquence.

|Code de la version 5|code.subCode de la version 7
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

- Paramètre de requête `modulesRequested` renommé en [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Annotations renommées en Balises Voir le paramètre de requête [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) pour Balises.  

- Valeur de la liste de marchés pris en charge de filtre ShoppingSources modifiée en en-US seulement. Consultez [ImageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Modifications des informations sur les images

- Champ `annotations` de [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) renommé en `imageTags`.  

- Objet `AnnotationModule` renommé en [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- Objet `Annotation` renommé en [Tag](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag), et champ `confidence` supprimé.  

- Champ `insightsSourcesSummary` de l’objet [Image](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) renommé en `insightsMetadata`.  

- Objet `InsightsSourcesSummary` renommé en [insightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Ajout du point de terminaison `https://api.cognitive.microsoft.com/bing/v7.0/images/details`. Utilisez ce point de terminaison pour demander des aperçus d’images au lieu du point de terminaison /images/search. Consultez [Informations sur les images](./image-insights.md).

- Les paramètres de requête suivants ne sont désormais valides qu’avec le point de terminaison `/images/details`.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Objet `ImageInsightsResponse` renommé en [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Types de données des champs suivants modifiés dans l’objet [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

    -   Type du champ `relatedCollections` de `ImageGallery[]` modifié dans [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Type du champ `pagesIncluding` de `Image[]` modifié dans [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Type du champ `relatedSearches` de `Query[]` modifié dans [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Type du champ `recipes` de `Recipe[]` modifié dans [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Type du champ `visuallySimilarImages` de `Image[]` modifié dans [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Type du champ `visuallySimilarProducts` de `ProductSummaryImage[]` modifié dans [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Objet `ProductSummaryImage` supprimé et champs associés au produit déplacé vers l’objet [Image](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image). L’objet `Image` inclut le champ des produits uniquement lorsque l’image est incluse comme produits visuellement similaires dans une réponse d’aperçu d’image.  

    -   Type du champ `recognizedEntityGroups` de `RecognizedEntityGroup[]` modifié dans [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Champ `categoryClassification` de [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) renommé en `annotations`, et type modifié en `AnnotationsModule`.  

### <a name="images-answer"></a>Réponse d’images

-   Champs displayRecipeSourcesBadges et displayShoppingSourcesBadges supprimés de [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Champ `nextOffsetAddCount` de [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) renommé en `nextOffset`. La façon d’utiliser le décalage a également changé. Auparavant, vous définissiez le paramètre de requête de [décalage](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) sur la valeur `nextOffsetAddCount`, ainsi que la valeur de décalage précédente et le nombre de vidéos dans le résultat. À présent, vous définissez `offset` sur la valeur `nextOffset`.  


## <a name="non-breaking-changes"></a>Changements non cassants

### <a name="query-parameters"></a>Paramètres de requête

- Ajout de Transparent comme valeur de filtre possible pour [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype). Le filtre Transparent retourne uniquement les images avec un arrière-plan transparent.

- Ajout de Any comme valeur de filtre possible pour [license](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license). Le filtre Any ne retourne que les images qui sont sous licence.

- Ajout des paramètres de requête [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) et [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize). Utilisez ces filtres pour retourner des images d’une étendue de taille de fichier.  

- Ajout des paramètres de requête [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth) et [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth). Utilisez ces filtres pour retourner des images d’une étendue de hauteurs et de largeurs.  

### <a name="object-changes"></a>Modifications d’objet

- Ajout des champs `description` et `lastUpdated` à l’objet [Offer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer).  

- Ajout du champ `name` à l’objet [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery).  

- Ajout de `similarTerms` à l’objet [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images). Ce champ contient une liste de termes au sens similaire à la chaîne de requête de l’utilisateur.  
