---
title: Acquérir un jeton pour appeler une API web en utilisant l’authentification Windows intégrée (appareil de bureau) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment générer une application de bureau qui appelle des API web en vue d’acquérir un jeton pour l’application en utilisant l’authentification Windows intégrée
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 67b2c1ac6b9a73444639bb174eacfbf5d685ce92
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234829"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-integrated-windows-authentication"></a>Appareil de bureau qui appelle des API web : acquérir un jeton en utilisant l’authentification Windows intégrée

Pour connecter un utilisateur de domaine sur une machine jointe à Azure AD ou à un domaine, utilisez l’authentification Windows intégrée (IWA).

## <a name="constraints"></a>Contraintes

- L’authentification Windows intégrée n’est disponible que pour les utilisateurs *fédérés+* , c’est-à-dire les utilisateurs créés dans Active Directory et reposant sur Azure AD. Les utilisateurs créés directement dans Azure AD sans appui Active Directory, appelés utilisateurs *managés*, ne peuvent pas utiliser ce flux d’authentification. Cette restriction ne concerne pas le flux de nom d’utilisateur et de mot de passe.
- L’authentification Windows n’ignore pas l’[authentification multifacteur (MFA)](../authentication/concept-mfa-howitworks.md). Si l’authentification MFA est configurée, IWA peut échouer en cas de demande MFA exigée, car MFA a besoin d’une interaction utilisateur.

    L’authentification IWA est non interactive, mais MFA nécessite l’interactivité avec l’utilisateur. Vous n’avez pas le contrôle lorsque le fournisseur d’identité demande l’exécution de MFA, l’administrateur de locataire, si. D’après ce que nous avons pu observer, l’authentification MFA est demandée lorsque vous vous connectez depuis un autre pays/région alors que vous n’êtes pas connecté à un réseau d’entreprise via un VPN, et parfois même lorsque vous êtes connecté via un VPN. Ne vous attendez pas à un ensemble déterministe de règles. Azure AD utilise l’intelligence artificielle pour apprendre en continu à déterminer si l’authentification MFA est exigée. Ayez recours à une invite utilisateur de secours, comme une authentification interactive ou un flux de code d’appareil, si l’IWA échoue.

- L’autorité transmise dans `PublicClientApplicationBuilder` doit être :
  - Avec locataire sous le format `https://login.microsoftonline.com/{tenant}/`, dans lequel `tenant` est le GUID qui représente l’ID de locataire ou un domaine associé au locataire.
  - Pour les comptes professionnels et scolaires : `https://login.microsoftonline.com/organizations/`.
  - Les comptes Microsoft personnels ne sont pas pris en charge. Vous ne pouvez pas utiliser les locataires /common ou /consumers.

- Étant donné que l’authentification Windows intégrée est un flux silencieux :
  - L’utilisateur de votre application doit avoir précédemment donné son consentement pour utiliser l’application.
  - Ou bien, l’administrateur de locataires doit avoir préalablement consenti à ce que tous les utilisateurs dans le locataire utilisent l’application.
  - En d’autres termes :
    - En tant que développeur, vous avez sélectionné le bouton **Accorder** pour vous-même, dans le portail Azure.
    - Ou bien, un administrateur de locataire a sélectionné le bouton **Accorder/révoquer le consentement administrateur pour {domaine du locataire}** dans l’onglet **Autorisations de l’API** lors de l’inscription de l’application. Pour plus d’informations, consultez [Ajouter des autorisations pour accéder à votre API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).
    - Ou bien, vous avez fourni un moyen aux utilisateurs de donner leur consentement pour l’utilisation de l’application. Pour plus d’informations, consultez [Demande de consentement d’utilisateur individuel](./v2-permissions-and-consent.md#requesting-individual-user-consent).
    - Ou bien, vous avez fourni un moyen à l’administrateur de locataire de donner son consentement pour l’utilisation de l’application. Pour plus d’informations, consultez [Consentement administrateur](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

- Ce flux est activé pour les applications .NET Desktop, .NET Core et UWP.

Pour plus d’informations sur le consentement, consultez [Autorisations et consentement de la plateforme d’identités Microsoft](./v2-permissions-and-consent.md).

## <a name="learn-how-to-use-it"></a>Découvrez son utilisation

# <a name="net"></a>[.NET](#tab/dotnet)

Dans MSAL.NET, utilisez ceci :

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Vous n’avez normalement besoin que d’un paramètre (`scopes`). En fonction de la façon dont votre administrateur Windows a configuré les stratégies, les applications sur votre machine Windows peuvent ne pas être autorisées à rechercher l’utilisateur connecté. Dans ce cas, utilisez une deuxième méthode, `.WithUsername()`, et passez le nom d’utilisateur de l’utilisateur connecté sous un format UPN, par exemple, `joe@contoso.com`.

L’exemple suivant met en évidence le cas le plus courant, avec des explications sur le genre d’exceptions que vous pouvez obtenir et les solutions d’atténuation.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }

 Console.WriteLine(result.Account.Username);
}
```

Pour la liste des modificateurs possibles sur AcquireTokenByIntegratedWindowsAuthentication, voir [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Voici un extrait des [exemples de développement MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Ce flux ne s’applique pas à macOS.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Ce flux n’est pas encore pris en charge dans MSAL Node.

# <a name="python"></a>[Python](#tab/python)

Ce flux n’est pas encore pris en charge dans MSAL Python.

---
## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Appeler une API web à partir de l’appareil de bureau](scenario-desktop-call-api.md).
