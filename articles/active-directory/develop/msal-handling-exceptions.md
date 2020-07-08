---
title: Erreurs et exceptions (MSAL)
titleSuffix: Microsoft identity platform
description: Découvrez comment gérer les erreurs et exceptions, l’accès conditionnel et les revendications dans les applications MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: c27938227a13934de11dd6e88d58138c46c3f58e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204624"
---
# <a name="handle-msal-exceptions-and-errors"></a>Gérer les erreurs et les exceptions MSAL

Cet article donne une vue d’ensemble des différents types d’erreurs et des recommandations pour la gestion des erreurs de connexion courantes.

## <a name="msal-error-handling-basics"></a>Notions de base de la gestion des erreurs MSAL

Dans Microsoft Authentication Library (MSAL), les exceptions sont destinées aux développeurs d’applications à des fins de résolution de problèmes. Elles n’ont pas vocation à être vues par les utilisateurs finaux. Les messages liés aux exceptions ne sont pas localisés.

Quand vous traitez les exceptions et les erreurs, vous pouvez utiliser le type d’exception lui-même et le code d’erreur pour les distinguer.  Pour obtenir la liste des codes d’erreur, consultez [Codes d’erreur d’authentification et d’autorisation](reference-aadsts-error-codes.md).

Durant la connexion, vous pouvez rencontrer des erreurs concernant les consentements, l’accès conditionnel (MFA, gestion des appareils, restrictions basées sur l’emplacement), l’émission et l’échange de jetons, et les propriétés utilisateur.

Pour plus d’informations sur la gestion des erreurs pour votre application, consultez la section suivante qui correspond à la langue que vous utilisez.

## <a name="net"></a>[.NET](#tab/dotnet)

Quand vous traitez les exceptions .NET, vous pouvez utiliser le type d’exception lui-même et le membre `ErrorCode` pour les distinguer. Les valeurs de `ErrorCode` sont des constantes de type [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Vous pouvez également examiner les champs de [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) et [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Si l’exception [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) est levée, consultez la liste [Codes d’erreur d’authentification et d’autorisation](reference-aadsts-error-codes.md) pour vérifier si le code y est répertorié.

### <a name="common-net-exceptions"></a>Exceptions .NET courantes

Voici les exceptions courantes pouvant être levées et certaines atténuations possibles :  

| Exception | Code d'erreur | Limitation des risques|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001 : L’utilisateur ou l’administrateur n’a pas donné son consentement pour utiliser l’application portant l’ID « {appId} » et le nom « {appName} ». Envoyez une demande d’autorisation interactive pour cet utilisateur et cette ressource.| Vous devez d’abord obtenir le consentement de l’utilisateur. Si vous n’utilisez pas .NET Core (qui n’a pas d’interface utilisateur web), appelez (une seule fois) `AcquireTokeninteractive`. Si vous utilisez .NET Core ou que vous ne souhaitez pas effectuer une `AcquireTokenInteractive`, l’utilisateur peut accéder à une URL pour donner son consentement : `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`. pour appeler `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079 : L’utilisateur est obligé d’utiliser [l’authentification multifacteur (MFA)](../authentication/concept-mfa-howitworks.md).| Il n’y a pas d’atténuation. Si l’authentification multifacteur est configurée pour votre locataire et qu’Azure Active Directory (AAD) décide de l’appliquer, vous devez revenir à un flux interactif, comme `AcquireTokenInteractive` ou `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010 : Le type d’autorisation n’est pas pris en charge sur les points de terminaison */common* ou */consumers*. Utilisez le point de terminaison */organizations* ou propre au locataire. Vous avez utilisé */common*.| Comme expliqué dans le message d’Azure AD, l’autorité doit avoir un locataire ou utiliser */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002 : Le corps de la requête doit contenir le paramètre : `client_secret or client_assertion`.| Cette exception peut être levée si votre application n’a pas été inscrite en tant qu’application cliente publique dans Azure AD. Dans le Portail Azure, modifiez le manifeste de votre application et affectez à `allowPublicClient` la valeur `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Impossible d’identifier l’utilisateur connecté.| La bibliothèque n’a pas pu interroger l’utilisateur Windows actuellement connecté ou cet utilisateur n’est pas rattaché à AD ou AAD (les utilisateurs rattachés à l’espace de travail ne sont pas pris en charge). Atténuation 1 : Sur UWP, vérifiez que l’application a les fonctionnalités suivantes : Authentification en entreprise, Réseaux privés (client et serveur), Informations sur le compte d’utilisateur. Atténuation 2 : Implémentez votre propre logique pour extraire le nom d’utilisateur (par exemple, john@contoso.com) et utiliser le formulaire `AcquireTokenByIntegratedWindowsAuth` qui prend le nom d’utilisateur.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Cette méthode s’appuie sur un protocole exposé par Active Directory (AD). Si un utilisateur a été créé dans Azure Active Directory sans stockage dans AD (utilisateur « managé »), cette méthode échoue. Les utilisateurs créés dans AD et stockés par AAD (utilisateurs « fédérés ») peuvent bénéficier de cette méthode d’authentification non interactive. Atténuation des risques : Utilisez l’authentification interactive.|

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
| None    | Aucun détail supplémentaire n’est fourni. La condition peut être résolue par l’interaction de l’utilisateur pendant le flux d’authentification interactive. | Appelez AcquireTokenInteractively(). |

## <a name="net-code-example"></a>Exemple de code .NET

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
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
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

## <a name="javascript"></a>[JavaScript](#tab/javascript)

MSAL.js fournit des objets d’erreur qui résument et classifient les différents types d’erreurs courantes. Il fournit également une interface pour accéder à des détails spécifiques des erreurs, comme les messages d’erreur, afin de les traiter de manière appropriée.

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

## <a name="python"></a>[Python](#tab/python)

Dans MSAL pour Python, la plupart des erreurs sont transmises sous la forme d’une valeur renvoyée de l’appel d’API. L’erreur est représentée sous la forme d’un dictionnaire contenant la réponse JSON de la plateforme d’identité Microsoft.

* Une réponse correcte contient la clé `"access_token"`. Le format de la réponse est défini par le protocole OAuth2. Pour plus d’informations, consultez [5.1 Réponse réussie](https://tools.ietf.org/html/rfc6749#section-5.1)
* Une réponse d’erreur contient `"error"` et généralement `"error_description"`. Le format de la réponse est défini par le protocole OAuth2. Pour plus d’informations, consultez [5.2 Réponse d’erreur](https://tools.ietf.org/html/rfc6749#section-5.2)

Lorsqu’une erreur est retournée, la clé `"error_description"` contient un message explicite qui, à son tour, contient un code d’erreur de la plateforme d’identité Microsoft. Pour obtenir les divers codes d’erreur, consultez [Codes d’erreur d’authentification et d’autorisation](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

Dans MSAL pour Python, les exceptions sont rares car la plupart des erreurs sont gérées en renvoyant une valeur d’erreur. L’exception `ValueError` est levée uniquement en cas de problème lié à la façon dont vous essayez d’utiliser la bibliothèque, par exemple lorsque des paramètres d’API sont incorrects.

## <a name="java"></a>[Java](#tab/java)

Dans MSAL pour Java, il existe trois types d’exceptions : `MsalClientException`, `MsalServiceException` et `MsalInteractionRequiredException` ; tous héritant de `MsalException`.

- `MsalClientException` est levée lorsqu’une erreur se produit localement dans la bibliothèque ou l’appareil.
- `MsalServiceException` est levée lorsque le service d’émission de jeton de sécurité (STS) renvoie une réponse d’erreur ou qu’une autre erreur de mise en réseau se produit.
- `MsalInteractionRequiredException` est levée lorsque l’interaction avec l’interface utilisateur est requise pour que l’authentification aboutisse.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` expose les en-têtes HTTP retournés dans les demandes au STS. Accédez-y via `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

L’un des codes d’état courants renvoyés par MSAL pour Java lors de l’appel à `AcquireTokenSilently()` est `InvalidGrantError`. Cela signifie que l’interaction de l’utilisateur supplémentaire est nécessaire pour qu’un jeton d’authentification puisse être émis. Votre application doit rappeler la bibliothèque d’authentification, mais en mode interactif en envoyant `AuthorizationCodeParameters` ou `DeviceCodeParameters` pour les applications clientes publiques.

La plupart du temps, lorsque `AcquireTokenSilently` échoue, cela est dû au fait que le cache du jeton n’a pas de jeton correspondant à votre demande. Les jetons d’accès expirent dans une heure et `AcquireTokenSilently` tente d’en obtenir un nouveau en fonction d’un jeton d’actualisation. En termes d’OAuth2, il s’agit du flux de jeton d’actualisation. Ce flux peut également échouer pour diverses raisons, comme lorsqu’un administrateur de locataire configure des stratégies de connexion plus strictes.

Certaines conditions qui provoquent cette erreur sont faciles à résoudre par les utilisateurs. Par exemple, ils peuvent avoir besoin d’accepter des conditions d’utilisation. Ou peut-être la demande ne peut pas être satisfaite avec la configuration actuelle, car l’ordinateur doit se connecter à un réseau d’entreprise spécifique.

MSAL expose un champ `reason`, que vous pouvez utiliser pour fournir une meilleure expérience utilisateur. Par exemple, le champ `reason` peut vous amener à indiquer à l’utilisateur que son mot de passe a expiré ou qu’il devra fournir son consentement pour utiliser certaines ressources. Les valeurs prises en charge font partie de l’énumération `InteractionRequiredExceptionReason` :

| Motif | Signification | Gestion recommandée |
|---------|-----------|-----------------------------|
| `BasicAction` | La condition peut être résolue par l’interaction de l’utilisateur pendant le flux d’authentification interactive. | Appeler `acquireToken` avec des paramètres interactifs |
| `AdditionalAction` | La condition peut être résolue par une interaction corrective supplémentaire avec le système, en dehors du flux d’authentification interactive. | Appelez `acquireToken` avec des paramètres interactifs pour afficher un message expliquant l’action corrective à prendre. L’appel de l’application peut choisir de masquer les flux qui requièrent une action supplémentaire si l’utilisateur est peu susceptible d’effectuer l’action corrective. |
| `MessageOnly` | La condition ne peut pas être résolue pour l’instant. Lancez le flux d’authentification interactive pour afficher un message qui explique la condition. | Appelez `acquireToken` avec des paramètres interactifs pour afficher un message expliquant la condition. `acquireToken` renvoie une erreur `UserCanceled` lorsque l’utilisateur lit le message et ferme la fenêtre. L’application peut choisir de masquer les flux qui résultent en un message si l’utilisateur est peu susceptible de tirer parti du message. |
| `ConsentRequired`| Le consentement de l’utilisateur est manquant ou a été révoqué. |Appelez `acquireToken` avec des paramètres interactifs afin que l’utilisateur puisse donner son consentement. |
| `UserPasswordExpired` | Le mot de passe de l’utilisateur a expiré. | Appeler `acquireToken` avec un paramètre interactif pour permettre à l’utilisateur de réinitialiser son mot de passe |
| `None` |  D’autres détails sont fournis. La condition peut être résolue par l’interaction de l’utilisateur pendant le flux d’authentification interactive. | Appeler `acquireToken` avec des paramètres interactifs |

### <a name="code-example"></a>Exemple de code

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

La liste complète des erreurs MSAL pour iOS et macOS est répertoriée dans l’[énumération MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Toutes les erreurs générées par MSAL sont retournées avec le domaine `MSALErrorDomain`.

Pour les erreurs système, MSAL retourne l’erreur `NSError` originale de l’API système. Par exemple, si l’acquisition de jeton échoue en raison d’une absence de connectivité réseau, MSAL retourne une erreur avec le domaine `NSURLErrorDomain` et le code `NSURLErrorNotConnectedToInternet`.

Nous vous recommandons de gérer au moins les deux erreurs MSAL suivantes côté client :

- `MSALErrorInteractionRequired`: l’utilisateur doit effectuer une requête interactive. De nombreuses conditions peuvent entraîner cette erreur, par exemple une session d’authentification arrivée à expiration ou le besoin d’autres exigences d’authentification. Appelez l’API d’acquisition interactive de jetons MSAL pour effectuer la récupération. 

- `MSALErrorServerDeclinedScopes`: Toutes les étendues ou certaines d’entre elles ont été refusées. Décidez si vous souhaitez continuer avec uniquement les étendues accordées ou arrêter le processus de connexion.

> [!NOTE]
> L’énumération `MSALInternalError` doit être utilisée uniquement pour référence et débogage. N’essayez pas de gérer automatiquement ces erreurs au moment de l’exécution. Si votre application rencontre l’une des erreurs se trouvant sous `MSALInternalError`, vous pouvez afficher un message d’utilisateur générique expliquant ce qui s’est passé.

Par exemple, `MSALInternalErrorBrokerResponseNotReceived` signifie que l’utilisateur n’a pas effectué l’authentification et est retourné manuellement à l’application. Dans ce cas, votre application doit afficher un message d’erreur générique expliquant que l’authentification n’est pas terminée et suggérant qu’elle essaie de s’authentifier à nouveau.

L’exemple de code Objective-C suivant illustre les meilleures pratiques pour gérer certaines conditions d’erreur courantes :

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>Accès conditionnel et revendications

Quand vous obtenez des jetons silencieusement, votre application risque de recevoir des erreurs quand une [revendication d’accès conditionnel](../azuread-dev/conditional-access-dev-guide.md), comme une stratégie d’authentification multifacteur, est exigée par une API à laquelle vous essayez d’accéder.

Le modèle de gestion de cette erreur consiste à acquérir de manière interactive un jeton à l’aide de MSAL. L’acquisition interactive d’un jeton propose une invite à l’utilisateur et lui donne la possibilité de satisfaire à la stratégie d’accès conditionnel exigée.

Dans certains cas, lors de l’appel d’une API qui exige un accès conditionnel, vous pouvez recevoir une revendication dans l’erreur de la part de l’API. Par exemple, si la stratégie d’accès conditionnel consiste à utiliser un appareil managé (Intune), l’erreur est de type [AADSTS53000 : Votre appareil doit être managé pour accéder à cette ressource](reference-aadsts-error-codes.md) ou quelque chose de semblable. Dans ce cas, vous pouvez transmettre la revendication dans l’appel d’acquisition du jeton, afin que l’utilisateur soit invité à satisfaire à la stratégie appropriée.

### <a name="net"></a>.NET

Quand vous appelez une API qui exige un accès conditionnel à partir de MSAL.NET, votre application a besoin de gérer les exceptions liées aux revendications. Celles prennent la forme d’une [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) où la propriété [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) n’est pas vide.

Pour gérer la revendication, vous devez utiliser la méthode `.WithClaim()` de la classe `PublicClientApplicationBuilder`.

### <a name="javascript"></a>JavaScript

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

### <a name="msal-for-ios-and-macos"></a>MSAL pour iOS et macOS

MSAL pour iOS et macOS vous permet de demander des revendications spécifiques dans des scénarios d’acquisition de jetons en mode interactif et en mode silencieux.

Pour demander des revendications personnalisées, spécifiez `claimsRequest` dans `MSALSilentTokenParameters` ou `MSALInteractiveTokenParameters`.

Pour plus d’informations, consultez [Demander des revendications personnalisées à l’aide de MSAL pour iOS et macOS](request-custom-claims.md).

## <a name="retrying-after-errors-and-exceptions"></a>Nouvelle tentative après des erreurs et exceptions

Vous êtes censé implémenter vos propres stratégies de nouvelle tentative lors de l’appel de MSAL. MSAL effectue des appels HTTP au service AAD et des défaillances occasionnelles peuvent se produire, par exemple le réseau peut être en panne ou le serveur surchargé.  

### <a name="http-error-codes-500-600"></a>Code d’erreur HTTP 500 à 600

MSAL.NET implémente un mécanisme simple de nouvelle tentative unique pour les erreurs avec codes d’erreur HTTP compris entre 500 et 600.

### <a name="http-429"></a>HTTP 429

Lorsque le serveur de jeton de service (STS) est surchargé avec un trop grand nombre de requêtes, il renvoie une erreur HTTP 429 en indiquant, dans le champ de réponse `Retry-After`, la durée qui s’écoule avant que vous puissiez renouveler votre demande.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) fait apparaître `System.Net.Http.Headers.HttpResponseHeaders` en tant que propriété `namedHeaders`. Vous pouvez utiliser des informations supplémentaires dans le code d’erreur pour améliorer la fiabilité de vos applications. Dans le cas décrit, vous pouvez utiliser `RetryAfterproperty` (de type `RetryConditionHeaderValue`) pour calculer le moment auquel réessayer.

Voici un exemple pour une application démon utilisant le flux d’informations d’identification du client. Vous pouvez l’adapter à n’importe quelle méthode pour acquérir un jeton.

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
