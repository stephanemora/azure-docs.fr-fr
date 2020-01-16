---
title: Configurer une application web appelant des API web – Plateforme d’identités Microsoft | Azure
description: Découvrez comment configurer le code d’une application web qui appelle des API web
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e551159ad2d41af37b1f400e91680c49117498d6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423631"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Application Web appelant des API web - Configuration du code

Comme indiqué dans le [scénario de l’application web qui connecte des utilisateurs](scenario-web-app-sign-user-overview.md), l’application web utilise le [flux du code d’autorisation](v2-oauth2-auth-code-flow.md) OAuth2.0 pour connecter l’utilisateur. Ce flux comporte deux parties :

1. Demande d’un code d’autorisation. Cette partie délègue à la plateforme d’identités Microsoft un dialogue privé avec l’utilisateur. L’utilisateur se connecte et consent à utiliser des API web. Quand ce dialogue privé se termine correctement, l’application reçoit un code d’autorisation sur son URI de redirection.
1. Demande d’un jeton d’accès pour l’API en échange du code d’autorisation.

Le [scénario de l’application web qui connecte des utilisateurs](scenario-web-app-sign-user-overview.md) n’effectuait que la première partie. Découvrez ici comment modifier votre API web qui connecte des utilisateurs, afin qu’elle appelle à présent des API web.

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliothèques prenant en charge les scénarios d’application web

Les bibliothèques prenant en charge le flux du code d’autorisation pour les applications web sont les suivantes :

| Bibliothèque MSAL | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Les plateformes prises en charge sont .NET Framework et .NET Core (et non UWP, Xamarin.iOS et Xamarin.Android, car ces plateformes sont utilisées pour créer des applications clientes publiques) |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Prise en charge des applications web Python |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Prise en charge des applications web Java |

Sélectionnez l’onglet correspondant à la plateforme qui vous intéresse :

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Étant donné que le fait de permettre à un utilisateur de se connecter est délégué au middleware (intergiciel) OIDC (Open ID Connect), vous voulez vous raccorder au processus OIDC. La façon de procéder diffère selon le framework que vous utilisez.
Pour ce qui est d’ASP.NET Core, vous allez vous abonner aux événements OIDC du middleware. Le principe est le suivant :

- Vous allez laisser ASP.NET Core demander un code d’autorisation par le biais du middleware Open ID Connect. Ce faisant, ASP.NET/ ASP.NET Core permet à l’utilisateur de se connecter et de donner son consentement,
- et vous vous abonnez à la réception d’un code d’autorisation par l’application web. Cet abonnement s’effectue par le biais d’un délégué C#.
- Une fois le code d’authentification reçu, vous allez utiliser les bibliothèques MSAL pour l’échanger. Les jetons d’accès et d’actualisation obtenus sont stockés dans le cache associé. À partir de là, le cache peut être utilisé dans d’autres parties de l’application, par exemple dans des contrôleurs, pour acquérir d’autres jetons en mode silencieux.

Les extraits de code indiqués dans cet article et les suivants proviennent du [tutoriel incrémentiel sur l’application web ASP.NET Core, chapitre 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Vous pouvez vous y référer pour obtenir tous les détails d’implémentation.

> [!NOTE]
> Pour comprendre entièrement les extraits de code ci-dessous, vous devez maîtriser les [notions de base d’ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals), plus particulièrement, l’[injection de dépendances](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) et le modèle [options](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Étant donné que le fait de permettre à un utilisateur de se connecter est délégué au middleware (intergiciel) OIDC (Open ID Connect), vous voulez vous raccorder au processus OIDC. La façon de procéder diffère selon le framework que vous utilisez.
Pour ce qui est d’ASP.NET, vous allez vous abonner aux événements OIDC du middleware. Le principe est le suivant :

- Vous allez laisser ASP.NET Core demander un code d’autorisation par le biais du middleware Open ID Connect. Ce faisant, ASP.NET/ ASP.NET Core permet à l’utilisateur de se connecter et de donner son consentement,
- et vous vous abonnez à la réception d’un code d’autorisation par l’application web. Il s’agit d’un délégué C#.
- Une fois le code d’authentification reçu, vous utilisez les bibliothèques MSAL pour l’échanger. Les jetons d’accès et d’actualisation obtenus sont, ensuite, stockés dans le cache associé. À partir de là, le cache peut être utilisé dans d’autres parties de l’application, par exemple dans des contrôleurs, pour acquérir d’autres jetons en mode silencieux.

Les extraits de code indiqués dans cet article et les suivants proviennent de l’[exemple d’application web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Vous pouvez vous référer à cet exemple pour obtenir tous les détails d’implémentation.

# <a name="javatabjava"></a>[Java](#tab/java)

Les extraits de code de cet article et les suivants proviennent de [l’exemple d’application web MSAL Java qui appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp).
L’exemple permet actuellement à MSAL Java de produire l’URL du code d’autorisation et gère la navigation vers le point de terminaison d’autorisation de la Plateforme d’identités Microsoft. Il est également possible d’utiliser la sécurité Sprint pour connecter l’utilisateur. Vous pouvez vous référer à cet exemple pour obtenir tous les détails d’implémentation.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les extraits de code indiqués dans cet article et les suivants proviennent de l’[exemple d’application web MSAL.Python qui appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp).
L’exemple permet actuellement à MSAL.Python de produire l’URL du code d’autorisation et gère la navigation vers le point de terminaison d’autorisation de la plateforme d’identités Microsoft. Vous pouvez vous référer à cet exemple pour obtenir tous les détails d’implémentation.

---

## <a name="code-that-redeems-the-authorization-code"></a>Code qui échange le code d’autorisation

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

Dans ASP.NET Core, le principe est celui du fichier `Startup.cs`. Vous devez vous abonner à l’événement Open ID Connect `OnAuthorizationCodeReceived` et, à partir de cet événement, appeler la méthode MSAL.NET `AcquireTokenFromAuthorizationCode` qui a pour effet de stocker, dans le cache de jetons, le jeton d’accès pour les `scopes` demandées et un jeton d’actualisation qui permettra d’actualiser le jeton d’accès si sa date d’expiration approche, ou d’obtenir un jeton pour le compte du même utilisateur, mais pour une autre ressource.

Dans la pratique, le [tutoriel sur l’application web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) essaie de vous fournir un code réutilisable pour vos applications web.

Voici le code [Startup.cs#L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42), qui contient l’appel à la méthode `AddMicrosoftIdentityPlatformAuthentication` qui ajoute l’authentification à l’application web, puis `AddMsal` qui ajoute la possibilité d’appeler des API web. L’appel à `AddInMemoryTokenCaches` a trait au choix d’une implémentation du cache de jetons parmi celles qui sont possibles :

```csharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead` est défini dans [Constants.cs#L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5).

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Vous avez déjà étudié le contenu de `AddMicrosoftIdentityPlatformAuthentication` dans [Application web qui connecte des utilisateurs - configuration de code](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>Méthode AddMsal

Le code pour `AddMsal` se trouve dans [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

La méthode `AddMsal` garantit ce qui suit :

- L’application web ASP.NET Core demande à la fois un IDToken pour l’utilisateur et un code d’authentification (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- L’étendue `offline_access` est ajoutée. Elle est nécessaire pour que l’utilisateur accepte de laisser l’application obtenir un jeton d’actualisation.
- L’application s’abonne à l’événement `OnAuthorizationCodeReceived` OIDC, puis échange l’appel à l’aide de MSAL.NET, qui est ici encapsulé dans un composant réutilisable qui implémente `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Méthode TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync

La méthode `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` se trouve dans [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Elle garantit ce qui suit :

- ASP.NET n’essaie pas d’échanger le code d’authentification en parallèle avec MSAL.NET (`context.HandleCodeRedemption();`).
- Les revendications incluses dans IDToken sont mises à la disposition de MSAL pour calculer une clé de cache de jetons pour le compte de l’utilisateur.
- L’application MSAL.NET est instanciée si besoin.
- Le code est échangé par l’application MSAL.NET.
- Le nouveau jeton d’ID est partagé avec ASP.NET Core (pendant l’appel à `context.HandleCodeRedemption(null, result.IdToken);`). Le jeton d’accès n’est pas partagé avec ASP.NET Core. Il reste dans le cache de jetons MSAL.NET associé à l’utilisateur, où il est prêt à être utilisé dans des contrôleurs ASP.NET Core.

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>Méthode TokenAcquisition.BuildConfidentialClientApplication

Dans ASP.NET Core, la génération de l’application cliente confidentielle utilise les informations figurant dans HttpContext. Accessible par le biais de la propriété `CurrentHttpContext`, HttpContext, associé à la demande, connaît l’URL de l’application web et l’utilisateur connecté (dans un `ClaimsPrincipal`). `BuildConfidentialClientApplication` utilise également la configuration ASP.NET Core qui inclut une section « AzureAD » et qui est liée aux éléments suivants :

- la structure de données `_applicationOptions` de type [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) ;
- l’instance `azureAdOptions` de type [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) définie dans `Authentication.AzureAD.UI` ASP.Net Core. Enfin, l’application doit gérer les caches de jetons. Vous en apprendrez davantage à ce sujet dans la section suivante.

Le code de la méthode `GetOrBuildConfidentialClientApplication()` se trouve dans [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Il utilise les membres qui ont été injectés par injection de dépendances (passée dans le constructeur de TokenAcquisition dans [Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Résumé

En résumé, `AcquireTokenByAuthorizationCode` échange vraiment le code d’autorisation demandé par ASP.NET et obtient les jetons qui sont ajoutés au cache de jetons utilisateur MSAL.NET. Ensuite, ils sont utilisés dans les contrôleurs ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

La façon de gérer d’ASP.NET est similaire à celle d’ASP.NET Core, à ceci près que la configuration d’OpenIdConnect et l’abonnement à l’événement `OnAuthorizationCodeReceived` se produisent dans le fichier [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs). Vous trouverez des concepts similaires à ceux d’ASP.NET Core, à ceci près que, dans ASP.NET, vous devez spécifier RedirectUri dans [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Cette configuration est un peu moins robuste que ce qui se passe dans ASP.NET Core, car vous avez besoin de la modifier quand vous déployez votre application.

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign in, get the access token & cache it using MSAL
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Pour comprendre comment l’exemple Java obtient le code d’autorisation, consultez [Application web qui connecte des utilisateurs - configuration de code](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code). Une fois reçu par l’application, [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) délègue à la méthode `AuthHelper.processAuthenticationCodeRedirect` dans [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97), puis appelle `getAuthResultByAuthCode` :

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

La méthode `getAuthResultByAuthCode` est définie dans [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Elle crée un `ConfidentialClientApplication` MSAL et appelle `acquireToken()` avec les `AuthorizationCodeParameters` créés à partir du code d’autorisation.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Une fois que le flux du code d’autorisation est demandé comme indiqué dans [Application web qui connecte des utilisateurs - configuration de code](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code), le code d’autorisation est reçu sur la fonction `authorized` que Flask route à partir de l’URL /getAToken. Consultez [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44).

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Au lieu d’un secret client, les applications clientes confidentielles peuvent prouver leur identité à l’aide d’un certificat client ou d’une assertion de client.
L’utilisation d’assertions de client est un scénario avancé décrit en détail dans [Assertions de client](msal-net-client-assertions.md)

## <a name="token-cache"></a>Cache de jeton

> [!IMPORTANT]
> Dans les applications web (et donc les API web), l’implémentation du cache de jetons diffère d’une implémentation de cache de jetons d’applications de bureau (souvent [basée sur des fichiers](scenario-desktop-acquire-token.md#file-based-token-cache)).
> Pour des raisons de sécurité et de performance, il est important de s’assurer que pour les applications web et les API web, il doit exister un seul cache de jetons par utilisateur (par compte). Vous devez sérialiser le cache de jetons pour chaque compte.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Le tutoriel ASP.NET Core utilise l’injection de dépendances pour vous laisser déterminer l’implémentation du cache de jetons dans le fichier Startup.cs de votre application. Microsoft.Identity.Web est fourni avec plusieurs sérialiseurs de caches de jetons prédéfinis décrits dans [Sérialisation du cache de jetons](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Une possibilité intéressante consiste à choisir des [caches en mémoire distribuée](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache) ASP.NET Core :

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Pour plus d’informations sur les fournisseurs de caches de jetons, consultez aussi les [tutoriels sur les applications web ASP.NET Core | phase Caches de jetons](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dans les applications web (et API web, en fait), l’implémentation du cache de jetons diffère d’une implémentation de cache de jetons d’applications de bureau (souvent [basée sur des fichiers](scenario-desktop-acquire-token.md#file-based-token-cache)). Elle peut utiliser la session ASP.NET ou la mémoire du serveur. Observez, par exemple, comment l’implémentation du cache est accrochée après la création de l’application MSAL.NET dans [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51).

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java fournit des méthodes permettant de sérialiser et de désérialiser le cache de jetons. L’exemple Java gère la sérialisation à partir de la session, comme illustré dans la méthode `getAuthResultBySilentFlow` dans [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122).

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Les détails de la classe `SessionManagementHelper` sont fournis dans [](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="pythontabpython"></a>[Python](#tab/python)

Dans l’exemple Python, un seul cache par compte est garanti en recréant une application cliente confidentielle pour chaque demande, puis en la sérialisant dans le cache de la session Flask :

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Étapes suivantes

À ce stade, lorsque l’utilisateur se connecte, un jeton est stocké dans le cache de jetons. Nous allons voir comment il est ensuite utilisé dans d’autres parties de l’application web.

> [!div class="nextstepaction"]
> [Connexion à l’application web](scenario-web-app-call-api-sign-in.md)
