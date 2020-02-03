---
title: Configurer une application web qui appelle des API web - Plateforme d'identités Microsoft | Azure
description: Apprenez à configurer le code d'une application web qui appelle des API web
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
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759158"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Application web qui appelle des API web : Configuration de code

Comme illustré dans le scénario [Application web qui connecte des utilisateurs](scenario-web-app-sign-user-overview.md), l'application web utilise le flux du code d'autorisation [OAuth 2.0](v2-oauth2-auth-code-flow.md) pour connecter l'utilisateur. Ce flux comporte deux étapes :

1. Demande d’un code d’autorisation. Cette partie délègue à la plateforme d'identités Microsoft un dialogue privé avec l'utilisateur. Pendant ce dialogue, l'utilisateur se connecte et consent à utiliser des API web. Lorsque le dialogue privé se termine correctement, l'application web reçoit un code d'autorisation sur son URI de redirection.
1. Demande d’un jeton d’accès pour l’API en échange du code d’autorisation.

Les scénarios [Application web qui connecte des utilisateurs](scenario-web-app-sign-user-overview.md) ne couvraient que la première étape. Vous allez ici apprendre à modifier votre application web afin qu'elle appelle les API web en plus de connecter les utilisateurs.

## <a name="libraries-that-support-web-app-scenarios"></a>Bibliothèques qui prennent en charge les scénarios relatifs aux applications web

Les bibliothèques suivantes de Microsoft Authentication Library (MSAL) prennent en charge le flux de code d'autorisation pour les applications web :

| Bibliothèque MSAL | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Prise en charge des plateformes .NET Framework et .NET Core. Les plateformes UWP (plateforme Windows universelle), Xamarin.iOS et Xamarin.Android ne sont pas prises en charge car elles sont utilisées pour créer des applications clientes publiques. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL pour Python | Prise en charge des applications web Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL pour Java | Prise en charge des applications web Java. |

Sélectionnez l'onglet correspondant à la plateforme qui vous intéresse :

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Étant donné que la connexion utilisateur est déléguée à l'intergiciel OIDC (Open ID Connect), vous devez interagir avec le processus OIDC. Le mode d'interaction dépend de l'infrastructure que vous utilisez.

Pour ASP.NET Core, vous vous abonnez aux événements OIDC de l'intergiciel :

- Vous laissez ASP.NET Core demander un code d'autorisation au moyen de l'intergiciel Open ID Connect. ASP.NET ou ASP.NET Core permet à l'utilisateur de se connecter et de donner son consentement.
- Vous vous abonnez à l'application web pour recevoir le code d'autorisation. Cet abonnement s'effectue par le biais d'un délégué C#.
- Une fois le code d'authentification reçu, vous utilisez les bibliothèques MSAL pour l'échanger. Les jetons d'accès et d'actualisation obtenus sont stockés dans le cache associé. Le cache peut être utilisé dans d'autres parties de l'application, comme les contrôleurs, pour acquérir d'autres jetons en mode silencieux.

Les exemples de code présentés dans cet article et le suivant proviennent du [tutoriel incrémentiel sur l'application web ASP.NET Core, chapitre 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Vous pouvez vous référer à ce didacticiel pour obtenir tous les détails d’implémentation.

> [!NOTE]
> Pour bien comprendre les exemples de code présentés ici, vous devez maîtriser les [notions de base d'ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals), en particulier l'[injection de dépendances](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) et le modèle [options](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Étant donné que la connexion utilisateur est déléguée à l'intergiciel OIDC (Open ID Connect), vous devez interagir avec le processus OIDC. Le mode d'interaction dépend de l'infrastructure que vous utilisez.

Pour ASP.NET, vous vous abonnez aux événements OIDC de l'intergiciel :

- Vous laissez ASP.NET Core demander un code d'autorisation au moyen de l'intergiciel Open ID Connect. ASP.NET ou ASP.NET Core permet à l'utilisateur de se connecter et de donner son consentement.
- Vous vous abonnez à l'application web pour recevoir le code d'autorisation. Cet abonnement s'effectue par le biais d'un délégué C#.
- Une fois le code d'authentification reçu, vous utilisez les bibliothèques MSAL pour l'échanger. Les jetons d'accès et d'actualisation obtenus sont stockés dans le cache associé. Le cache peut être utilisé dans d'autres parties de l'application, comme les contrôleurs, pour acquérir d'autres jetons en mode silencieux.

Les exemples de code présentés dans cet article et le suivant proviennent de l'[exemple d'application web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Vous pouvez vous référer à cet exemple pour obtenir tous les détails d'implémentation.

# <a name="javatabjava"></a>[Java](#tab/java)

Les exemples de code présentés dans cet article et le suivant proviennent de l'exemple d'[application web Java qui appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp). Cet exemple d'application web utilise MSAL pour Java.
L'exemple permet actuellement à MSAL pour Java de produire l'URL du code d'autorisation et gère la navigation vers le point de terminaison d'autorisation de la plateforme d'identités Microsoft. Il est également possible d'utiliser la sécurité Sprint pour connecter l'utilisateur. Vous pouvez vous référer à cet exemple pour obtenir tous les détails d'implémentation.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les exemples de code présentés dans cet article et le suivant proviennent de l'exemple d'[application web Python qui appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp). Cet exemple d'application web utilise MSAL.Python.
L'exemple permet actuellement à MSAL.Python de produire l'URL du code d'autorisation et gère la navigation vers le point de terminaison d'autorisation de la plateforme d'identités Microsoft. Vous pouvez vous référer à cet exemple pour obtenir tous les détails d'implémentation.

---

## <a name="code-that-redeems-the-authorization-code"></a>Code qui échange le code d’autorisation

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

En ce qui concerne ASP.NET Core, dans le fichier `Startup.cs`, abonnez-vous à l'événement OpenID Connect `OnAuthorizationCodeReceived`. À partir de cet événement, appelez la méthode MSAL.NET `AcquireTokenFromAuthorizationCode`. Cette méthode stocke les jetons suivants dans le cache de jetons :

- *Jeton d'accès* des `scopes` demandés.
- *Jeton d'actualisation*. Ce jeton sera utilisé pour actualiser le jeton d'accès lorsqu'il arrivera à expiration ou pour obtenir un autre jeton au nom du même utilisateur mais pour une ressource différente.

Le [tutoriel sur l'application web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) vous fournit un code réutilisable pour vos applications web.

Vous trouverez ci-dessous le code de [Startup.cs#L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Il comprend les appels à :

- La méthode `AddMicrosoftIdentityPlatformAuthentication`, qui ajoute l'authentification à l'application web
- La méthode `AddMsal`, qui ajoute la possibilité d'appeler des API web
- La méthode `AddInMemoryTokenCaches`, qui consiste à choisir une implémentation de cache de jetons

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead` est défini dans [Constants.cs#L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5) :

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
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
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

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
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

- L'application web ASP.NET Core demande à la fois un jeton d'identification pour l'utilisateur et un code d'authentification (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- L'étendue `offline_access` est ajoutée. Cette étendue permet d'obtenir le consentement de l'utilisateur afin que l'application se procure un jeton d'actualisation.
- L'application s'abonne à l'événement OIDC `OnAuthorizationCodeReceived`, puis échange l'appel à l'aide de MSAL.NET, qui est ici encapsulé dans un composant réutilisable qui implémente `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>Méthode TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync

La méthode `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` se trouve dans [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Elle garantit ce qui suit :

- ASP.NET n'essaie pas d'échanger le code d'authentification en parallèle de MSAL.NET (`context.HandleCodeRedemption();`).
- Les revendications incluses dans le jeton d'identification sont mises à la disposition de MSAL afin de calculer une clé de cache de jetons pour le compte de l'utilisateur.
- Si nécessaire, une instance de l'application MSAL.NET est créée.
- Le code est échangé par l'application MSAL.NET.
- Le nouveau jeton d'identification est partagé avec ASP.NET Core pendant l'appel à `context.HandleCodeRedemption(null, result.IdToken);`. Le jeton d’accès n’est pas partagé avec ASP.NET Core. Il reste dans le cache de jetons MSAL.NET associé à l’utilisateur, où il est prêt à être utilisé dans des contrôleurs ASP.NET Core.

Voici le code approprié pour `TokenAcquisition` :

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
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
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

Dans ASP.NET Core, la génération de l'application cliente confidentielle utilise les informations figurant dans `HttpContext`. Le contexte `HttpContext` associé à la demande est accessible à l'aide de la propriété `CurrentHttpContext`. `HttpContext` contient des informations sur l'URL de l'application web et sur l'utilisateur connecté (dans un élément `ClaimsPrincipal`). 

La méthode `BuildConfidentialClientApplication` utilise également la configuration ASP.NET Core. La configuration comporte une section « AzureAD », et est également liée aux deux éléments suivants :

- la structure de données `_applicationOptions` de type [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) ;
- l'instance `azureAdOptions` de type [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) définie dans le paramètre `Authentication.AzureAD.UI` d'ASP.Net Core.

Enfin, l’application doit gérer les caches de jetons. La section suivante vous permettra d'en apprendre plus à ce sujet.

Le code de la méthode `GetOrBuildConfidentialClientApplication()` se trouve dans [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Il utilise les membres qui ont été injectés par injection de dépendances (transmission dans le constructeur de `TokenAcquisition` dans [Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Voici le code de `GetOrBuildConfidentialClientApplication` :

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
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

`AcquireTokenByAuthorizationCode` est vraiment la méthode qui utilise le code d'autorisation demandé par ASP.NET et qui obtient les jetons ajoutés au cache de jetons utilisateur MSAL.NET. À partir du cache, les jetons sont ensuite utilisés dans les contrôleurs ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Le mode de gestion d'ASP.NET est semblable à celui d'ASP.NET Core, à ceci près que la configuration d'OpenID Connect et l'abonnement à l'événement `OnAuthorizationCodeReceived` se produisent dans le fichier [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs). De même, les concepts sont semblables à ceux d'ASP.NET Core, sauf que dans ASP.NET, vous devez spécifier le `RedirectUri` dans [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Cette configuration est un peu moins robuste que celle d'ASP.NET Core car il est nécessaire de la modifier lors du déploiement de l'application.

Voici le code de Startup.Auth.cs :

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
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
      // Upon successful sign-in, get the access token and cache it by using MSAL.
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

Pour en savoir plus sur la façon dont l'exemple Java se procure le code d'autorisation, consultez [Application web qui connecte des utilisateurs : configuration de code](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code). Une fois que l'application a reçu le code, [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) :

1. Délègue à la méthode `AuthHelper.processAuthenticationCodeRedirect` dans [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Appelle `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

La méthode `getAuthResultByAuthCode` est définie dans [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Elle crée un `ConfidentialClientApplication` MSAL, puis appelle `acquireToken()` avec les paramètres `AuthorizationCodeParameters` créés à partir du code d'autorisation.

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

Le flux de code d'autorisation est demandé comme indiqué dans [Application Web qui connecte des utilisateurs : configuration de code](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). La fonction `authorized` reçoit alors le code et Flask l'achemine à partir de l'URL `/getAToken`. Pour accéder au contexte complet de ce code, consultez [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) :

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Au lieu d'utiliser un secret client, l'application cliente confidentielle peut également prouver son identité à l'aide d'un certificat client ou d'une assertion de client.
L'utilisation d'assertions de client est un scénario avancé décrit en détail dans [Assertions de client](msal-net-client-assertions.md).

## <a name="token-cache"></a>Cache de jeton

> [!IMPORTANT]
> L'implémentation du cache de jetons des applications ou API web est différente de l'implémentation relative aux applications de bureau, qui est souvent [basée sur des fichiers](scenario-desktop-acquire-token.md#file-based-token-cache).
> En termes de sécurité et de performances, pour les applications et API web, vous devez impérativement disposer d'un cache de jetons par compte d'utilisateur. Vous devez sérialiser le cache de jetons pour chaque compte.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Le tutoriel ASP.NET Core utilise l’injection de dépendances pour vous laisser déterminer l’implémentation du cache de jetons dans le fichier Startup.cs de votre application. Microsoft.Identity.Web est fourni avec des sérialiseurs de cache de jetons prédéfinis décrits dans [Sérialisation du cache de jetons](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Une possibilité intéressante consiste à choisir des [caches en mémoire distribuée](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache) ASP.NET Core :

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Pour plus d'informations sur les fournisseurs de cache de jetons, consultez aussi les [tutoriels sur les applications web ASP.NET Core | phase Caches de jetons](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

L'implémentation du cache de jetons des applications ou API web est différente de l'implémentation relative aux applications de bureau, qui est souvent [basée sur des fichiers](scenario-desktop-acquire-token.md#file-based-token-cache).

L'implémentation des applications web peut utiliser la session ASP.NET ou la mémoire du serveur. Observez, par exemple, comment l'implémentation du cache est accrochée après la création de l'application MSAL.NET dans [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51) :

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

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java fournit des méthodes permettant de sérialiser et de désérialiser le cache de jetons. L'exemple Java gère la sérialisation à partir de la session, comme illustré par la méthode `getAuthResultBySilentFlow` dans [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122) :

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

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Les détails de la classe `SessionManagementHelper` sont fournis dans l'[exemple MSAL pour Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="pythontabpython"></a>[Python](#tab/python)

Dans l'exemple Python, un seul cache par compte est garanti en recréant une application cliente confidentielle pour chaque demande, puis en la sérialisant dans le cache de la session Flask :

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

À ce stade, lorsque l'utilisateur se connecte, un jeton est stocké dans le cache de jetons. Nous allons voir comment il est ensuite utilisé dans d'autres parties de l'application web.

> [!div class="nextstepaction"]
> [Application web qui appelle des API web : Supprimer les comptes du cache lors de la déconnexion globale](scenario-web-app-call-api-sign-in.md)
