---
title: Application web appelant des API web (configuration du code) - Plateforme d’identité Microsoft
description: Découvrez comment créer une application web qui appelle des API web (configuration du code de l’application)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 391546b4d3ac9ad3674897b39284fdd16e9025a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562276"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Application Web appelant des API web - Configuration du code

Comme indiqué dans le [scénario de connexion des utilisateurs par l’application web](scenario-web-app-sign-user-overview.md), étant donné que la connexion de l’utilisateur est déléguée à l’intergiciel Open ID Connect (OIDC), vous devez vous raccorder au processus OIDC. La manière de procéder est différente selon l’infrastructure que vous avez utilisée (ici, ASP.NET et ASP.NET Core). Cependant, en fin de compte, vous vous abonnerez aux événements de l’intergiciel OIDC. Le principe est le suivant :

- Vous laissez à ASP.NET ou ASP.NET Core le soin de demander un code d’autorisation. Ce faisant, ASP.NET/ ASP.NET Core permet à l’utilisateur de se connecter et de donner son consentement,
- et vous vous abonnez à la réception d’un code d’autorisation par l’application web.
- Une fois le code d’authentification reçu, vous utilisez les bibliothèques MSAL pour accepter le code et les jetons d’accès qui en résultent, ainsi que pour actualiser le magasin de jetons dans le cache associé. À partir de là, le cache peut être utilisé dans d’autres parties de l’application pour acquérir d’autres jetons en mode silencieux.

> [!NOTE]
> Les extraits de code de cet article proviennent des exemples suivants sur GitHub, qui sont entièrement fonctionnels :
>
> - [Didacticiel incrémentiel sur les applications web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)
> - [Exemple d’application web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliothèques prenant en charge les scénarios d’application web

Les bibliothèques prenant en charge le flux de code d’autorisation pour les applications web sont les suivantes :

| Bibliothèque MSAL | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Les plateformes prises en charge sont .NET Framework et .NET Core (et non UWP, Xamarin.iOS et Xamarin.Android, car ces plateformes sont utilisées pour créer des applications clientes publiques) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Développement en cours, en préversion publique |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Développement en cours, en préversion publique |

## <a name="aspnet-core-configuration"></a>Configuration de la plateforme ASP.NET Core

Dans ASP.NET Core, le fichier `Startup.cs` connait un certain nombre de modifications. Vous devez vous abonner à l’événement Open ID Connect `OnAuthorizationCodeReceived` et, à partir de cet événement, appeler la méthode MSAL.NET `AcquireTokenFromAuthorizationCode` qui a pour effet de stocker les données dans le cache de jetons le jeton d’accès pour les `scopes` demandées et un jeton d’actualisation qui permettra d’actualiser le jeton d’accès si sa date d’expiration approche, ou d’obtenir un jeton pour le compte du même utilisateur, mais pour une autre ressource.

```CSharp
string[] scopes = new string[]{ "user.read" };
string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };
```

Les commentaires du code ci-dessous vous aideront à comprendre certains aspects difficiles liés à l’utilisation de MSAL.NET et ASP.NET Core. Tous les détails sont fournis dans le [Didacticiel incrémentiel d’application web ASP.NET Core, chapitre 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add("offline_access");
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

Dans ASP.NET Core, la génération de l’application cliente confidentielle utilise les informations figurant dans HttpContext. Ce paramètre `HttpContext` connaît l’URL de l’application web ainsi que l’utilisateur connecté (dans un élément `ClaimsPrincipal`). 

Il utilise également la configuration ASP.NET Core qui inclut une section « AzureAD » et est liée aux éléments suivants :

- la structure de données `_applicationOptions` de type [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) ;
- l’instance `azureAdOptions` de type [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) définie dans `Authentication.AzureAD.UI` ASP.Net Core. Enfin, l’application doit gérer les caches de jetons.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (UserTokenCacheProvider != null)
 {
  UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (AppTokenCacheProvider != null)
 {
  AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

Pour plus d’informations sur les fournisseurs de cache de jetons, voir les [didacticiels sur les applications web ASP.net Core | Caches de jetons](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/455d32f09f4f6647b066ebee583f1a708376b12f/2-WebApp-graph-user/2-2-TokenCache)

> [!NOTE]
> `AcquireTokenByAuthorizationCode` accepte le code d’autorisation demandé par ASP.NET et obtient les jetons qui sont ajoutés au cache de jetons utilisateur MSAL.NET. Ensuite, ils sont utilisés dans les contrôleurs ASP.NET Core.

## <a name="aspnet-configuration"></a>Configuration de la plateforme ASP.NET Core

La manière dont ASP.NET gère ses opérations est similaire, à ceci près que la configuration d’OpenIdConnect et l’abonnement à l’événement `OnAuthorizationCodeReceived` se produit dans le fichier `App_Start\Startup.Auth.cs`. Vous trouverez des concepts similaires, sauf qu’ici, vous devez spécifier l’URI de redirection dans le fichier de configuration, qui est un peu moins robuste :

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
   NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

Enfin, au lieu d’une clé secrète client, les applications clientes confidentielles peuvent prouver leur identité à l’aide d’un certificat client ou d’une assertion de client.
L’utilisation d’assertions de client est un scénario avancé décrit en détail dans [Assertions de client](msal-net-client-assertions.md)

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Cache de jetons MSAL.NET pour une application web ASP.NET (Core)

Dans les applications web (et API web, en fait), l’implémentation du cache de jetons diffère d’une implémentation de cache de jetons d’applications de bureau (souvent [basée sur des fichiers](scenario-desktop-acquire-token.md#file-based-token-cache)). Elle peut utiliser la session ASP.NET/ASP.NET Core, ou un cache Redis, une base de données, voire Azure Storage Blob. Dans l’extrait de code ci-dessus, il s’agit de l’objet de l’appel de méthode `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);`qui lie un service de cache. Ce guide basé sur des scénarios n’aborde pas en détail ce qui se produit ici, mais vous trouverez des liens ci-dessous.

> [!IMPORTANT]
> Un point essentiel à retenir est que, pour les applications et API web, il doit y avoir un seul cache de jetons par utilisateur (par compte). Vous devez sérialiser le cache de jetons pour chaque compte.

Des exemples d’utilisation de caches de jetons pour des applications et API web sont disponibles dans le [tutoriel sur les applications web ASP.NET Core](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial), à l’étape [2-2 Cache de jetons](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Pour obtenir des implémentations, examinez le dossier [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) dans la bibliothèque [microsoft-authentification-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) dans le dossier [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web).

## <a name="next-steps"></a>Étapes suivantes

À ce stade, lorsque l’utilisateur se connecte, un jeton est stocké dans le cache de jetons. Nous allons voir comment il est ensuite utilisé dans d’autres parties de l’application web.

> [!div class="nextstepaction"]
> [Connexion à l’application web](scenario-web-app-call-api-sign-in.md)
