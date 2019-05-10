---
title: Application Web qui connecte les utilisateurs (configuration de code) - plateforme d’identité Microsoft
description: Découvrez comment créer une application web qui connecte les utilisateurs (configuration de code)
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
ms.openlocfilehash: b2204fe3e08b3c4b909ddc8b7ade4cec219d34fb
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406630"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Application Web qui se connecte les utilisateurs - configuration du code

Découvrez comment configurer le code de votre application Web que les utilisateurs se connecte.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliothèques utilisées pour protéger les applications Web

<!-- This section can be in an include for Web App and Web APIs -->
Les bibliothèques utilisées pour protéger une application Web (et une API Web) sont :

| Plateforme | Bibliothèque | Description  |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensions de modèle d’identité pour .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Directement utilisé par ASP.NET et ASP.NET Core, Microsoft Identity Extensions à .NET propose un ensemble de DLL en cours d’exécution sur .NET Framework et .NET Core. À partir d’une application Web de Core ASP.NET/ASP.NET, vous pouvez contrôler à l’aide de la validation du jeton la **TokenValidationParameters** classe (en particulier dans certains scénarios d’éditeurs de logiciels indépendants) |

## <a name="aspnet-core-configuration"></a>Configuration d’ASP.NET Core

### <a name="application-configuration-files"></a>Fichiers de configuration d’application

Dans ASP.NET Core, une application de signature dans utilisateurs Web avec la plateforme Microsoft identity est configuré via le `appsettings.json` fichier. Dont vous avez besoin pour remplir les valeurs suivantes :

- le cloud `Instance` si vous souhaitez que votre application s’exécute dans les clouds nationaux
- l’audience `tenantId`
- le `clientId` pour votre application, comme copié à partir du portail Azure.

```JSon
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
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

Dans ASP.NET Core, il existe un autre fichier qui contient l’URL (`applicationUrl`) et le Port SSL (`sslPort`) pour votre application, ainsi que divers profils.

```JSon
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

Dans le portail Azure, la réponse URI dont vous avez besoin pour inscrire dans le **authentification** page de votre application doit correspondre à ces URL ; autrement dit, pour les deux fichiers de configuration ci-dessus, il serait `https://localhost:44321/signin-oidc` comme l’applicationUrl est `http://localhost:3110` mais le `sslPort` est spécifié (44321) et le `CallbackPath` est `/signin-oidc` tel que défini dans le `appsettings.json`.
  
Dans la même façon, l’URI de déconnexion est définie sur `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Code d’initialisation

Dans les applications Web ASP.NET Core (et les API Web), le code effectuant l’initialisation de l’application se trouve dans le `Startup.cs` fichier, et pour ajouter l’authentification avec Microsoft Identity platform (anciennement Azure AD) v2.0, vous devez ajouter le code suivant. Les commentaires dans le code doivent être explicites.

```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Configuration ASP.NET

Dans ASP.NET, l’application est configurée via le `Web.Config` fichier

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

Le code lié à l’authentification dans ASP.NET Web application / API Web se trouve dans le `App_Start/Startup.Auth.cs` fichier.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
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

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Se connecter et se déconnecter](scenario-web-app-sign-user-sign-in.md)
