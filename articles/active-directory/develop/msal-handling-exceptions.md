---
title: Erreurs et exceptions (MSAL) | Azure
description: Découvrez comment gérer les erreurs et exceptions, l’accès conditionnel et les revendications dans les applications MSAL.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: negoe
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 280746281fd45b3286cc76be5d3483f0cc65f90f
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70872791"
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
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001 : L’utilisateur ou l’administrateur n’a pas donné son consentement pour utiliser l’application portant l’ID « {appId} » et le nom « {appName} ». Envoyez une demande d’autorisation interactive pour cet utilisateur et cette ressource.| Vous devez d’abord obtenir le consentement de l’utilisateur. Si vous n’utilisez pas .NET Core (qui n’a pas d’interface utilisateur web), appelez (une seule fois) `AcquireTokeninteractive`. Si vous utilisez .NET Core ou que vous ne souhaitez pas effectuer une `AcquireTokenInteractive`, l’utilisateur peut accéder à une URL pour donner son consentement : https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. Pour appeler `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079 : L’utilisateur est obligé d’utiliser l’authentification multifacteur.| Aucune atténuation possible : si l’authentification multifacteur est configurée pour votre locataire et qu’AAD décide de l’appliquer, vous avez besoin de basculer vers un flux interactif, comme `AcquireTokenInteractive` ou `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010 : Le type d’autorisation n’est pas pris en charge sur les points de terminaison */common* ou */consumers*. Utilisez le point de terminaison */organizations* ou propre au locataire. Vous avez utilisé */common*.| Comme expliqué dans le message d’Azure AD, l’autorité doit avoir un locataire ou utiliser */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002 : Le corps de la demande doit contenir le paramètre suivant : « client_secret ou client_assertion ».| Cette exception peut se produire si votre application n’a pas été inscrite en tant qu’application cliente publique dans Azure AD. Dans le portail Azure, modifiez le manifeste de votre application et affectez à `allowPublicClient` la valeur `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| Message unknown_user : Impossible d’identifier l’utilisateur connecté.| La bibliothèque n’a pas pu interroger l’utilisateur Windows actuellement connecté ou cet utilisateur n’est pas rattaché à AD ou AAD (les utilisateurs rattachés à l’espace de travail ne sont pas pris en charge). Atténuation 1 : Sur UWP, vérifiez que l’application a les fonctionnalités suivantes : Authentification en entreprise, Réseaux privés (client et serveur), Informations sur le compte d’utilisateur. Atténuation 2 : Implémentez votre propre logique pour extraire le nom d’utilisateur (par exemple, john@contoso.com) et utiliser le formulaire `AcquireTokenByIntegratedWindowsAuth` qui prend le nom d’utilisateur.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Cette méthode s’appuie sur un protocole exposé par Active Directory (AD). Si un utilisateur a été créé dans Azure Active Directory sans stockage dans AD (utilisateur « managé »), cette méthode échoue. Les utilisateurs créés dans AD et stockés par AAD (utilisateurs « fédérés ») peuvent bénéficier de cette méthode d’authentification non interactive. Atténuation : Utilisez l’authentification interactive.|

### `MsalUiRequiredException`

L’un des codes d’état courants retournés par MSAL.NET lors de l’appel à `AcquireTokenSilent()` est `MsalError.InvalidGrantError`. Ce code d’état signifie que l’application doit rappeler la bibliothèque d’authentification, mais en mode interactif (AcquireTokenInteractive ou AcquireTokenByDeviceCodeFlow pour les applications clientes publiques, et faire un test dans les applications web). Cela est dû au fait que l’interaction de l’utilisateur supplémentaire est nécessaire pour que le jeton d’authentification puisse être émis.

La plupart du temps, lorsque `AcquireTokenSilent` échoue, cela est dû au fait que le cache du jeton n’a pas de jeton correspondant à votre demande. Les jetons d’accès expirent dans un délai d’une heure et `AcquireTokenSilent` tente d’en extraire un nouveau en fonction d’un jeton d’actualisation (en termes de OAuth2, il s’agit du flux « Jeton d’actualisation »). Ce flux peut également échouer pour diverses raisons, par exemple si un administrateur de locataire configure des stratégies de connexion plus strictes. 

L’interaction vise à faire en sorte que l’utilisateur effectue une action. Certaines de ces conditions sont faciles à résoudre par les utilisateurs (par exemple, accepter les Conditions d’utilisation d’un simple clic), et d’autres ne peuvent pas être résolues avec la configuration actuelle (par exemple, l’ordinateur en question doit se connecter à un réseau d’entreprise spécifique). Certaines aident l’utilisateur à configurer l’authentification multifacteur ou à installer Microsoft Authenticator sur son appareil.

### <a name="msaluirequiredexception-classification-enumeration"></a>Énumération de classification `MsalUiRequiredException`

MSAL expose un champ `Classification`, que vous pouvez lire pour fournir une meilleure expérience utilisateur, par exemple pour indiquer à l’utilisateur que son mot de passe a expiré ou qu’il devra fournir son consentement pour utiliser certaines ressources. Les valeurs prises en charge font partie de l’énumération `UiRequiredExceptionClassification` :

| classification ;    | Signification           | Gestion recommandée |
|-------------------|-------------------|----------------------|
| BasicAction | La condition peut être résolue par l’interaction de l’utilisateur pendant le flux d’authentification interactive. | Appelez AcquireTokenInteractively(). |
| AdditionalAction | La condition peut être résolue par une interaction corrective supplémentaire avec le système, en dehors du flux d’authentification interactive. | Appelez AcquireTokenInteractively() pour afficher un message expliquant l’action corrective. L’appel de l’application peut choisir de masquer les flux qui requièrent additional_action si l’utilisateur est peu susceptible d’effectuer l’action corrective. |
| MessageOnly      | La condition ne peut pas être résolue pour l’instant. Le lancement du flux d’authentification interactive affiche un message qui explique la condition. | Appelez AcquireTokenInteractively() pour afficher un message expliquant la condition. AcquireTokenInteractively() renvoie une erreur UserCanceled lorsque l’utilisateur lit le message et ferme la fenêtre. L’appel de l’application peut choisir de masquer les flux qui résultent en message_only si l’utilisateur est peu susceptible de tirer parti du message.|
| ConsentRequired  | Le consentement de l’utilisateur est manquant ou a été révoqué. | Appelez AcquireTokenInteractively() pour que l’utilisateur donne son consentement. |
| UserPasswordExpired | Le mot de passe de l’utilisateur a expiré. | Appelez AcquireTokenInteractively() pour que l’utilisateur puisse réinitialiser son mot de passe. |
| PromptNeverFailed| L’authentification interactive a été appelée avec le paramètre prompt=never, forçant MSAL à s’appuyer sur les cookies du navigateur et à ne pas afficher le navigateur. Ceci a échoué. | Appelez AcquireTokenInteractively() sans Prompt.None |
| AcquireTokenSilentFailed | Le SDK MSAL ne dispose pas de suffisamment d’informations pour extraire un jeton du cache. Cela peut être dû au fait qu’aucun jeton n’est présent dans le cache ou qu’un compte n’a pas été trouvé. Le message d’erreur contient plus d’informations.  | Appelez AcquireTokenInteractively(). |
| Aucun    | Aucun détail supplémentaire n’est fourni. La condition peut être résolue par l’interaction de l’utilisateur pendant le flux d’authentification interactive. | Appelez AcquireTokenInteractively(). |

## <a name="code-example"></a>Exemple de code

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
   res = await application.AcquireTokenInteractive(scopes)
                          .ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```


## <a name="javascript-errors"></a>Erreurs JavaScript

MSAL.js fournit des objets d’erreur qui résument et classifient les différents types d’erreurs courantes. Il fournit également une interface pour accéder à des détails spécifiques des erreurs, comme les messages d’erreur, afin de les traiter de manière appropriée.

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
* **AuthError.message :**  Identique à errorMessage.
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

Une erreur est retournée lorsque vous essayez d’utiliser une méthode non interactive pour acquérir un jeton (par exemple `acquireTokenSilent`) et que MSAL ne peut pas le faire en mode silencieux.

Les raisons possibles sont :

* l’utilisateur doit se connecter
* l’utilisateur doit donner son consentement
* l’utilisateur doit passer par une expérience d’authentification multifacteur

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
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

L’acquisition interactive du jeton propose une invite à l’utilisateur et lui donne la possibilité de satisfaire à la stratégie d’accès conditionnel exigée.

Lors de l’appel d’une API qui exige un accès conditionnel, vous pouvez recevoir une revendication dans l’erreur de la part de l’API. Dans ce cas, vous pouvez passer les revendications retournées dans l’erreur au champ `claimsRequest` de la classe `AuthenticationParameters.ts` pour satisfaire la stratégie appropriée. 

Pour plus d’informations, consultez [Demander des revendications supplémentaires](active-directory-optional-claims.md).

## <a name="retrying-after-errors-and-exceptions"></a>Nouvelle tentative après des erreurs et exceptions

Vous êtes censé implémenter vos propres stratégies de nouvelle tentative lors de l’appel de MSAL. MSAL effectue des appels HTTP au service AAD et des défaillances occasionnelles peuvent se produire, par exemple le réseau peut être en panne ou le serveur surchargé.  

### <a name="http-error-codes-500-600"></a>Code d’erreur HTTP 500 à 600

MSAL.NET implémente un mécanisme simple de nouvelle tentative unique pour les erreurs avec codes d’erreur HTTP compris entre 500 et 600.

### <a name="http-429"></a>HTTP 429

Lorsque le serveur de jeton de service (STS) est surchargé avec un trop grand nombre de requêtes, il renvoie une erreur HTTP 429 en indiquant quand vous pouvez réessayer en termes de temps. L’erreur peut être lue à partir du champ de réponse `Retry-After`.

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
