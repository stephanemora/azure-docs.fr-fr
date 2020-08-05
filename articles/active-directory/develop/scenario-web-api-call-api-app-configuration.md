---
title: Configurer une API web qui appelle des API web | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment créer une API web qui appelle des API web (configuration du code de l’application)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: eff5f68569d1878e1b802f2db4151d246bcc07c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026422"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Une API web qui appelle des API web : Configuration de code

Une fois que vous avez inscrit votre API web, vous pouvez configurer le code pour l’application.

Le code que vous utilisez pour configurer votre API web afin qu’elle appelle des API web en aval s’appuie sur le code utilisé pour protéger une API web. Pour plus d’informations, consultez la section [API web protégée : Configuration d’application](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Secrets clients ou certificats clients

Dans la mesure où votre API web appelle désormais une API web en aval, vous devez fournir un secret client ou un certificat client dans le fichier *appsettings.json*.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 }
}
```

À la place d’un secret client, vous pouvez fournir un certificat client. L’extrait de code suivant montre l’utilisation d’un certificat stocké dans Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
  ]
 }
}
```

Microsoft.Identity.Web propose plusieurs façons de décrire les certificats, que ce soit en définissant une configuration ou en écrivant du code. Pour plus d’informations, consultez [Microsoft.Identity.Web wiki - Using certificates](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) sur GitHub.

## <a name="startupcs"></a>Startup.cs

À l’aide de Microsoft.Identity.Web, si vous souhaitez que votre API web appelle les API web en aval, ajoutez la ligne `.AddMicrosoftWebApiCallsWebApi()` après `.AddMicrosoftWebApiAuthentication(Configuration)`, puis choisissez une implémentation de cache de jetons, par exemple `.AddInMemoryTokenCaches()`, dans *Startup.cs* :

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
   // ...
   services.AddMicrosoftWebApiAuthentication(Configuration)
           .AddMicrosoftWebApiCallsWebApi()
           .AddInMemoryTokenCaches();
  // ...
  }
  // ...
}
```

Comme pour les applications web, vous pouvez choisir diverses implémentations de cache de jetons. Pour plus d’informations, consultez [Microsoft identity web wiki - Token cache serialization](https://aka.ms/ms-id-web/token-cache-serialization) sur GitHub.

Si vous êtes certain que votre API web a besoin d’étendues spécifiques, vous pouvez éventuellement les passer en tant qu’arguments à `AddMicrosoftWebApiCallsWebApi`.

# <a name="java"></a>[Java](#tab/java)

Le flux On-behalf-of (OBO) permet d’obtenir un jeton pour appeler l’API web en aval. Dans ce flux, votre API web reçoit de l’application cliente un jeton du porteur avec des permissions déléguées par l’utilisateur, puis échange ce jeton contre un autre jeton d’accès pour appeler l’API web en aval.

Le code ci-dessous utilise le `SecurityContextHolder` de l’infrastructure de sécurité Spring dans l’API web pour récupérer le jeton du porteur validé. Il utilise ensuite la bibliothèque Java MSAL pour obtenir un jeton pour l’API en aval à l’aide de l’appel `acquireToken` avec `OnBehalfOfParameters`. MSAL met en cache le jeton afin que les appels subséquents à l’API puissent utiliser `acquireTokenSilently` pour récupérer ce jeton.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Le flux On-behalf-of (OBO) permet d’obtenir un jeton pour appeler l’API web en aval. Dans ce flux, votre API web reçoit de l’application cliente un jeton du porteur avec des permissions déléguées par l’utilisateur, puis échange ce jeton contre un autre jeton d’accès pour appeler l’API web en aval.

Une API web Python doit utiliser un intergiciel pour valider le jeton du porteur reçu du client. L'API web peut ensuite obtenir le jeton d'accès de l'API située en aval à l'aide de la bibliothèque MSAL Python en appelant la méthode [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of). Pour obtenir un exemple d’utilisation de cette API, consultez le [code de test pour microsoft-authentication-library-for-python sur GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Consultez également la présentation du [problème 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) dans ce même dépôt pour une approche qui contourne la nécessité d’une application de niveau intermédiaire.

---

Vous pouvez également voir un exemple d’implémentation de flux OBO dans [NodeJS et Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol

Pour plus d’informations sur le protocole OBO, consultez la section [Plateforme d’identités Microsoft et flux On-Behalf-Of OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Une API web qui appelle des API web : Acquérir un jeton pour l’application](scenario-web-api-call-api-acquire-token.md)
