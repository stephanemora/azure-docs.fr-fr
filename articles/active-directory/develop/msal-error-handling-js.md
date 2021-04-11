---
title: Gérer les erreurs et les exceptions dans MSAL.js
titleSuffix: Microsoft identity platform
description: Découvrez comment gérer les erreurs et exceptions, les défis relatifs aux revendications d’accès conditionnel et les nouvelles tentatives dans MSAL pour les applications iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761347"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>Gérer les erreurs et les exceptions dans MSAL.js

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>Gestion des erreurs dans MSAL.js

MSAL.js fournit des objets d’erreur qui résument et classifient les différents types d’erreurs courantes. Il fournit également une interface permettant d’accéder à des détails spécifiques des erreurs, comme les messages d’erreur, afin de les traiter de manière appropriée.

### <a name="error-object"></a>Objet d’erreur

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

En étendant la classe d’erreur, vous avez accès aux propriétés suivantes :
- `AuthError.message`:  Identique à `errorMessage`.
- `AuthError.stack`: Trace des erreurs levées.

### <a name="error-types"></a>Types d’erreurs

Les types d’erreur suivants sont disponibles :

- `AuthError`: Classe d’erreur de base pour la bibliothèque MSAL.js, également utilisée pour les erreurs inattendues.

- `ClientAuthError`: Classe d’erreur qui indique un problème avec l’authentification du client. La plupart des erreurs provenant de la bibliothèque sont de type ClientAuthError. Ces erreurs proviennent de l’appel d’une méthode de connexion lorsque la connexion est déjà en cours, que l’utilisateur annule la connexion, et ainsi de suite.

- `ClientConfigurationError`: Classe d’erreur qui étend une erreur de type `ClientAuthError` levée avant que les demandes ne soient effectuées quand les paramètres de configuration utilisateur donnés sont incorrects ou manquants.

- `ServerError`: Classe d’erreur représentant les chaînes d’erreur envoyées par le serveur d’authentification. Il peut s’agir d’erreurs comme des formats ou paramètres de demande non valides, ou encore d’autres paramètres qui empêchent le serveur d’authentifier ou d’autoriser l’utilisateur.

- `InteractionRequiredAuthError`: Classe d’erreur qui étend une erreur de type `ServerError` permettant de représenter les erreurs de serveur qui exigent un appel interactif. Ce type d’erreur est levé par `acquireTokenSilent` si l’utilisateur est obligé d’interagir avec le serveur pour fournir des informations d’identification ou donner son consentement à l’authentification/autorisation. Les codes d’erreur sont les suivants : `"interaction_required"`, `"login_required"` et `"consent_required"`.

Pour gérer les erreurs dans les flux d’authentification avec des méthodes de redirection (`loginRedirect`, `acquireTokenRedirect`), vous devez inscrire le rappel qui est appelé avec ou sans succès après la redirection à l’aide de la méthode `handleRedirectCallback()` comme suit :

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Les méthodes liées à une expérience contextuelle (`loginPopup`, `acquireTokenPopup`) retournent des promesses, donc vous pouvez utiliser le modèle de promesse (.then et .catch) pour les gérer comme indiqué :

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Erreurs qui nécessitent une interaction

Une erreur est retournée lorsque vous essayez d’utiliser une méthode non interactive pour acquérir un jeton, tel que `acquireTokenSilent`, mais que MSAL ne peut pas le faire en mode silencieux.

Les raisons possibles sont :

- Vous devez vous connecter.
- Vous devez donner votre consentement.
- Vous devez passer par une expérience d’authentification multifacteur.

Pour corriger cette erreur, appelez une méthode interactive comme `acquireTokenPopup` ou `acquireTokenRedirect` :

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Quand vous obtenez des jetons silencieusement (en utilisant `acquireTokenSilent`) avec MSAL.js, votre application risque de recevoir des erreurs quand une [revendication d’accès conditionnel](../azuread-dev/conditional-access-dev-guide.md) comme une stratégie d’authentification multifacteur est exigée par une API à laquelle vous essayez d’accéder.

Le modèle permettant de gérer cette erreur consiste à effectuer un appel interactif pour acquérir un jeton dans MSAL.js comme `acquireTokenPopup` ou `acquireTokenRedirect`, comme dans l’exemple suivant :

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

L’acquisition interactive du jeton propose une invite à l’utilisateur et lui donne la possibilité de satisfaire à la stratégie d’accès conditionnel exigée.

Lors de l’appel d’une API qui exige un accès conditionnel, vous pouvez recevoir une revendication dans l’erreur de la part de l’API. Dans ce cas, vous pouvez passer les revendications retournées dans l’erreur au champ `claimsRequest` de la classe `AuthenticationParameters.ts` pour satisfaire la stratégie appropriée. 

Pour plus d’informations, consultez [Demander des revendications supplémentaires](active-directory-optional-claims.md).


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Étapes suivantes

Envisagez l’activation de la [journalisation dans MSAL.js](msal-logging-js.md) pour vous aider à diagnostiquer et à déboguer les problèmes.
