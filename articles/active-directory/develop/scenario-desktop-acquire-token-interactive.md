---
title: Acquérir un jeton pour appeler une API web de manière interactive (application de bureau) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment générer une application de bureau qui appelle des API web en vue d’acquérir un jeton pour l’application de manière interactive
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
ms.openlocfilehash: 6cd9e4ff795e1051267aa20e244e03c37c15f831
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838457"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-interactively"></a>Application de bureau appelant des API web : acquérir un jeton de manière interactive

L’exemple suivant montre le code minimal permettant d’obtenir un jeton de manière interactive pour lire le profil de l’utilisateur avec Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>Dans MSAL.NET

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Paramètres obligatoires

`AcquireTokenInteractive` n’a qu’un seul paramètre obligatoire, ``scopes``, qui contient une énumération de chaînes définissant les étendues pour lesquelles un jeton est nécessaire. Si le jeton est destiné à Microsoft Graph, vous trouvez les étendues nécessaires dans les informations de référence d’API de chaque API Microsoft Graph, à la section intitulée « Autorisations ». Par exemple, pour [lister les contacts de l’utilisateur](/graph/api/user-list-contacts), vous devez utiliser les étendues « User.Read », « Contacts.Read ». Pour plus d’informations, consultez la [documentation de référence sur les autorisations Microsoft Graph](/graph/permissions-reference).

Sur Android, vous devez également spécifier l’activité parente au moyen de `.WithParentActivityOrWindow`, comme indiqué, afin que le jeton retourne à cette activité parente après l’interaction. Si vous ne le précisez pas, une exception est levée lors de l’appel de `.ExecuteAsync()`.

### <a name="specific-optional-parameters-in-msalnet"></a>Paramètres facultatifs particuliers dans MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

L’interface utilisateur est importante, car elle est interactive. `AcquireTokenInteractive` présente un paramètre facultatif spécifique pouvant préciser, pour les plateformes qui le prennent en charge, l’interface utilisateur parente. Lorsqu’il est utilisé dans une application de bureau, `.WithParentActivityOrWindow` présente un type différent qui dépend la plateforme. Vous pouvez également omettre le paramètre facultatif de fenêtre parente pour créer une fenêtre, si vous ne souhaitez pas contrôler l’emplacement où la boîte de dialogue de connexion s’affiche à l’écran. Cela s’applique aux applications basées sur une ligne de commande, utilisées pour passer des appels à tout autre service principal et qui n’ont pas besoin de fenêtres pour l’interaction avec l’utilisateur.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .NET Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Remarques :

- Sur .NET Standard, l’élément `object` attendu est `Activity` sur Android, `UIViewController` sur iOS, `NSWindow` sur Mac et `IWin32Window` ou `IntPr` sur Windows.
- Sur Windows, vous devez appeler `AcquireTokenInteractive` à partir du thread d’interface utilisateur, afin que le navigateur intégré obtienne le contexte de synchronisation de l’interface utilisateur approprié. Ne pas appeler depuis le thread d’interface utilisateur peut occasionner des messages qui ne pompent pas correctement, et des scénarios de blocage avec l’interface utilisateur. Un moyen d’appeler les bibliothèques d’authentification Microsoft (MSAL) à partir du thread d’interface utilisateur, si vous n’êtes pas déjà sur le thread, consiste à utiliser `Dispatcher` sur WPF.
- Si vous utilisez WPF, vous pouvez vous servir de la classe `WindowInteropHelper.Handle` pour obtenir une fenêtre à partir d’un contrôle WPF. L’appel est alors le suivant, à partir d’un contrôle WPF (`this`) :

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` permet de contrôler l’interactivité avec l’utilisateur en spécifiant une invite. Vous pouvez contrôler le comportement exact à l’aide de la structure [Microsoft.Identity.Client.Prompt](/dotnet/api/microsoft.identity.client.prompt).

La structure définit les constantes suivantes :

- `SelectAccount` force le service d’émission de jeton de sécurité (STS) à présenter la boîte de dialogue de sélection de compte qui contient les comptes pour lesquels l’utilisateur dispose d’une session. Cette option est utile lorsque les développeurs d’applications veulent laisser les utilisateurs choisir parmi différentes identités. Cette option oblige MSAL à envoyer `prompt=select_account` au fournisseur d’identité. Cette option est celle par défaut. Elle remplit bien sa tâche en fournissant la meilleure expérience possible en fonction des informations disponibles, comme le compte et la présence d’une session pour l’utilisateur. Ne la remplacez pas, à moins d’avoir une excellente raison de le faire.
- `Consent` permet au développeur d’application de forcer l’affichage d’une invite demandant à l’utilisateur son consentement, et même si celui-ci a été accordé auparavant. Dans ce cas, MSAL envoie `prompt=consent` au fournisseur d’identité. Cette option peut être utilisée dans certaines applications axées sur la sécurité, dans lesquelles la gouvernance de l’organisation exige que l’utilisateur voie s’afficher la boîte de dialogue de consentement chaque fois que l’application est utilisée.
- `ForceLogin` permet au développeur d’application de faire afficher par le service une invite demandant à l’utilisateur d’entrer des informations d’identification, et même si cette invite utilisateur n’est peut-être pas nécessaire. Cette option peut s’avérer utile pour permettre à l’utilisateur de se reconnecter si l’acquisition d’un jeton échoue. Dans ce cas, MSAL envoie `prompt=login` au fournisseur d’identité. Elle est parfois utilisée dans les applications axées sur la sécurité, pour lesquelles la gouvernance de l’organisation exige que l’utilisateur se reconnecte chaque fois qu’il accède à des parties spécifiques d’une application.
- `Create` déclenche une expérience d’inscription, qui est utilisée pour External Identities, en envoyant `prompt=create` au fournisseur d’identité. Cette invite ne doit pas être envoyée pour les applications Azure AD B2C. Pour plus d’informations, consultez [Ajouter un flux d’utilisateurs d’inscription en libre-service à une application](../external-identities/self-service-sign-up-user-flow.md).
- `Never` (pour .NET 4.5 et WinRT uniquement) n’affiche aucune invite utilisateur, mais tente à la place d’utiliser le cookie stocké dans la vue web incorporée masquée. Pour plus d’informations, consultez les vues web dans MSAL.NET. L’utilisation de cette option peut échouer. Dans ce cas, `AcquireTokenInteractive` lève une exception pour notifier qu’une interaction avec l’interface utilisateur est nécessaire. Vous devez utiliser un autre paramètre `Prompt`.
- `NoPrompt` n’envoyant aucune invite au fournisseur d’identité, celui-ci décide de présenter la meilleure expérience de connexion à l’utilisateur (authentification unique ou sélection de compte). Cette option est également obligatoire que pour les stratégies de modification de profil Azure Active Directory (Azure AD) B2C. Pour plus d’informations, consultez [Spécificités d’Azure Active Directory B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withuseembeddedwebview"></a>WithUseEmbeddedWebView

Cette méthode vous permet de spécifier si vous souhaitez forcer l’utilisation d’une vue web intégrée ou de la vue web du système (si disponible). Pour plus d’informations, consultez [Utilisation de navigateurs Web](msal-net-web-browsers.md).

```csharp
var result = await app.AcquireTokenInteractive(scopes)
                    .WithUseEmbeddedWebView(true)
                    .ExecuteAsync();
```

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ce modificateur est utilisé dans un scénario élaboré, suivant lequel vous souhaitez obtenir le consentement préalable de l’utilisateur pour plusieurs ressources en amont, et ne souhaitez pas utiliser le consentement incrémentiel, qui est normalement utilisé avec MSAL.NET/la plateforme d’identités Microsoft. Pour plus d’informations, consultez [Guide pratique pour obtenir le consentement préalable de l’utilisateur sur plusieurs ressources](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Une interface utilisateur web est un mécanisme pour appeler un navigateur. Ce mécanisme peut être un contrôle WebBrowser dédié avec interface utilisateur ou un moyen de déléguer l’ouverture du navigateur.
MSAL fournit des implémentations d’interface utilisateur web pour la plupart des plateformes, mais il existe des cas où vous pourriez préférer héberger vous-même le navigateur :

- Pour les plateformes qui ne sont pas explicitement couvertes par MSAL, comme Blazor, Unity et Mono version bureau
- Si vous souhaitez tester l’interface utilisateur de votre application et vous servir d’un navigateur automatisé qui peut être utilisé avec Selenium
- Si le navigateur et l’application qui exécutent MSAL se trouvent dans des processus distincts

##### <a name="at-a-glance"></a>Aperçu

Pour ce faire, vous fournissez `start Url` à MSAL, qui doit s’afficher dans un navigateur de votre choix, afin que l’utilisateur final puisse entrer des informations telles que son nom d’utilisateur.
L’authentification terminée, votre application doit redonner `end Url` à MSAL, qui contient un code fourni par Azure AD.
L’hôte de `end Url` est toujours `redirectUri`. Pour intercepter `end Url`, effectuez l’une des opérations suivantes :

- Superviser les redirections du navigateur jusqu’à atteindre `redirect Url`
- Demander au navigateur de rediriger vers une URL que vous supervisez

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi est un point d’extensibilité

`WithCustomWebUi` est un point d’extensibilité que vous pouvez utiliser pour fournir votre propre interface utilisateur dans les applications clientes publiques. Vous pouvez également laisser l’utilisateur passer par le point de terminaison /Authorize du fournisseur d’identité et l’autoriser à se connecter et à donner son consentement. MSAL.NET peut alors échanger le code d’authentification pour obtenir un jeton. Par exemple, il est utilisé dans Visual Studio, pour que des applications électrons (comme les commentaires Visual Studio) fournissent l’interaction web tout en laissant MSAL.NET faire le gros du travail. Vous pouvez également l’utiliser si vous voulez fournir UI automation. Dans les applications clientes publiques, MSAL.NET utilise la norme PKCE (Proof Key for Code Exchange) pour garantir le respect de la sécurité. seul MSAL.NET peut échanger le code. Pour plus d’informations, consultez [RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients (RFC 7636 - Clé de preuve pour l’échange de clé par les clients publics OAuth)](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Utiliser WithCustomWebUi

Pour utiliser `.WithCustomWebUI`, suivez ces étapes.

  1. Implémentez l'interface `ICustomWebUi`. Pour plus d’informations, consultez [ce site web](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implémentez une méthode `AcquireAuthorizationCodeAsync` et acceptez l’URL de code d’autorisation calculée par MSAL.NET. Ensuite, permettez à l’utilisateur de parcourir l’interaction avec le fournisseur d’identité et de retourner l’URL par laquelle le fournisseur d’identité aurait rappelé votre implémentation avec le code d’autorisation. Si vous rencontrez des problèmes, votre implémentation doit lever une exception `MsalExtensionException` afin de pouvoir coopérer efficacement avec MSAL.
  2. Dans votre appel `AcquireTokenInteractive`, utilisez le modificateur `.WithCustomUI()` en passant l’instance de votre interface utilisateur web personnalisée.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Exemples d’implémentation de ICustomWebUi dans l’automatisation de test : SeleniumWebUI

L’équipe de MSAL.NET a réécrit les tests d’interface utilisateur pour utiliser ce mécanisme d’extensibilité. Si vous êtes intéressé, examinez de plus près la classe [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) dans le code source MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Fournir une bonne expérience avec SystemWebViewOptions

À partir de MSAL.NET 4.1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions), vous pouvez spécifier :

- L’URI vers laquelle naviguer (`BrowserRedirectError`) ou le fragment HTML à afficher (`HtmlMessageError`) en cas d’erreurs de connexion ou de consentement dans le navigateur web système.
- L’URI vers laquelle naviguer (`BrowserRedirectSuccess`) ou le fragment HTML à afficher (`HtmlMessageSuccess`) en cas de réussite de la connexion ou du consentement.
- L’action à exécuter pour démarrer le navigateur système. Vous pouvez fournir votre propre implémentation en définissant le délégué `OpenBrowserAsync`. La classe fournit également une implémentation par défaut pour deux navigateurs : `OpenWithEdgeBrowserAsync` et `OpenWithChromeEdgeBrowserAsync`, pour Microsoft Edge et [Microsoft Edge sur Chromium](https://www.windowscentral.com/faq-edge-chromium), respectivement.

Pour utiliser cette structure, écrivez quelque chose de similaire à l’exemple suivant :

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Autres paramètres facultatifs

Pour en savoir plus sur tous les autres paramètres facultatifs de `AcquireTokenInteractive`, consultez [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

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
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Dans MSAL pour iOS et macOS

Objective-C :

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift :

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Dans MSAL Node, vous acquérez des jetons par le biais d’un flux de code d’autorisation avec une clé de preuve pour la norme PKCE (Proof Key for Code Exchange). Le processus comporte deux étapes : tout d’abord, l’application obtient une URL qui peut être utilisée pour générer un code d’autorisation. L’utilisateur peut ouvrir cette URL dans le navigateur de son choix, où il peut entrer ses informations d’identification et sera redirigé vers le `redirectUri` (inscrit pendant l’inscription de l’application) avec un code d’autorisation. Ensuite, l’application transmet le code d’autorisation reçu à la méthode `acquireTokenByCode()` qui l’échange pour un jeton d’accès.

```javascript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

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
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 1.7+ fournit une méthode d’acquisition de jeton interactive.

```python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_interactive(  # It automatically provides PKCE protection
         scopes=config["scope"])
```

---
### <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Appeler une API web à partir de l’appareil de bureau](scenario-desktop-call-api.md).
