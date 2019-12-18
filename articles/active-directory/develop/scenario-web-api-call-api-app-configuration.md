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
ms.openlocfilehash: 219724186e3fa69fec35e89435af495b662c871d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919747"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>API Web appelant des API web - Configuration du code

Une fois que vous avez inscrit votre API web, vous pouvez configurer le code pour l’application.

Le code pour configurer votre API web afin qu’il appelle des API web en aval s’appuie sur le code utilisé pour protéger une API web. Pour plus d’informations, consultez [API web protégée - configuration de l’application](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Code faisant l’objet d’un abonnement OnTokenValidated

Au début de la configuration du code pour toutes les API web protégées, vous devez vous abonner à la validation du jeton du porteur reçu lorsque votre API est appelée :

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
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
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
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

- instancier une application cliente MSAL confidentielle.
- Appelez `AcquireTokenOnBehalf` pour échanger le jeton du porteur qui a été acquis par le client pour l’API web contre un jeton du porteur pour le même utilisateur, mais pour que notre API appelle une API en aval.

### <a name="instantiate-a-confidential-client-application"></a>Instancier une application cliente confidentielle

Ce flux est uniquement disponible dans le flux client confidentiel. Par conséquent, l’API web protégée fournit des informations d’identification de client (certificat ou clé secrète client) à [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) via les méthodes `WithClientSecret` ou `WithCertificate`, respectivement.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
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

Enfin, au lieu d’une clé secrète client ou d’un certificat, les applications clientes confidentielles peuvent également prouver leur identité à l’aide d’assertions client.
Ce scénario avancé est détaillé dans [Assertions client](msal-net-client-assertions.md)

### <a name="how-to-call-on-behalf-of"></a>Comment appeler on-behalf-of

L’appel on-behalf-of (OBO) s’effectue en appelant la méthode [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) sur l’interface `IConfidentialClientApplication`.

L’`UserAssertion` est générée à partir du jeton du porteur fourni à l’API par ses propres clients. Il y a [deux constructeurs](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), un qui accepte un jeton du porteur JWT et un qui accepte tout type d’assertion de l’utilisateur (un autre type de jeton de sécurité, ensuite spécifié dans un paramètre supplémentaire nommé `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

Dans la pratique, le flux OBO est souvent utilisé pour acquérir un jeton pour une API en aval et le stocker dans le cache de jeton utilisateur MSAL.NET, afin que d’autres parties de l’API web puissent appeler par la suite sur les [substituts](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) de ``AcquireTokenOnSilent`` pour appeler les API en aval. Cet appel a pour effet d’actualiser les jetons si nécessaire.

```CSharp
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

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
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

Vous pouvez également voir un exemple d’implémentation de flux on-behalf-of dans [NodeJS et Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol

Pour plus d’informations sur le protocole on-behalf-of, consultez [Plateforme d’identités Microsoft et flux On-Behalf-Of OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Acquisition d’un jeton pour l’application](scenario-web-api-call-api-acquire-token.md)
