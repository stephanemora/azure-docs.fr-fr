---
title: Acquérir un jeton pour appeler une API Web (applications mobiles) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment générer une application mobile qui appelle des API web (acquisition d’un jeton pour l’application)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 2a86e8352958524bc51b185712d6b60ec347b98e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702094"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Application mobile qui appelle des API web - acquisition d’un jeton

Avant de commencer à appeler des API web protégées, votre application doit disposer d’un jeton d’accès. Cet article vous guide dans la procédure d’acquisition d’un jeton à l’aide de la bibliothèque d’authentification Microsoft (MSAL).

## <a name="scopes-to-request"></a>Étendues à demander

Lorsque vous demandez un jeton, vous devez définir une étendue. L’étendue détermine les données auxquelles votre application peut accéder.  

L’approche la plus simple consiste à combiner l’`App ID URI` de l’API web souhaitée avec l’étendue `.default`. Cela indique à la plateforme d’identités Microsoft que votre application a besoin de toutes les étendues définies dans le portail.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Obtenir des jetons

### <a name="acquire-tokens-via-msal"></a>Acquérir des jetons via MSAL

MSAL permet aux applications d’acquérir des jetons en mode silencieux et de manière interactive. Il suffit d’appeler ces méthodes pour que MSAL retourne un jeton d’accès pour les étendues demandées. La bonne procédure consiste à effectuer une demande en mode silencieux, puis de revenir à une demande interactive.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**Essayez d’abord d’acquérir un jeton de manière silencieuse :**

Objective-C :

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
Swift :

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
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
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**Ensuite, si MSAL renvoie `MSALErrorInteractionRequired`, essayez d’acquérir des jetons de manière interactive :**

Objective-C :

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
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
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL pour iOS et macOS prend en charge différents modificateurs lors de l’obtention d’un jeton de manière interactive ou silencieuse.
* [Paramètres courants lors de l’obtention d’un jeton](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Paramètres pour l’acquisition de jetons interactive](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Paramètres pour l’acquisition de jetons silencieuse](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

L’exemple suivant montre le code minimal permettant d’obtenir un jeton de manière interactive pour lire le profil de l’utilisateur avec Microsoft Graph.

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

#### <a name="mandatory-parameters-in-msalnet"></a>Paramètres obligatoires dans MSAL.NET

`AcquireTokenInteractive` n’a qu’un seul paramètre obligatoire, ``scopes``, qui contient une énumération de chaînes définissant les étendues pour lesquelles un jeton est nécessaire. Si le jeton est destiné à Microsoft Graph, vous trouvez les étendues nécessaires dans les informations de référence d’api de chaque API Microsoft graph, à la section intitulée « Autorisations ». Par exemple, pour [lister les contacts de l’utilisateur](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), vous aurez besoin des étendues « User.Read », « Contacts.Read ». Voir aussi [Informations de référence sur les autorisations Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Si vous ne l’avez pas spécifié lors de la création de l’application, sur Android, vous devez également spécifier l’activité parente (au moyen de `.WithParentActivityOrWindow`, voir ci-dessous), afin que le jeton retourne à cette activité parente après l’interaction. Si vous ne le précisez pas, une exception est levée lors de l’appel de `.ExecuteAsync()`.

#### <a name="specific-optional-parameters-in-msalnet"></a>Paramètres facultatifs particuliers dans MSAL.NET

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` permet de contrôler l’interactivité avec l’utilisateur en spécifiant une invite

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

La classe définit les constantes suivantes :

- ``SelectAccount`` : force le service d’émission de jeton de sécurité (STS) à présenter la boîte de dialogue de sélection de compte contenant les comptes pour lesquels l’utilisateur dispose d’une session. Cette option est utile lorsque les développeurs d’applications veulent laisser l’utilisateur choisir parmi différentes identités. Cette option oblige MSAL à envoyer ``prompt=select_account`` au fournisseur d’identité. C’est l’option par défaut, et elle remplit bien sa tâche en fournissant la meilleure expérience possible en fonction des informations disponibles (compte, présence d’une session pour l’utilisateur et ainsi de suite ...). Ne la remplacez pas, à moins d’avoir une excellente raison de le faire.
- ``Consent`` : permet au développeur d’application de forcer l’affichage d’une invite demandant à l’utilisateur son consentement, et même si celui-ci a été accordé auparavant. Dans ce cas, MSAL envoie `prompt=consent` au fournisseur d’identité. Cette option peut être utilisée dans certaines applications axées sur la sécurité, dans lesquelles la gouvernance de l’organisation exige que l’utilisateur voie s’afficher la boîte de dialogue de consentement chaque fois que l’application est utilisée.
- ``ForceLogin`` : permet au développeur d’application de faire afficher par le service une invite demandant à l’utilisateur d’entrer des informations d’identification, et même si cette invite utilisateur n’est pas nécessaire. Cette option peut s’avérer utile si l’acquisition d’un jeton échoue, afin de permettre à l’utilisateur de se reconnecter. Dans ce cas, MSAL envoie `prompt=login` au fournisseur d’identité. D’ailleurs, nous l’avons déjà vu utilisée dans certaines applications axées sur la sécurité, pour lesquelles la gouvernance de l’organisation exige que l’utilisateur se reconnecte chaque fois qu’il accède à des parties spécifiques d’une application.
- ``Never`` (pour .NET 4.5 et WinRT uniquement) n’affiche aucune invite utilisateur, mais tente à la place d’utiliser le cookie stocké dans l’affichage web incorporé masqué (voir ci-dessous : affichages web dans MSAL.NET). L’utilisation de cette option risque d’échouer ; dans ce cas `AcquireTokenInteractive` lève une exception pour prévenir qu’une interaction avec l’interface utilisateur est nécessaire, et que vous allez devoir utiliser un autre paramètre `Prompt`.
- ``NoPrompt``: n’envoie pas d’invite au fournisseur d’identité. Cette option n’est utile que pour les stratégies de modification de profil Azure AD B2C (consultez [Spécificités d’Azure AD B2C](https://aka.ms/msal-net-b2c-specificities)).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ce modificateur est utilisé dans un scénario élaboré, suivant lequel vous souhaitez obtenir le consentement préalable de l’utilisateur pour plusieurs ressources en amont (et ne souhaitez pas utiliser le consentement incrémentiel, qui est normalement utilisé avec MSAL.NET / la plateforme d’identités Microsoft v2.0). Consultez les détails dans le [Guide pratique pour obtenir le consentement préalable de l’utilisateur sur plusieurs ressources](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Autres paramètres facultatifs

Apprenez-en davantage sur tous les autres paramètres facultatifs de `AcquireTokenInteractive` en consultant la documentation de référence relative à [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

### <a name="acquire-tokens-via-the-protocol"></a>Acquérir des jetons via le protocole

Nous ne recommandons pas d’utiliser le protocole directement. Si vous le faites, l’application ne prendra pas en charge certains scénarios d’authentification unique (SSO), de gestion des périphériques et d’accès conditionnel.

Lorsque vous utilisez le protocole pour acquérir des jetons pour les applications mobiles, vous devez envoyer deux demandes : une pour obtenir un code d’autorisation, l’autre pour échanger ce dernier contre un jeton.

#### <a name="get-authorization-code"></a>Obtenir un code d’autorisation

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Obtenir un jeton d’accès et d’actualisation

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appel d’une API web](scenario-mobile-call-api.md)
