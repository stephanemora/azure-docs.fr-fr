---
title: Configurer des applications API web protégées | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment créer une API web protégée et configurer le code de votre application.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 7f78fa35096b7e17d3736190bfa49619c2c81520
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965396"
---
# <a name="protected-web-api-code-configuration"></a>API web protégée : Configuration de code

Pour configurer le code de votre API web protégée, vous devez comprendre ce qui définit une API comme protégée, comment configurer un jeton du porteur et comment valider le jeton.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Qu’est-ce qui définit une API ASP.NET/ASP.NET Core comme protégée ?

Comme les applications web, les API Web ASP.NET/ASP.NET Core sont « protégées » parce que leurs actions de contrôleur sont préfixées avec l’attribut `[Authorize]`. Les actions de contrôleur ne peuvent ainsi être appelées que si l’API est appelée avec une identité autorisée.

Considérez les questions suivantes :

- Comment l’API web connaît-elle l’identité de l’application qui l’appelle ? (Seule une application peut appeler une API web.)
- Si l’application a appelé l’API web au nom d’un utilisateur, quelle est l’identité de celui-ci ?

## <a name="bearer-token"></a>Jeton du porteur

Les informations sur l’identité de l’application et sur l’utilisateur (sauf si l’application web accepte les appels de service à service à partir d’une application démon) sont conservées dans le jeton du porteur défini dans l’en-tête lors de l’appel de l’application.

Voici un exemple de code C# qui présente un client appelant l’API après avoir acquis un jeton de Microsoft Authentication Library pour .NET (MSAL.NET) :

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Le jeton du porteur a été demandé par une application cliente au point de terminaison de la Plateforme d’identités Microsoft *pour l’API web*. L’API web est la seule application qui doive vérifier le jeton et afficher les revendications qu’il contient. Les applications clientes ne doivent jamais tenter d’inspecter les revendications contenues dans les jetons (l’API web pourrait exiger à l’avenir que le jeton soit chiffrés ; cette exigence empêcherait l’accès des applications clientes capables d’afficher les jetons d’accès).

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

Quand une application est appelée sur une action de contrôleur qui contient un attribut `[Authorize]`, ASP.NET/ASP.NET Core examine le jeton du porteur dans l’en-tête Authorization de la demande, et extrait le jeton d’accès. Le jeton est ensuite transmis à l’intergiciel JwtBearer qui appelle Microsoft IdentityModel Extensions pour .NET.

Dans ASP.NET Core, cet intergiciel est initialisé dans le fichier Startup.cs :

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

L’intergiciel (middleware) est ajouté à l’API web par cette instruction :

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Actuellement, les modèles ASP.NET Core créent des API web Azure Active Directory (Azure AD) qui connectent des utilisateurs de votre organisation ou d’une autre organisation sans utiliser de comptes personnels. Mais vous pouvez facilement les modifier pour utiliser le point de terminaison de la Plateforme d’identités Microsoft en ajoutant ce code dans le fichier Startup.cs :

```CSharp
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

Cet extrait de code est extrait du tutoriel incrémentiel de l’API web ASP.NET Core, dans [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). La méthode `AddProtectedWebApi`, bien plus vaste, est appelée à partir de Startup.cs.

## <a name="token-validation"></a>Validation du jeton

L’intergiciel JwtBearer, tout comme l’intergiciel OpenID Connect dans les applications web, reçoit de `TokenValidationParameters` l’instruction de valider le jeton. Le jeton est déchiffré (si nécessaire), les revendications extraites et la signature vérifiée. L’intergiciel valide ensuite le jeton en vérifiant les points suivants :

- Il est ciblé pour l’API web (audience).
- Il a été émis pour une application autorisée à appeler l’API web (sub).
- Il a été émis par un service de jeton de sécurité (STS) approuvé (émetteur).
- Sa durée de vie s’inscrit dans la plage valide (expiration).
- Il n’a pas été falsifié (signature).

Il peut également y avoir des validations spéciales. Par exemple, il est possible de valider le fait que les clés de signature (quand elles sont incorporées dans un jeton) sont approuvées et que le jeton n’est pas en cours de réexécution. Enfin, certains protocoles nécessitent des validations spécifiques.

### <a name="validators"></a>Validateurs

Les étapes de validation sont capturées dans des validateurs qui figurent tous dans la bibliothèque open source [Microsoft IdentityModel Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet), dans un fichier source unique : [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Les validateurs sont décrits dans ce tableau :

| Validateur | Description |
|---------|---------|
| `ValidateAudience` | S’assure que le jeton est destiné à l’application qui le valide (pour moi). |
| `ValidateIssuer` | S’assure que le jeton a été émis par un service d’émission de jeton de sécurité approuvé (de quelqu’un en qui j’ai confiance). |
| `ValidateIssuerSigningKey` | S’assure que l’application qui valide le jeton approuve la clé utilisée pour signer le jeton (cas particulier où la clé est incorporée dans le jeton ; généralement non obligatoire). |
| `ValidateLifetime` | S’assure que le jeton est encore (ou déjà) valide. Le validateur vérifie si la durée de vie du jeton (revendications `notbefore` et `expires`) s’inscrit dans la plage valide. |
| `ValidateSignature` | S’assure que le jeton n’a pas été falsifié. |
| `ValidateTokenReplay` | S’assure que le jeton n’est pas en cours de réexécution (cas spécial pour certains protocoles d’utilisation unique). |

Les validateurs sont tous associés à des propriétés de la classe `TokenValidationParameters`, elles-mêmes initialisées à partir de la configuration ASP.NET/ASP.NET Core. Dans la plupart des cas, vous n’avez pas besoin de modifier les paramètres. Il existe une exception pour les applications qui ne sont pas à locataire unique (c’est-à-dire des applications web qui acceptent des utilisateurs de toute organisation ou de comptes Microsoft personnels). Dans ce cas, l’émetteur doit être validé.

## <a name="token-validation-in-azure-functions"></a>Validation des jetons dans Azure Functions

Il est également possible de valider les jetons d’accès entrants dans Azure Functions. Vous trouverez des exemples de validation de jetons dans Azure Functions dans [Dotnet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) et [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vérifiez les étendues et les rôles d’application dans votre code](scenario-protected-web-api-verification-scope-app-roles.md)
