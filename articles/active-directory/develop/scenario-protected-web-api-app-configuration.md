---
title: API web - configuration de code d’application protégée | Azure
description: Découvrez comment créer une API Web protégée et de configurer le code de votre application.
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
ms.openlocfilehash: e206cb29338445e30a7462bcbaf0079236e75510
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074964"
---
# <a name="protected-web-api---code-configuration"></a>API - configuration du code web protégée

Pour configurer correctement le code de votre API web protégée, vous devez comprendre ce qui rend les API protégées, vous devez configurer le jeton du porteur et comment valider le jeton.

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>En quoi les API de Core ASP.NET/ASP.NET protégées ?

Comme dans les applications web, le ASP.NET/ASP.NET core web API sont « protégés », car leurs actions de contrôleur sont précédées de la `[Authorize]` attribut. Par conséquent, les actions de contrôleur peuvent uniquement être appelées si l’API est appelée avec une identité qui est autorisée.

Posez-vous les questions suivantes :

- Comment l’API web que l’identité de l’application qui l’appelle (uniquement pour une application peut appeler une API web) ?
- Si l’application appelle l’API web part d’un utilisateur, ce qui est l’identité l’utilisateur ?

## <a name="bearer-token"></a>Jeton du porteur

Les informations sur l’identité de l’application, ainsi que sur l’utilisateur (sauf si l’application web accepte les appels de service à service à partir d’une application démon), est conservé dans le jeton du porteur qui est défini dans l’en-tête lors de l’appel de l’application.

Voici un C# exemple de code qui montre un client en appelant l’API après avoir acquis un jeton avec MSAL.NET.

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
> Le jeton du porteur a été demandé par une application cliente pour le point de terminaison Microsoft identity plateforme **de l’API web**. L’API web est la seule application qui doit vérifier le jeton et examiner les revendications qu’il contient. Les applications clientes ne doivent jamais étudier les revendications dans les jetons (l’API web peut décider, à l’avenir, qu’il exige que le jeton sont chiffrées et cette opération rompt l’application cliente qui peut ouvrir les jetons d’accès).

## <a name="jwtbearer-configuration"></a>Configuration de JwtBearer

Cette section décrit ce que vous devez configurer le jeton du porteur.

### <a name="config-file"></a>Fichier de configuration

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
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

Lorsque l’application est appelée sur l’exploitation d’action de contrôleur un `[Authorize]` attribut, ASP.NET/ASP.NET core examine le jeton du porteur dans l’en-tête d’autorisation de la demande d’appel et extrait le jeton d’accès, qui est ensuite transmis à la JwtBearer intergiciel (middleware), qui appelle les Extensions de modèle d’identité Microsoft pour .NET.

Dans ASP.NET Core, cet intergiciel est initialisé dans le `Startup.cs` fichier.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

L’intergiciel (middleware) est ajouté à l’API web par l’instruction suivante :

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Actuellement, les modèles ASP.NET Core créent Azure AD v1.0 des API web. Toutefois, vous pouvez facilement les modifier pour utiliser le point de terminaison Microsoft identity platform en ajoutant le code suivant dans le `Startup.cs` fichier.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Validation du jeton

L’intergiciel (middleware) JwtBearer, comme le middleware OpenID Connect dans les applications web, est dirigée par les `TokenValidationParameters` pour valider le jeton. Le jeton est déchiffré (si nécessaire), les revendications sont extraits et la signature est vérifiée. Ensuite, le jeton est validé en recherchant les données suivantes :

- Il est ciblé pour le web API (public)
- Il a été émis pour une application qui est autorisée à appeler l’API (sub) web
- Il a été émis par un digne de confiance serveur STS (Security Token) (émetteur)
- Durée de vie du jeton est dans la plage (expiration)
- Il n’a pas été falsifié (signature)

Il peut également être validations spéciales. Par exemple, il est possible valider que les clés de signature (lorsqu’il est incorporé dans un jeton) sont approuvés et que le jeton n’est pas en cours de réexécution. Enfin, certains protocoles nécessitent des validations spécifiques.

### <a name="validators"></a>Validateurs

Les étapes de validation sont capturées dans les validateurs, lesquels figurent tous dans le [Extension du modèle d’identité Microsoft pour .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) bibliothèque open source, dans un fichier source : [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

Les validateurs sont décrites dans le tableau suivant :

| Programme de validation | Description |
|---------|---------|
| `ValidateAudience` | Garantit que le jeton est pour l’application qui valide le jeton (pour moi). |
| `ValidateIssuer` | Garantit que le jeton a été émis par un STS approuvé (à partir d’une personne que j’ai confiance). |
| `ValidateIssuerSigningKey` | Garantit que l’application valide les approbations de jeton de la clé qui a été utilisée pour signer le jeton (cas particulier où la clé est incorporée dans le jeton est généralement ne pas nécessaire). |
| `ValidateLifetime` | Garantit que le jeton est valide toujours (ou déjà). Fait en vérifiant que la durée de vie du jeton (`notbefore`, `expires` revendications) est à portée. |
| `ValidateSignature` | Garantit que le jeton n’a pas été falsifié. |
| `ValidateTokenReplay` | Garantit que le jeton n’est pas relu (cas spécial pour certains protocoles utilisation onetime). |

Les validateurs sont tous associés à des propriétés de la `TokenValidationParameters` classe eux-mêmes initialisé à partir de la configuration de base de ASP.NET/ASP.NET. Dans la plupart des cas, vous ne devrez pas modifier les paramètres. Il existe une exception pour les applications qui ne sont pas des locataires uniques (c'est-à-dire les applications web qui acceptent des utilisateurs à une organisation ou les comptes Microsoft personnels) : dans ce cas, l’émetteur doit être validé.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-protected-web-api-production.md)
