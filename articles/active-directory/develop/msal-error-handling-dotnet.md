---
title: Gérer les erreurs et les exceptions dans MSAL.NET
titleSuffix: Microsoft identity platform
description: Découvrez comment gérer les erreurs et exceptions, les défis relatifs aux revendications d’accès conditionnel et les nouvelles tentatives dans MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584042"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>Gérer les erreurs et les exceptions dans MSAL.NET

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>Gestion des erreurs dans MSAL.NET

Quand vous traitez les exceptions .NET, vous pouvez utiliser le type d’exception lui-même et le membre `ErrorCode` pour les distinguer. Les valeurs de `ErrorCode` sont des constantes de type [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

Vous pouvez également examiner les champs de [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) et [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Si l’exception [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) est levée, consultez la liste [Codes d’erreur d’authentification et d’autorisation](reference-aadsts-error-codes.md) pour vérifier si le code y est répertorié.

### <a name="common-net-exceptions"></a>Exceptions .NET courantes

Voici les exceptions courantes pouvant être levées et certaines atténuations possibles :  

| Exception | Code d'erreur | Limitation des risques|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001 : L’utilisateur ou l’administrateur n’a pas donné son consentement pour utiliser l’application portant l’ID « {appId} » et le nom « {appName} ». Envoyez une demande d’autorisation interactive pour cet utilisateur et cette ressource.| Obtenez d’abord le consentement de l’utilisateur. Si vous n’utilisez pas .NET Core (qui n’a pas d’interface utilisateur web), appelez (une seule fois) `AcquireTokeninteractive`. Si vous utilisez .NET Core ou que vous ne souhaitez pas effectuer une `AcquireTokenInteractive`, l’utilisateur peut accéder à une URL pour donner son consentement : `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`. pour appeler `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079 : L’utilisateur est obligé d’utiliser [l’authentification multifacteur (MFA)](../authentication/concept-mfa-howitworks.md).| Il n’y a pas d’atténuation. Si l’authentification multifacteur est configurée pour votre locataire et qu’Azure Active Directory (AAD) décide de l’appliquer, revenez à un flux interactif, comme `AcquireTokenInteractive` ou `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010 : Le type d’autorisation n’est pas pris en charge sur les points de terminaison */common* ou */consumers*. Utilisez le point de terminaison */organizations* ou propre au locataire. Vous avez utilisé */common*.| Comme expliqué dans le message d’Azure AD, l’autorité doit avoir un locataire ou utiliser */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002 : Le corps de la requête doit contenir le paramètre : `client_secret or client_assertion`.| Cette exception peut être levée si votre application n’a pas été inscrite en tant qu’application cliente publique dans Azure AD. Dans le Portail Azure, modifiez le manifeste de votre application et affectez à `allowPublicClient` la valeur `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Impossible d’identifier l’utilisateur connecté.| La bibliothèque n’a pas pu interroger l’utilisateur Windows actuellement connecté ou cet utilisateur n’est pas rattaché à AD ou à Azure AD (les utilisateurs rattachés à l’espace de travail ne sont pas pris en charge). Atténuation 1 : Sur UWP, vérifiez que l’application a les fonctionnalités suivantes : Authentification en entreprise, Réseaux privés (client et serveur), Informations sur le compte d’utilisateur. Atténuation 2 : Implémentez votre propre logique pour extraire le nom d’utilisateur (par exemple, john@contoso.com) et utiliser le formulaire `AcquireTokenByIntegratedWindowsAuth` qui prend le nom d’utilisateur.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Cette méthode s’appuie sur un protocole exposé par Active Directory (AD). Si un utilisateur a été créé dans Azure AD sans s’appuyer sur AD (utilisateur « managé »), cette méthode échoue. Les utilisateurs créés dans AD et s’appuyant sur Azure AD (utilisateurs « fédérés ») peuvent bénéficier de cette méthode d’authentification non interactive. Atténuation des risques : Utilisez l’authentification interactive.|

### `MsalUiRequiredException`

L’un des codes d’état courants retournés par MSAL.NET lors de l’appel à `AcquireTokenSilent()` est `MsalError.InvalidGrantError`. Ce code d’état signifie que l’application doit rappeler la bibliothèque d’authentification, mais en mode interactif (AcquireTokenInteractive ou AcquireTokenByDeviceCodeFlow pour les applications clientes publiques, présence d’un test dans les applications web). Cela est dû au fait que l’interaction de l’utilisateur supplémentaire est nécessaire pour que le jeton d’authentification puisse être émis.

La plupart du temps, lorsque `AcquireTokenSilent` échoue, cela est dû au fait que le cache du jeton n’a pas de jeton correspondant à votre demande. Les jetons d’accès expirent dans un délai d’une heure et `AcquireTokenSilent` tente d’en extraire un nouveau en fonction d’un jeton d’actualisation (en termes de OAuth2, il s’agit du flux « Jeton d’actualisation »). Ce flux peut également échouer pour diverses raisons, par exemple si un administrateur de locataire configure des stratégies de connexion plus strictes. 

L’interaction vise à faire en sorte que l’utilisateur effectue une action. Certaines de ces conditions sont faciles à résoudre par les utilisateurs (par exemple, accepter les Conditions d’utilisation d’un simple clic), et d’autres ne peuvent pas être résolues avec la configuration actuelle (par exemple, l’ordinateur en question doit se connecter à un réseau d’entreprise spécifique). Certaines aident l’utilisateur à configurer l’authentification multifacteur ou à installer Microsoft Authenticator sur son appareil.

### <a name="msaluirequiredexception-classification-enumeration"></a>Énumération de classification `MsalUiRequiredException`

MSAL expose un champ `Classification`, que vous pouvez lire pour fournir une meilleure expérience utilisateur. Par exemple, pour indiquer à l’utilisateur que son mot de passe a expiré ou qu’il devra fournir son consentement pour utiliser certaines ressources. Les valeurs prises en charge font partie de l’énumération `UiRequiredExceptionClassification` :

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
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Quand vous appelez une API qui exige un accès conditionnel à partir de MSAL.NET, votre application a besoin de gérer les exceptions liées aux revendications. Celles prennent la forme d’une [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) où la propriété [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) n’est pas vide.

Pour gérer la revendication, vous devez utiliser la méthode `.WithClaim()` de la classe `PublicClientApplicationBuilder`.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>Code d’erreur HTTP 500 à 600

MSAL.NET implémente un mécanisme simple de nouvelle tentative unique pour les erreurs avec codes d’erreur HTTP compris entre 500 et 600.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) fait apparaître `System.Net.Http.Headers.HttpResponseHeaders` en tant que propriété `namedHeaders`. Vous pouvez utiliser des informations supplémentaires dans le code d’erreur pour améliorer la fiabilité de vos applications. Dans le cas décrit, vous pouvez utiliser `RetryAfterproperty` (de type `RetryConditionHeaderValue`) pour calculer le moment auquel réessayer.

Voici un exemple pour une application démon utilisant le flux d’informations d’identification du client. Vous pouvez l’adapter à n’importe quelle méthode pour acquérir un jeton.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
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
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>Étapes suivantes

Envisagez l’activation de la [journalisation dans MSAL.NET](msal-logging-dotnet.md) pour vous aider à diagnostiquer et à déboguer les problèmes.
