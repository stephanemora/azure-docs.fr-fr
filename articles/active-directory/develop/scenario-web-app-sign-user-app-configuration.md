---
title: Application web qui connecte des utilisateurs (configuration de code) - Plateforme d’identités Microsoft
description: Apprendre à générer une application web qui connecte des utilisateurs (configuration de code)
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
ms.openlocfilehash: f52fc70b54c27362575bef00c39a93d13e77cc2e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175355"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Application web qui connecte des utilisateurs - configuration de code

Découvrez comment configurer le code de votre application web pour connecter les utilisateurs.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliothèques utilisées pour protéger les applications web

<!-- This section can be in an include for Web App and Web APIs -->
Les bibliothèques utilisées pour protéger une application web (et une API web) sont les suivantes :

| Plateforme | Bibliothèque | Description |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensions des modèles d’identité pour .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Directement utilisé par ASP.NET et ASP.NET Core, Microsoft Identity Extensions pour .NET propose un ensemble de DLL s’exécutant sur .NET Framework et .NET Core. À partir d’une application web ASP.NET/ASP.NET Core, vous pouvez contrôler la validation de jeton à l’aide de la classe **TokenValidationParameters** (en particulier dans certains scénarios d’éditeurs de logiciels indépendants) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL pour Java - Actuellement disponible en préversion publique |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL pour Python - Actuellement disponible en préversion publique |

Sélectionnez l’onglet correspondant à la plateforme qui vous intéresse :

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Les extraits de code dans cet article et les suivants sont extraits du [didacticiel incrémentiel de l’application web ASP.NET Core, chapitre 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Vous pouvez vous y référer pour obtenir tous les détails d’implémentation.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Les extraits de code indiqués dans cet article et les suivants proviennent de l’[exemple d’application web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Vous pouvez vous y référer pour obtenir tous les détails d’implémentation.

# <a name="javatabjava"></a>[Java](#tab/java)

Les extraits de code de cet article et les suivants proviennent de [l’exemple d’application web MSAL Java qui appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp).

Vous pouvez vous référer à cet exemple pour obtenir tous les détails d’implémentation.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les extraits de code indiqués dans cet article et les suivants proviennent de l’[exemple d’application web MSAL.Python qui appelle Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp).

Vous pouvez vous y référer pour obtenir tous les détails d’implémentation.

---

## <a name="configuration-files"></a>Fichiers de configuration

Les applications web qui connectent les utilisateurs à la plateforme d’identités Microsoft sont généralement configurées par le biais de fichiers de configuration. Les paramètres que vous devez renseigner sont les suivants :

- le cloud `Instance` si vous souhaitez que votre application s’exécute (par exemple dans les clouds nationaux) ;
- l’audience dans `tenantId` ;
- `clientId` pour votre application, tel qu’il a été copié à partir du portail Azure.

Parfois, les applications peuvent être paramétrées par `authority`, qui est la concaténation de `instance` et de `tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dans ASP.NET Core, ces paramètres se trouvent dans le fichier [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8), section AzureAD.

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

Dans ASP.NET Core, il existe un autre fichier [properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) qui contient l’URL (`applicationUrl`) et le port SSL (`sslPort`) de votre application, ainsi que divers profils.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Dans le portail Azure, les URI de réponse à inscrire dans la page **Authentification** de votre application doivent correspondre à ces URL ; autrement dit, pour les deux fichiers de configuration ci-dessus, elles seraient `https://localhost:44321/signin-oidc`, car applicationUrl est `http://localhost:3110`, mais la valeur de `sslPort` est spécifiée (44321), et `CallbackPath` est `/signin-oidc`, tel que défini dans `appsettings.json`.

De la même façon, l’URI de déconnexion devrait être défini sur `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dans ASP.NET, l’application est configurée par le biais du fichier [Web.Config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15), lignes 12-15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Dans le portail Azure, les URI de réponse à inscrire dans la page **Authentification** de votre application doivent correspondre à ces URL, c’est-à-dire `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

Dans Java, la configuration se trouve dans le fichier [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) sous `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Dans le portail Azure, les URI de réponse à inscrire dans la page **Authentification** de votre application doivent correspondre aux redirectUris définis par l’application, c’est-à-dire `http://localhost:8080/msal4jsample/secure/aad` et `http://localhost:8080/msal4jsample/graph/me`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Voici le fichier de configuration Python dans [app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py) :

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> Ce guide de démarrage rapide propose de stocker le secret client dans le fichier de configuration pour que ce soit plus simple.
> Dans votre application de production, vous allez utiliser d’autres moyens pour stocker votre secret, comme un coffre de clés ou une variable d’environnement, comme décrit dans la documentation de Flask : https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Code d’initialisation

Le code d’initialisation est différent selon la plateforme. Pour ASP.NET Core et ASP.NET, la connexion des utilisateurs est déléguée à l’intergiciel (middleware) OpenIDConnect. À l’heure actuelle, le modèle ASP.NET/ASP.NET Core génère des applications web pour le point de terminaison Azure AD v 1.0. Par conséquent, un peu de configuration est nécessaire pour les adapter au point de terminaison de la plateforme des identités Microsoft (v 2.0). Dans le cas de Java, elle est gérée par Spring avec la coopération de l’application.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dans les applications web ASP.NET Core (et les API web), l’application est protégée, car vous disposez d’un attribut `[Authorize]` sur les contrôleurs ou les actions des contrôleurs. Cet attribut vérifie que l’utilisateur est authentifié. Le code exécutant l’initialisation de l’application se trouve dans le fichier `Startup.cs` et, pour ajouter l’authentification auprès la plateforme d’identités Microsoft (anciennement Azure AD v2.0), vous devez ajouter le code suivant. Les commentaires dans le code doivent être explicites.

  > [!NOTE]
  > Si vous démarrez votre projet avec le projet web ASP.NET Core par défaut dans Visual Studio ou en utilisant `dotnet new mvc`, la méthode `AddAzureAD` est disponible par défaut, car les packages associés sont chargés automatiquement.
  > Toutefois, si vous générez un projet à partir de zéro, et que vous essayez d’utiliser le code ci-dessous, nous vous suggérons d’ajouter le package NuGet **« Microsoft.AspNetCore.Authentication.AzureAD.UI »** à votre projet pour rendre la méthode `AddAzureAD` disponible.

Le code suivant est disponible à partir de [Startup.cs#L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

La méthode d’extension `AddMicrosoftIdentityPlatformAuthentication` est définie dans [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Elle effectue les actions suivantes :

- ajout du service d’authentification ;
- configuration des options de lecture du fichier de configuration ;
- configuration des options de connexion OpenID, afin que l’autorité utilisée soit le point de terminaison de la plateforme des identités Microsoft (anciennement Azure AD v 2.0) ;
- l’émetteur du jeton est validé ;
- la revendication correspondant au nom est mappée à partir de la revendication « preferred_username » dans le jeton d’ID.

En plus de la configuration, vous pouvez spécifier en appelant `AddMicrosoftIdentityPlatformAuthentication` :

- le nom de la section de configuration (par défaut AzureAD) ;
- si vous souhaitez suivre les événements de l’intergiciel OpenIdConnect, ce qui peut vous aider à résoudre les problèmes de votre application web au cas où l’authentification ne fonctionnerait pas ; le fait de définir `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` sur `true` vous montre comment les informations sont élaborées par l’ensemble de l’intergiciel ASP.NET Core, à mesure qu’il progresse de la réponse HTTP à l’identité de l’utilisateur dans `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

La classe `AadIssuerValidator` permet à l’émetteur du jeton d’être validé dans de nombreux cas (jeton v1.0 ou v2.0, application à locataire unique ou multilocataire, ou application qui connecte les utilisateurs avec leurs comptes Microsoft personnels, dans le cloud public Azure ou les clouds nationaux). Elle est disponible à partir de [Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Le code lié à l’authentification dans l’application web ASP.NET/les API web se trouve dans le fichier [App_Start/Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61).

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

L’exemple Java utilise le framework Spring. L’application est protégée parce que vous implémentez un `Filter`, qui intercepte chaque réponse HTTP. Dans le guide de démarrage rapide de l’application web Java, ce filtre est `AuthFilter` dans `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`. Le filtre traite le flux du code d’autorisation OAuth 2.0 et par conséquent :

- vérifie si l’utilisateur est authentifié (méthode `isAuthenticated()`) ;
- si l’utilisateur n’est pas authentifié, il calcule l’URL des points de terminaison d’autorisation Azure AD et redirige le navigateur vers cet URI ;
- lorsque la réponse arrive, contenant le code d’authentification, il acquiert le jeton à l’aide de MSAL Java ;
- lorsqu’il reçoit enfin le jeton du point de terminaison de jeton (sur l’URI de redirection), l’utilisateur est connecté.

Pour plus d’informations, consultez la méthode `doFilter()` dans [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Le code de `doFilter()` est écrit dans un ordre légèrement différent, mais le flux est celui décrit.

Consultez [Plateforme d’identités Microsoft et flux du code d’autorisation OAuth 2.0](v2-oauth2-auth-code-flow.md) pour plus d’informations sur le flux du code d’autorisation déclenché par cette méthode.

# <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple Python utilise Flask. L’initialisation de Flask et de MSAL.Python s’effectue dans [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans l’article suivant, vous apprenez à déclencher la connexion et la déconnexion.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Se connecter et se déconnecter](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Se connecter et se déconnecter](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Se connecter et se déconnecter](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Se connecter et se déconnecter](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
