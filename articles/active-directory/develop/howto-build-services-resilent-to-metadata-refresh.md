---
title: 'Guide pratique : Générer des services résilients à l’actualisation des métadonnées OpenID Connect d’Azure AD | Azure'
titleSuffix: Microsoft identity platform
description: Découvrez comment vous assurer que votre application web ou API web est résiliente à l’actualisation des métadonnées OpenID Connect d’Azure AD.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/21/2021
ms.author: jmprieur
ms.reviewer: marsma, shermanouko
ms.custom: aaddev
ms.openlocfilehash: 8eb9db4d89f6d3c517afb57d6343387343ac5325
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108169397"
---
# <a name="how-to-build-services-that-are-resilient-to-azure-ads-openid-connect-metadata-refresh"></a>Guide pratique : Générer des services résilients à l’actualisation des métadonnées OpenID Connect d’Azure AD

Les API web protégées doivent valider les jetons d’accès. Les applications web valident également les jetons d’ID. La validation du jeton se compose de plusieurs parties, en vérifiant si le jeton appartient à l’application, a été émis par un fournisseur d’identité approuvé (IDP), a une durée de vie toujours valide et n’a pas été falsifié. Il peut également y avoir des validations spéciales. Par exemple, l’application doit valider la signature et le fait que les clés de signature, quand elles sont incorporées dans un jeton, sont approuvées et que le jeton n’est pas en cours de réexécution. Lorsque les clés de signature ne sont pas incorporées dans le jeton, elles doivent être extraites du fournisseur d’identité (détection ou métadonnées). Parfois, il est également nécessaire d’obtenir des clés de manière dynamique au moment de l’exécution.

Les applications web et les API web doivent actualiser les métadonnées OpenID Connect obsolètes pour qu’elles soient résilientes. Cet article vous guide sur la façon d’obtenir des applications résilientes. Il s’applique à ASP.NET Core, ASP.NET classique et Microsoft.IdentityModel.

## <a name="aspnet-core"></a>ASP.NET Core

Utilisez la dernière version de [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel) et suivez manuellement les instructions ci-dessous.

Dans la méthode `ConfigureServices` de Startup.cs, vérifiez que `JwtBearerOptions.RefreshOnIssuerKeyNotFound` a la valeur true et que vous utilisez la dernière bibliothèque Microsoft.IdentityModel.*. Cette propriété doit être activée par défaut.

```csharp
  services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
  {
    …
    // shouldn’t be necessary as it’s true by default
    options.RefreshOnIssuerKeyNotFound = true;
    …
   };
```

## <a name="aspnet-owin"></a>ASP.NET/OWIN

Microsoft vous recommande de passer à ASP.NET Core, dans la mesure où le développement s’est arrêté sur ASP.NET. 

Si vous utilisez ASP.NET classique, utilisez la dernière version de [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel).

OWIN dispose d’un intervalle d’actualisation automatique de 24 heures pour `OpenIdConnectConfiguration`. Cette actualisation n’est déclenchée que si une requête est reçue après la fin de l’intervalle de 24 heures. Pour autant que nous sachions, il n’existe aucun moyen de modifier cette valeur ou de déclencher une actualisation plus tôt, sauf en redémarrant l’application.

## <a name="microsoftidentitymodel"></a>Microsoft.IdentityModel

Si vous validez votre jeton vous-même, par exemple dans une fonction Azure, utilisez la version la plus récente de [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel) et suivez les instructions des métadonnées illustrées dans les extraits de code ci-dessous.

```csharp
ConfigurationManager<OpenIdConnectConfiguration> configManager = 
  new ConfigurationManager<OpenIdConnectConfiguration>("http://someaddress.com", 
                                                       new OpenIdConnectConfigurationRetriever());
OpenIdConnectConfiguration config = await configManager.GetConfigurationAsync().ConfigureAwait(false);
TokenValidationParameters validationParameters = new TokenValidationParameters()
{
  …
  IssuerSigningKeys = config.SigningKeys;
  …
}

JsonWebTokenHandler tokenHandler = new JsonWebTokenHandler();
result = Handler.ValidateToken(jwtToken, validationParameters);
if (result.Exception != null && result.Exception is SecurityTokenSignatureKeyNotFoundException)
{
  configManager.RequestRefresh();
  config = await configManager.GetConfigurationAsync().ConfigureAwait(false);
  validationParameters = new TokenValidationParameters()
  {
    …
    IssuerSigningKeys = config.SigningKeys,
    …
  };
  // attempt to validate token again after refresh
  result = Handler.ValidateToken(jwtToken, validationParameters);
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Validation des jetons dans une API web protégée](scenario-protected-web-api-app-configuration.md#token-validation)
