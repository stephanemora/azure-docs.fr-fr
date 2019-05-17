---
title: Application Web par appels web API (configuration de code) - plateforme d’identité Microsoft
description: Découvrez comment créer une application Web par appels web API (configuration de code de l’application)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd7f393f889facf147cf25625d5c3b20f886ddf5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784937"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Application Web par appels web API - configuration du code

Comme indiqué dans le [scénario se connecte aux utilisateurs des compléments d’application Web](scenario-web-app-sign-user-overview.md), étant donné que la connexion utilisateur est déléguée à l’ID d’ouverture se connecter (OIDC) intergiciel (middleware), vous raccordement à distance dans le processus OIDC. La manière de procéder est différente selon l’infrastructure que vous avez utilisé (ici ASP.NET et ASP.NET Core), mais au final, vous allez vous abonner aux événements OIDC intergiciel (middleware). Le principe est le suivant :

- Vous allez laisser ASP.NET ou ASP.NET core demander un code d’autorisation. En effectuant cette ASP.NET/ASP.NET core permettra l’utilisateur se connecter et donner son consentement,
- Vous allez vous abonner à la réception du code d’autorisation par l’application Web.
- Lorsque le code d’authentification est reçu, vous allez utiliser les bibliothèques MSAL pour échanger le code et les jetons d’accès résultant et le magasin de jetons dans le cache de jeton d’actualisation. À partir de là, le cache peut être utilisé dans d’autres parties de l’application pour acquérir les autres jetons en mode silencieux.

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliothèques prenant en charge les scénarios d’application Web

Les bibliothèques prenant en charge le flux de code d’autorisation pour les applications Web sont :

| Bibliothèque MSAL | Description  |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Plateformes prises en charge sont les plateformes .NET Framework et .NET Core (pas UWP, Xamarin.iOS et Xamarin.Android en tant que ces plateformes sont utilisées pour créer des applications de client public) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Développement en cours, en version préliminaire publique |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Développement en cours, en version préliminaire publique |

## <a name="aspnet-core-configuration"></a>Configuration d’ASP.NET Core

Dans ASP.NET Core, les choses se produisent dans le `Startup.cs` fichier. Vous souhaitez vous abonner à la `OnAuthorizationCodeReceived` ouvrir ID événement connect et à partir de cet événement, appelez MSAL. Méthode de NET `AcquireTokenFromAuthorizationCode` qui a pour effet de stockage dans le cache de jeton, le jeton d’accès pour les étendues demandées et un jeton d’actualisation qui sera utilisé pour actualiser le jeton d’accès lorsqu’il est proche d’expiration, ou pour obtenir un jeton pour le compte de l’utilisateur , mais pour une autre ressource.

Les commentaires dans le code ci-dessous vous aidera à comprendre certains aspects difficiles de tisser MSAL.NET et ASP.NET Core

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
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

Dans ASP.NET Core, la génération de l’application de client confidentiel utilise les informations qui se trouve dans le HttpContext. Cette HttpContext connaît l’URL pour l’application Web et que l’utilisateur connecté (dans un `ClaimsPrincipal`). Il utilise également la configuration d’ASP.NET Core, qui a une section « AzureAD », et qui est lié à la `_applicationOptions` structure de données. Enfin, l’application doit gérer les caches de jeton.

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
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` vraiment échanger le code d’autorisation demandé par ASP.NET et obtient les jetons qui sont ajoutés au cache de jeton utilisateur MSAL.NET. À partir de là, ils sont ensuite utilisés dans les contrôleurs ASP.NET Core.

## <a name="aspnet-configuration"></a>Configuration ASP.NET

La manière dont ASP.NET gère les choses est similaire, à ceci près que la configuration de OpenIdConnect et l’abonnement à la `OnAuthorizationCodeReceived` événement se produit dans le `App_Start\Startup.Auth.cs` fichier. Vous trouverez des concepts similaires, sauf qu’ici, vous devez spécifier l’URI de redirection dans le fichier de configuration, qui est un peu moins fiable :

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
  // We'll inject our own issuer validation logic below.
  ValidateIssuer = false,
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

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Cache de jeton de MSAL.NET pour une application Web ASP.NET (cœurs)

Dans les applications web (ou web API comme une question de faits), l’implémentation de cache de jeton est différente des implémentations de cache de jeton d’applications bureautiques (qui sont souvent [fichier basé](scenario-desktop-acquire-token.md#file-based-token-cache). Il peut utiliser la session ASP.NET/ASP.NET Core, ou un cache Redis, une base de données ou même stockage Blob Azure. Dans le code extrait de code ci-dessus Ceci est l’objet de la `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` appel de méthode qui lie un service de cache. Les détails de ce qui se produit ici dépasse le cadre de ce guide de scénario, mais des liens sont fournis ci-dessous.

> [!IMPORTANT]
> Un élément très important à observer est que pour les applications web et API web, il doit y avoir un cache de jeton par utilisateur (par compte). Vous devez sérialiser le cache de jetons pour chaque compte.

Exemples montrant comment utiliser les caches de jeton pour les applications Web et API web sont disponibles dans le [didacticiel d’application Web ASP.NET Core](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) dans la phase [Cache de jeton 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Pour obtenir des implémentations, examinez le dossier [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) dans la bibliothèque [microsoft-authentification-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) dans le dossier [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web).

## <a name="next-steps"></a>Étapes suivantes

À ce stade, lorsque l’utilisateur se connecte un jeton est stocké dans le cache de jetons. Nous allons voir comment il est ensuite utilisé dans d’autres parties de l’application Web.

> [!div class="nextstepaction"]
> [Connectez-vous à l’application Web](scenario-web-app-call-api-sign-in.md)
