---
title: Configurer une API web qui appelle des API web | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment créer une API web qui appelle des API web (configuration du code de l’application)
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
ms.openlocfilehash: 1e54e26bba1618a3b5835480ed1b1fe698bc8db4
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76043420"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Une API web qui appelle des API web : Configuration de code

Une fois que vous avez inscrit votre API web, vous pouvez configurer le code pour l’application.

Le code que vous utilisez pour configurer votre API web afin qu’elle appelle des API web en aval s’appuie sur le code utilisé pour protéger une API web. Pour plus d’informations, consultez la section [API web protégée : Configuration d’application](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Code faisant l’objet d’un abonnement OnTokenValidated

En plus de la configuration du code pour toutes les API web protégées, vous devez vous abonner à la validation du jeton du porteur que vous recevez lorsque votre API est appelée :

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Flux On-Behalf-Of

La méthode AddAccountToCacheFromJwt() doit :

- Instancier une application cliente confidentielle Microsoft Authentification Library (MSAL).
- Appelez la méthode `AcquireTokenOnBehalf` . Cet appel permet d’échanger le jeton du porteur qui a été acquis par le client pour l’API web contre un jeton du porteur pour le même utilisateur, mais il est nécessaire que l’API appelle une API en aval.

### <a name="instantiate-a-confidential-client-application"></a>Instancier une application cliente confidentielle

Ce flux est uniquement disponible dans le flux client confidentiel. Par conséquent, l’API web protégée fournit des informations d’identification de client (certificat ou clé secrète client) à la [classe ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) via les méthodes `WithClientSecret` ou `WithCertificate`.

![Liste des méthodes IConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Enfin, au lieu de prouver leur identité à l’aide d’une clé secrète client ou d’un certificat, les applications clientes confidentielles peuvent prouver leur identité à l’aide d’assertions client.
Pour plus d’informations sur ce scénario avancé, consultez la section [Assertions clientes confidentielles](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Comment appeler On-Behalf-Of ?

Vous effectuez l’appel On-Behalf-Of (OBO) en appelant la [méthode AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) sur l’interface `IConfidentialClientApplication`.

La classe `UserAssertion` est générée à partir du jeton du porteur fourni à l’API web par ses propres clients. Elle compte [deux contrôleurs](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet) :
* un qui accepte un jeton du porteur JSON Web Token (JWT)
* un qui accepte tout type d’assertion de l’utilisateur, un autre type de jeton de sécurité, dont le type est ensuite spécifié dans un paramètre supplémentaire nommé `assertionType`

![Propriétés et méthodes UserAssertion](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

En pratique, le flux OBO est souvent utilisé pour acquérir un jeton pour une API en aval et le stocker dans le cache de jetons utilisateur MSAL.NET. Le but est que d’autres parties de l’API web puissent ensuite appeler les [remplacements](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) de ``AcquireTokenOnSilent`` pour appeler les API en aval. Cet appel a pour effet d’actualiser les jetons si nécessaire.

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```

Vous pouvez également voir un exemple d’implémentation de flux OBO dans [NodeJS et Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol

Pour plus d’informations sur le protocole OBO, consultez la section [Plateforme d’identités Microsoft et flux On-Behalf-Of OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Une API web qui appelle des API web : Acquérir un jeton pour l’application](scenario-web-api-call-api-acquire-token.md)
