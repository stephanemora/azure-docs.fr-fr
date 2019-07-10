---
title: Erreurs et exceptions (MSAL) | Azure
description: Découvrez comment gérer les erreurs et exceptions, l’accès conditionnel et les revendications dans les applications MSAL.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 30ab8a3fec459bef1a85c44e9a7cdb91b541fa2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111375"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Gestion des exceptions et erreurs à l’aide de MSAL
Dans Microsoft Authentication Library (MSAL), les exceptions sont destinées aux développeurs d’applications à des fins de résolution de problèmes et n’ont pas vocation à être vues par les utilisateurs finaux. Les messages liés aux exceptions ne sont pas localisés.

Quand vous traitez les exceptions et les erreurs, vous pouvez utiliser le type d’exception lui-même et le code d’erreur pour les distinguer.  Pour obtenir la liste des codes d’erreur, consultez [Codes d’erreur d’authentification et d’autorisation](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>Exceptions .NET
Quand vous traitez les exceptions, vous pouvez utiliser le type d’exception lui-même et le membre `ErrorCode` pour les distinguer. Les valeurs de `ErrorCode` sont des constantes de type [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Vous pouvez également examiner les champs de [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Si une exception de type [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) est levée, le code d’erreur peut contenir un code indiqué dans [Codes d’erreur d’authentification et d’autorisation](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Exceptions courantes
Voici les exceptions courantes pouvant être levées et certaines atténuations possibles.

| Exception | Code d'erreur | Atténuation|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001 : L’utilisateur ou l’administrateur n’a pas donné son consentement pour utiliser l’application portant l’ID « {appId} » et le nom « {appName} ». Envoyez une demande d’autorisation interactive pour cet utilisateur et cette ressource.| Vous devez d’abord obtenir le consentement de l’utilisateur. Si vous n’utilisez pas .NET Core (qui n’a pas d’interface utilisateur web), appelez (une seule fois) `AcquireTokeninteractive`. Si vous utilisez .NET Core ou que vous ne souhaitez pas effectuer une `AcquireTokenInteractive`, l’utilisateur peut accéder à une URL pour donner son consentement : https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read Pour appeler `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079 : L’utilisateur est obligé d’utiliser l’authentification multifacteur.| Aucune atténuation possible : si l’authentification multifacteur est configurée pour votre locataire et qu’AAD décide de l’appliquer, vous avez besoin de basculer vers un flux interactif, comme `AcquireTokenInteractive` ou `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010 : Le type d’autorisation n’est pas pris en charge sur les points de terminaison */common* ou */consumers*. Utilisez le point de terminaison */organizations* ou propre au locataire. Vous avez utilisé */common*.| Comme expliqué dans le message d’Azure AD, l’autorité doit avoir un locataire ou utiliser */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002 : Le corps de la demande doit contenir le paramètre suivant : « client_secret ou client_assertion ».| Cette erreur peut se produire si votre application n’a pas été inscrite en tant qu’application cliente publique dans Azure AD. Dans le portail Azure, modifiez le manifeste de votre application et affectez à `allowPublicClient` la valeur `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| Message unknown_user : Impossible d’identifier l’utilisateur connecté.| La bibliothèque n’a pas pu interroger l’utilisateur Windows actuellement connecté ou cet utilisateur n’est pas rattaché à AD ou AAD (les utilisateurs rattachés à l’espace de travail ne sont pas pris en charge). Atténuation 1 : Sur UWP, vérifiez que l’application a les fonctionnalités suivantes : Authentification en entreprise, Réseaux privés (client et serveur), Informations sur le compte d’utilisateur. Atténuation 2 : Implémentez votre propre logique pour extraire le nom d’utilisateur (par exemple, john@contoso.com) et utiliser le formulaire `AcquireTokenByIntegratedWindowsAuth` qui prend le nom d’utilisateur.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Cette méthode s’appuie sur un protocole exposé par Active Directory (AD). Si un utilisateur a été créé dans Azure Active Directory sans stockage dans AD (utilisateur « managé »), cette méthode échoue. Les utilisateurs créés dans AD et stockés par AAD (utilisateurs « fédérés ») peuvent bénéficier de cette méthode d’authentification non interactive. Atténuation : Utilisez l’authentification interactive.|

## <a name="javascript-errors"></a>Erreurs JavaScript

MSAL.js fournit des objets Error qui extraient et classent les différents types d’erreurs courantes et proposent une interface pour accéder à des détails spécifiques des erreurs comme les messages d’erreur afin de les traiter de manière appropriée.

**Objet Error**

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
* **AuthError.message :** Identique à errorMessage.
* **AuthError.stack :** Trace des erreurs levées. Permet le traçage jusqu’au point d’origine de l’erreur.

**Types d’erreurs**

Les types d’erreur suivants sont disponibles :

* *AuthError :* Classe d’erreur de base pour la bibliothèque MSAL.js, également utilisée pour les erreurs inattendues.

* *ClientAuthError :* Classe d’erreur qui indique un problème avec l’authentification du client. La plupart des erreurs provenant de la bibliothèque sont de type ClientAuthError. Il peut s’agir d’erreurs comme l’appel d’une méthode de connexion alors que la connexion est en cours, l’annulation de la connexion par des utilisateurs, etc.

* *ClientConfigurationError :* Classe d’erreur qui étend une erreur de type ClientAuthError levée avant que les demandes ne soient effectuées quand les paramètres de configuration utilisateur donnés sont incorrects ou manquants.

* *ServerError :* Classe d’erreur permettant de représenter les chaînes d’erreur envoyées par le serveur d’authentification. Il peut s’agir d’erreurs comme des formats ou paramètres de demande non valides, ou encore d’autres paramètres qui empêchent le serveur d’authentifier ou d’autoriser l’utilisateur.

* *InteractionRequiredAuthError :* Classe d’erreur qui étend une erreur de type ServerError permettant de représenter les erreurs de serveur qui exigent un appel interactif. Ce type d’erreur est levé par `acquireTokenSilent` si l’utilisateur est obligé d’interagir avec le serveur pour fournir des informations d’identification ou donner son consentement à l’authentification/autorisation. Les codes d’erreur incluent « interaction_required », « login_required », « consent_required ».

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

### <a name="interaction-required-errors"></a>Erreurs d’interaction requise

Une erreur est retournée quand une interaction avec l’interface utilisateur est requise. Cela signifie que vous avez tenté d’utiliser une méthode non interactive pour acquérir un jeton (par exemple, `acquireTokenSilent`), mais MSAL n’a pas pu le faire silencieusement. Les raisons possibles sont :

* Vous devez vous connecter.
* Vous devez donner votre consentement.
* Vous devez passer par une expérience d’authentification multifacteur.

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

## <a name="conditional-access-and-claims-challenges"></a>Accès conditionnel et revendications
Quand vous obtenez des jetons silencieusement, votre application risque de recevoir des erreurs quand une [revendication d’accès conditionnel](conditional-access-dev-guide.md), comme une stratégie d’authentification multifacteur, est exigée par une API à laquelle vous essayez d’accéder.

Le modèle à utiliser pour gérer cette erreur consiste à acquérir de manière interactive un jeton à l’aide de MSAL. L’acquisition interactive d’un jeton propose une invite à l’utilisateur et lui donne la possibilité de satisfaire à la stratégie d’accès conditionnel exigée.

Dans certains cas, lors de l’appel d’une API qui exige un accès conditionnel, vous pouvez recevoir une revendication dans l’erreur de la part de l’API. Par exemple, si la stratégie d’accès conditionnel consiste à utiliser un appareil managé (Intune), l’erreur est de type [AADSTS53000 : Votre appareil doit être managé pour accéder à cette ressource](reference-aadsts-error-codes.md) ou quelque chose de semblable. Dans ce cas, vous pouvez transmettre la revendication dans l’appel d’acquisition du jeton, afin que l’utilisateur soit invité à satisfaire à la stratégie appropriée.

### <a name="net"></a>.NET
Quand vous appelez une API qui exige un accès conditionnel à partir de MSAL.NET, votre application a besoin de gérer les exceptions liées aux revendications. Celles prennent la forme d’une [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) où la propriété [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) n’est pas vide.

Pour gérer la revendication, vous devez utiliser la méthode `.WithClaim()` de la classe `PublicClientApplicationBuilder`.

### <a name="javascript"></a>JavaScript
Quand vous obtenez des jetons silencieusement (en utilisant `acquireTokenSilent`) avec MSAL.js, votre application risque de recevoir des erreurs quand une [revendication d’accès conditionnel](conditional-access-dev-guide.md) comme une stratégie d’authentification multifacteur est exigée par une API à laquelle vous essayez d’accéder.

Le modèle permettant de gérer cette erreur consiste à effectuer un appel interactif pour acquérir un jeton dans MSAL.js comme `acquireTokenPopup` ou `acquireTokenRedirect`, comme dans l’exemple suivant :

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

L’acquisition interactive du jeton propose une invite à l’utilisateur et lui donne la possibilité de satisfaire à la stratégie d’accès conditionnel exigée.

Lors de l’appel d’une API qui exige un accès conditionnel, vous pouvez recevoir une revendication dans l’erreur de la part de l’API. Dans ce cas, vous pouvez transmettre la revendication retournée dans l’erreur en tant que `extraQueryParameters` dans l’appel d’acquisition du jeton, afin que l’utilisateur soit invité à satisfaire à la stratégie appropriée :

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Nouvelle tentative après des erreurs et exceptions

### <a name="http-error-codes-500-600"></a>Code d’erreur HTTP 500 à 600
MSAL.NET implémente un mécanisme simple de nouvelle tentative unique pour les erreurs avec codes d’erreur HTTP compris entre 500 et 600.

### <a name="http-429"></a>HTTP 429
Quand le service d’émission de jeton de sécurité (STS) est trop occupé en raison d’un nombre de demandes trop élevé, il retourne une erreur HTTP 429 avec une indication du moment auquel vous pouvez réessayer (champ de réponse Retry-After) sous la forme d’un délai exprimé en secondes ou d’une date.

#### <a name="net"></a>.NET
L’exception [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) fait apparaître `System.Net.Http.Headers.HttpResponseHeaders` en tant que propriété `namedHeaders`. Vous pouvez donc exploiter des informations supplémentaires dans le code d’erreur pour améliorer la fiabilité de vos applications. Dans le cas que nous venons de décrire, vous pouvez utiliser `RetryAfterproperty` (de type `RetryConditionHeaderValue`) pour calculer le moment auquel réessayer.

Voici un exemple pour une application de démon (qui utilise le flux d’informations d’identification du client), mais vous pouvez adapter cet exemple à toutes les méthodes d’acquisition d’un jeton.

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
