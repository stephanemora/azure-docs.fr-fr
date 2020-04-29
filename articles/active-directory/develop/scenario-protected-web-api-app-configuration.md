---
title: Configurer des applications API web protégées | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment créer une API web protégée et configurer le code de votre application.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882469"
---
# <a name="protected-web-api-code-configuration"></a>API web protégée : Configuration de code

Pour configurer le code de votre API web protégée, vous devez comprendre ce qui suit :

- Ce qui définit les API comme étant protégées.
- Comment configurer un jeton du porteur.
- Comment valider le jeton.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Qu’est-ce qui définit les API ASP.NET et ASP.NET Core comme étant protégées ?

Comme les applications web, les API web ASP.NET et ASP.NET Core sont protégées parce que leurs actions de contrôleur sont préfixées par l’attribut **[Authorize]** . Les actions de contrôleur peuvent être appelées uniquement si l’API est appelée avec une identité autorisée.

Considérez les questions suivantes :

- Seule une application peut appeler une API web. Comment l’API connaît-elle l’identité de l’application qui l’appelle ?
- Si l’application appelle l’API au nom d’un utilisateur, quelle est l’identité de celui-ci ?

## <a name="bearer-token"></a>Jeton du porteur

Le jeton du porteur qui est défini dans l’en-tête lorsque l’application est appelée contient des informations sur l’identité de l’application. Il contient également des informations sur l’utilisateur, sauf si l’application web accepte les appels de service à service à partir d’une application démon.

Voici un exemple de code C# qui présente un client appelant l’API après avoir acquis un jeton de Microsoft Authentication Library pour .NET (MSAL.NET) :

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Une application cliente demande le jeton du porteur au point de terminaison de la plateforme d’identités Microsoft *pour l’API web*. L’API web est la seule application qui doive vérifier le jeton et afficher les revendications qu’il contient. Les applications clientes ne doivent jamais tenter d’inspecter les revendications contenues dans les jetons
>
> À l’avenir, l’API web pourrait exiger que le jeton soit chiffré. Cette exigence empêcherait l’accès des applications clientes capables d’afficher les jetons d’accès.

## <a name="jwtbearer-configuration"></a>Configuration de JwtBearer

Cette section décrit comment configurer un jeton du porteur.

### <a name="config-file"></a>Fichier de configuration

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Initialisation du code

Quand une application est appelée sur une action de contrôleur qui contient un attribut **[Authorize]** , ASP.NET et ASP.NET Core extraient le jeton d’accès à partir du jeton du porteur dans l’en-tête d’autorisation. Le jeton d’accès est ensuite transmis à l’intergiciel JwtBearer qui appelle Microsoft IdentityModel Extensions pour .NET.

Dans ASP.NET Core, cet intergiciel est initialisé dans le fichier Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

L’intergiciel (middleware) est ajouté à l’API web par cette instruction :

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 Actuellement, les modèles ASP.NET Core créent des API web Azure Active Directory (Azure AD) qui connectent des utilisateurs de votre organisation ou d’une autre organisation. Elles ne connectent pas d’utilisateurs à l’aide de comptes personnels. Cependant, vous pouvez modifier les modèles pour utiliser le point de terminaison de la plateforme d’identités Microsoft en ajoutant ce code à Startup.cs :

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

L’extrait de code précédent est extrait du tutoriel incrémentiel de l’API web ASP.NET Core, dans [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). La méthode **AddProtectedWebApi**, qui en fait plus que ce que montre l’extrait de code, est appelée à partir de Startup.cs.

## <a name="token-validation"></a>Validation du jeton

Dans l’extrait de code précédent, l’intergiciel JwtBearer, tout comme le middleware OpenID Connect dans des applications web, valide le jeton en fonction de la valeur de `TokenValidationParameters`. Le jeton est déchiffré si nécessaire, les revendications extraites et la signature vérifiée. L’intergiciel valide ensuite le jeton en vérifiant les points suivants :

- Audience: Le jeton est destiné à l’API web.
- Sub : Il a été émis pour une application autorisée à appeler l’API web.
- Émetteur : Il a été émis par un service d’émission de jeton de sécurité (STS) approuvé.
- Expiration : Sa durée de vie s’inscrit dans la plage valide.
- Signature : Il n’a pas été falsifié.

Il peut également y avoir des validations spéciales. Par exemple, il est possible de valider le fait que les clés de signature, quand elles sont incorporées dans un jeton, sont approuvées et que le jeton n’est pas en cours de réexécution. Enfin, certains protocoles nécessitent des validations spécifiques.

### <a name="validators"></a>Validateurs

Les étapes de validation sont capturées dans des validateurs qui sont fournis par la bibliothèque open source [Microsoft IdentityModel Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet). Les validateurs sont définis dans le fichier source de la bibliothèque [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Ce tableau décrit les validateurs :

| Validateur | Description |
|---------|---------|
| **ValidateAudience** | S’assure que le jeton est destiné à l’application qui le valide pour vous. |
| **ValidateIssuer** | S’assure que le jeton a été émis par un STS approuvé, ce qui signifie qu’il s’agit d’une personne à qui vous faites confiance. |
| **ValidateIssuerSigningKey** | S’assure que l’application qui valide le jeton approuve la clé utilisée pour signer le jeton Il existe un cas particulier où la clé est incorporée dans le jeton. Cependant, ce cas ne se produit généralement pas. |
| **ValidateLifetime** | S’assure que le jeton est encore ou déjà valide. Le validateur vérifie si la durée de vie du jeton est comprise dans la plage spécifiée par les revendications **notbefore** et **expires**. |
| **ValidateSignature** | S’assure que le jeton n’a pas été falsifié. |
| **ValidateTokenReplay** | S’assure que le jeton n’est pas en cours de réexécution Il existe un cas particulier pour certains protocoles d’utilisation unique. |

Les validateurs sont associés aux propriétés de la classe **TokenValidationParameters**. Les propriétés sont initialisées à partir de la configuration ASP.NET et ASP.NET Core.

Dans la plupart des cas, vous n’avez pas besoin de modifier les paramètres. Les applications qui ne sont pas à locataire unique sont des exceptions. Ces applications web acceptent des utilisateurs de toute organisation ou de comptes Microsoft personnels. Dans ce cas, les émetteurs doivent être validés.

## <a name="token-validation-in-azure-functions"></a>Validation des jetons dans Azure Functions

Vous pouvez également valider les jetons d’accès entrants dans Azure Functions. Vous trouverez des exemples de ce type de validation dans [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) et [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vérifiez les étendues et les rôles d’application dans votre code](scenario-protected-web-api-verification-scope-app-roles.md)
