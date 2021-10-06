---
title: Acquérir un jeton pour appeler une API web (application de bureau) | Azure
titleSuffix: Microsoft identity platform
description: Découvrir comment générer une application de bureau qui appelle des API web en vue d’acquérir un jeton pour l’application
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python, has-adal-ref
ms.openlocfilehash: 0e23951d0d38f2ea8ddfba44a5a2afc7590e2ba2
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234297"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Application de bureau qui appelle des API web : Acquérir un jeton

Après avoir créé une instance de l’application cliente publique, vous l’utilisez pour acquérir un jeton que vous utiliserez ensuite pour appeler une API web.

## <a name="recommended-pattern"></a>Modèle recommandé

L’API web est définie par ses valeurs de `scopes`. Quelle que soit l’expérience que vous fournissez dans votre application, le modèle à utiliser est le suivant :

- Systématiquement tenter d’obtenir un jeton à partir du cache de jeton en appelant `AcquireTokenSilent`
- Si cet appel échoue, utiliser le flux `AcquireToken` que vous souhaitez utiliser, ici représenté par `AcquireTokenXX`

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>Dans MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java
Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Dans MSAL pour iOS et macOS

Objective-C :

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```

Swift :

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Dans MSAL Node, vous acquérez des jetons par le biais d’un flux de code d’autorisation avec une clé de preuve pour la norme PKCE (Proof Key for Code Exchange). MSAL Node utilise un cache de jeton en mémoire pour déterminer s’il existe des comptes d’utilisateur dans le cache. Si c’est le cas, l’objet de compte peut être transmis à la méthode `acquireTokenSilent()` pour récupérer un jeton d’accès mis en cache.

```javascript

const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const msalTokenCache = pca.getTokenCache();

let accounts = await msalTokenCache.getAllAccounts();

    if (accounts.length > 0) {

        const silentRequest = {
            account: accounts[0], // Index must match the account that is trying to acquire token silently
            scopes: ["user.read"],
        };

        pca.acquireTokenSilent(silentRequest).then((response) => {
            console.log("\nSuccessful silent token acquisition");
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => console.log(error));
    } else {
        const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

        const authCodeUrlParameters = {
            scopes: ["User.Read"],
            redirectUri: "your_redirect_uri",
            codeChallenge: challenge, // PKCE Code Challenge
            codeChallengeMethod: "S256" // PKCE Code Challenge Method 
        };

        // get url to sign user in and consent to scopes needed for application
        pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            console.log(response);

            const tokenRequest = {
                code: response["authorization_code"],
                codeVerifier: verifier // PKCE Code Verifier 
                redirectUri: "your_redirect_uri",
                scopes: ["User.Read"],
            };

            // acquire a token by exchanging the code
            pca.acquireTokenByCode(tokenRequest).then((response) => {
                console.log("\nResponse: \n:", response);
            }).catch((error) => {
                console.log(error);
            });
        }).catch((error) => console.log(JSON.stringify(error)));
    }
```

# <a name="python"></a>[Python](#tab/python)

```python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

---

Vous pouvez acquérir des jetons de différentes façons dans une application de bureau.

- [de manière interactive,](scenario-desktop-acquire-token-interactive.md)
- [Authentification Windows intégrée](scenario-desktop-acquire-token-integrated-windows-authentication.md)
- [WAM](scenario-desktop-acquire-token-wam.md)
- [Username Password](scenario-desktop-acquire-token-username-password.md)
- [Flux de code d’appareil](scenario-desktop-acquire-token-device-code-flow.md)

---
## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Appeler une API web à partir de l’appareil de bureau](scenario-desktop-call-api.md).
