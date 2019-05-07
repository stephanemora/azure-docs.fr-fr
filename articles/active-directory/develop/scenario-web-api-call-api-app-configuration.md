---
title: Web API qui appelle en aval des API web (configuration de code de l’application) - la plateforme d’identité Microsoft
description: Découvrez comment créer une API que les appels de web API (configuration de code de l’application) web
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
ms.openlocfilehash: 204baac37254592c0dc808af413fd8b3c6c79864
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074784"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>API que les appels de web API - configuration du code Web

Une fois que vous avez inscrit votre API web, vous pouvez configurer le code de l’application.

Le code pour configurer votre API web afin qu’il appelle des API web en aval s’appuie sur le code utilisé pour projeter une API web. Pour plus d’informations, consultez [API web - configuration de l’application protégée](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Code souscrit à OnTokenValidated

En haut de la configuration du code pour toutes les API web protégée, vous devez vous abonner à la validation du jeton du porteur reçu lorsque votre API est appelée :

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
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

- Instancier une application cliente confidentielle de MSAL.
- Appelez `AcquireTokenOnBehalf` pour échanger le jeton du porteur qui a été acquis par le client pour l’API web, par rapport à un jeton du porteur pour le même utilisateur, mais pour notre API pour appeler une API en aval.

### <a name="instantiate-a-confidential-client-application"></a>Instancier une application cliente confidentielle

Ce flux est uniquement disponible dans le flux client confidentiel par conséquent, l’API web protégée fournit des informations d’identification de client (certificat ou clé secrète client) à la [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.confidentialclientapplicationbuilder?view=azure-dotnet-preview) via la `WithClientSecret` ou `WithCertificate`méthodes, respectivement.

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

### <a name="how-to-call-on-behalf-of"></a>Comment appeler on-behalf-of

L’appel de (OBO) on-behalf-of s’effectue en appelant le [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenonbehalfofparameterbuilder?view=azure-dotnet-preview) méthode sur le `IConfidentialClientApplication` interface.

Le `ClientAssertion` est généré à partir du jeton du porteur reçu par l’API web à partir de ses propres clients. Il existe [deux constructeurs](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), une fonction qui accepte un porteur JWT token et une fonction qui accepte tout type d’assertion de l’utilisateur (un autre type de jeton de sécurité, le type est ensuite spécifié dans un paramètre supplémentaire nommé `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

Dans la pratique, le flux OBO est souvent utilisé pour acquérir un jeton pour une API en aval et le stocker dans le cache de jeton utilisateur MSAL.NET afin que les autres parties de l’API web peuvent appeler par la suite sur le [substitue](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) de ``AcquireTokenOnSilent`` pour appeler les API en aval. Cela a pour effet de l’actualisation des jetons, si nécessaire.

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

## <a name="protocol"></a>Protocol

Pour plus d’informations sur le protocole on-behalf-of, consultez [plateforme d’identité Microsoft et les flux OAuth 2.0 pour le compte](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [L’acquisition d’un jeton pour l’application](scenario-web-api-call-api-acquire-token.md)
