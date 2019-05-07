---
title: Application de bureau qui appelle web API (l’acquisition d’un jeton pour l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une application de bureau qui appelle des API web (l’acquisition d’un jeton pour l’application |)
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
ms.openlocfilehash: 6d4389af86e27ddb04f5a3e5f53c5509eeede005
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075339"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Application de bureau qui appelle des API - web acquérir un jeton

Une fois que vous avez créé vous `IPublicClientApplication`, vous allez l’utiliser pour acquérir un jeton que vous allez ensuite utiliser pour appeler une API web.

## <a name="recommended-pattern"></a>Modèle recommandé

L’API web est défini par ses `scopes`. Quelle que soit l’expérience que vous fournissez dans votre application, le modèle que vous souhaitez utiliser est :

- Systématiquement tente d’obtenir un jeton à partir du cache de jeton en appelant `AcquireTokenSilent`
- Si cet appel échoue, utilisez la `AcquireToken` flux que vous souhaitez utiliser (ici représentée par `AcquireTokenXX`)

```CSharp
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

Voici maintenant les détails des différentes façons pour acquérir des jetons dans une application de bureau

## <a name="acquiring-a-token-interactively"></a>L’acquisition d’un jeton de manière interactive

L’exemple suivant montre un minimum de code pour obtenir un jeton de manière interactive pour lire le profil utilisateur avec Microsoft Graph.

```CSharp
string[] scopes = new string["user.read"];
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

`AcquireTokenInteractive` a qu’un seul paramètre obligatoire ``scopes``, qui contient une énumération de chaînes qui définissent les étendues pour lequel un jeton est nécessaire. Si le jeton est pour Microsoft Graph, les étendues requis trouverez dans la référence de l’api de chaque API Microsoft graph dans la section nommée « Autorisations ». Par exemple, pour [répertorier les contacts de l’utilisateur](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), l’étendue « User.Read », « Contacts.Read » sera doivent être utilisés. Voir aussi [référence des autorisations Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Sur Android, vous devez également spécifier l’activité parente (à l’aide de `.WithParentActivityOrWindow`, voir ci-dessous) afin que le jeton obtient sur cette activité parent après l’interaction. Si vous ne le spécifiez pas, une exception est levée lors de l’appel `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Paramètres facultatifs spécifiques

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Étant interactif, interface utilisateur est important. `AcquireTokenInteractive` a un paramètre facultatif spécifique qui permet de spécifier, pour les plateformes qui l’accompagnent, le parent de l’interface utilisateur. Lorsqu’il est utilisé dans une application de bureau, `.WithParentActivityOrWindow` a un type différent selon la plateforme :

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Remarques :

- Sur .NET Standard, attendu `object` est un `Activity` sur Android, un `UIViewController` sur iOS, un `NSWindow` sur MAC et un `IWin32Window` ou `IntPr` sur Windows.
- Sur Windows, vous devez appeler `AcquireTokenInteractive` à partir de l’interface utilisateur de thread afin que le navigateur intégré Obtient le contexte de synchronisation de l’interface utilisateur approprié.  Ne pas appel à partir du thread d’interface utilisateur peut entraîner des messages pas pump correctement et/ou interblocage des scénarios avec l’interface utilisateur. Une de l’appel de MSAL à partir du thread d’interface utilisateur si vous n’êtes pas sur le thread d’interface utilisateur déjà consiste à utiliser le `Dispatcher` sur WPF.
- Si vous êtes à l’aide de WPF, pour obtenir une fenêtre à partir d’un contrôle WPF, vous pouvez utiliser `WindowInteropHelper.Handle` classe. L’appel est alors, à partir d’un contrôle WPF (`this`) :
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` permet de contrôler l’interactivité avec l’utilisateur en spécifiant une invite de commandes

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

La classe définit les constantes suivantes :

- ``SelectAccount``: force le STS pour présenter la boîte de dialogue de sélection de compte contenant les comptes pour lesquels l’utilisateur dispose d’une session. Cette option est utile lorsque vous souhaitent que les développeurs d’applications permettre à utilisateur de choisir parmi différentes identités. MSAL pour envoyer des lecteurs de cette option ``prompt=select_account`` au fournisseur d’identité. Cette option est la valeur par défaut, et c’est le cas de bon de travail de fournir la meilleure expérience possible selon les informations disponibles (compte), la présence d’une session pour l’utilisateur et ainsi de suite. ...). Ne la changez pas sauf si vous avez la bonne raison de le faire.
- ``Consent``: permet à l’application développeur pour forcer l’utilisateur invité à fournir son consentement même si l’autorisation a été accordée avant. Dans ce cas, MSAL envoie `prompt=consent` au fournisseur d’identité. Cette option peut être utilisée dans certaines applications de sécurité axée où la gouvernance de l’organisation exige que l’utilisateur voit s’afficher la boîte de dialogue de consentement chaque fois que l’application est utilisée.
- ``ForceLogin``: permet au développeur d’application pour que l’utilisateur d’informations d’identification demandé par le service même si cette invite utilisateur n’est pas nécessaire. Cette option peut être utile si l’acquisition d’un jeton échoue, pour permettre à l’utilisateur ré-connexion-in. Dans ce cas, MSAL envoie `prompt=login` au fournisseur d’identité. Nous avons vu là encore, il est utilisé dans certaines applications de sécurité axée où la gouvernance de l’organisation exige que l’utilisateur relogs-dans chaque fois qu’ils accèdent à des parties spécifiques d’une application.
- ``Never`` (pour .NET 4.5 et WinRT uniquement) n’invite pas l’utilisateur, mais au lieu de cela tentent d’utiliser le cookie stocké dans la vue web incorporées masqué (voir ci-dessous : Affichages Web dans MSAL.NET). À l’aide de cette option risque d’échouer et dans ce cas `AcquireTokenInteractive` lève une exception pour avertir qu’une interaction de l’interface utilisateur est nécessaire, et vous devez utiliser une autre `Prompt` paramètre.
- ``NoPrompt``: N’envoie pas d’invite au fournisseur d’identité. Cette option est uniquement utile pour les stratégies de modification de profil Azure AD B2C (consultez [B2C spécificités](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ce modificateur est utilisé dans un scénario avancé où vous souhaitez que l’utilisateur à accepter préalable à l’avance de plusieurs ressources (et ne souhaitez pas utiliser le consentement incrémentiel, qui est normalement utilisé avec MSAL.NET / v2.0 de plateforme d’identité Microsoft). Pour plus d’informations, consultez [procédures : inviter l’utilisateur à donner son consentement dès le départ pour plusieurs ressources](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi est un point d’extensibilité

`WithCustomWebUi` point d’extensibilité qui permet de que vous fournir votre propre interface utilisateur dans les applications de client public, et laisser l’utilisateur à passer par le point de terminaison /Authorize du fournisseur d’identité et permettent de les connecter et donner son consentement. Ensuite, MSAL.NET peut échanger le code d’authentification et obtenir un jeton. Par exemple, il est utilisé dans Visual Studio pour avoir électrons applications (par exemple les commentaires VS) fournissent l’interaction web, mais laisse MSAL.NET faire l’essentiel du travail. Vous pouvez également l’utiliser si vous souhaitez fournir l’automatisation d’interface utilisateur. Dans les applications de client public, MSAL.NET utilise la norme PKCE ([7636 RFC - clé de preuve pour l’échange de Code par les Clients publics OAuth](https://tools.ietf.org/html/rfc7636)) pour vous assurer que la sécurité est respectée : MSAL.NET uniquement peut échanger le code.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Comment utiliser WithCustomWebUi

Pour pouvoir utiliser `.WithCustomWebUI`, vous devez :
  
  1. Implémentez le `ICustomWebUi` interface (voir [ici](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Vous devez en fait à implémenter une méthode `AcquireAuthorizationCodeAsync` acceptant l’URL du code d’autorisation (calculé par MSAL.NET), lui permettre d’accéder via l’interaction avec le fournisseur d’identité et puis de retourner de nouveau l’URL par laquelle le fournisseur d’identité est avez appelé votre implémentation de retour (y compris le code d’autorisation). Si vous rencontrez des problèmes, votre implémentation doit lever une `MsalExtensionException` exception bien coopérer avec MSAL.
  2. Dans votre `AcquireTokenInteractive` appel, vous pouvez utiliser `.WithCustomUI()` modificateur en passant l’instance de votre personnalisation de l’interface utilisateur web

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Exemples d’implémentation de ICustomWebUi dans automation de test - SeleniumWebUI

L’équipe MSAL.NET ont réécrire nos tests d’interface utilisateur pour tirer parti de ce mécanisme d’extensibilité. Dans le cas où vous souhaitez avoir un coup de œil à la [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) classe dans le code source MSAL.NET

#### <a name="other-optional-parameters"></a>Autres paramètres facultatifs

En savoir plus sur tous les autres paramètres facultatifs pour `AcquireTokenInteractive` à partir de la documentation de référence [AcquireTokenInteractiveParameterBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Authentification Windows intégrée

Si vous souhaitez connecter un utilisateur de domaine un domaine ou Azure AD joint à un ordinateur, vous devez utiliser :

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Contraintes

- AcquireTokenByIntegratedWindowsAuth intégrée (IWA) n’est utilisable pour **fédérés** utilisateurs uniquement, c'est-à-dire, les utilisateurs créés dans Active Directory et soutenue par Azure Active Directory. Les utilisateurs créés directement dans AAD, sans sauvegarde de AD - **gérés** - ne peut pas utiliser ce flux d’authentification. Cette limitation n’affecte pas le flux de nom d’utilisateur/mot de passe.
- Authentification Windows intégrée est pour les applications écrites pour le .NET Framework, .NET Core et les plateformes UWP
- Authentification Windows intégrée n’ignore pas MFA (multi-factor authentication). Si MFA est configuré, IWA peut échouer si une authentification MFA est nécessaire, étant donné que l’authentification Multifacteur nécessite une interaction utilisateur.
  > [!NOTE]
  > Celle-ci est délicat. Authentification Windows intégrée est non interactif, mais 2FA nécessite l’interactivité utilisateur. Vous ne contrôlez pas lorsque le fournisseur d’identité demande 2FA à effectuer, fait de l’administrateur client. À partir de nos observations, à 2 facteurs est requis lorsque vous vous connectez à partir d’un autre pays, lorsque non connecté via un VPN à un réseau d’entreprise et parfois même lorsque connectés via VPN. Ne pensez pas un ensemble de règles déterministe, Azure Active Directory utilise IA en permanence apprendre si 2FA est requis. Vous devez recourir à une invite d’utilisateur (interactive d’authentification ou un appareil flux de code) si l’authentification intégrée Windows échoue.

- L’autorité est passé dans le `PublicClientApplicationBuilder` doit être :
  - client-ed (sous la forme `https://login.microsoftonline.com/{tenant}/` où `tenant` est le guid représentant l’ID de locataire ou un domaine associé au locataire.
  - pour les comptes professionnels et scolaires (`https://login.microsoftonline.com/organizations/`)

  > Comptes personnels Microsoft ne sont pas pris en charge (vous ne pouvez pas utiliser les locataires/Common ou /consumers)

- Étant donné que l’authentification Windows intégrée est un flux en mode silencieux :
  - l’utilisateur de votre application doit avoir précédemment a donné son consentement pour utiliser l’application
  - ou l’administrateur client doit avoir précédemment a donné son consentement à tous les utilisateurs dans le client à utiliser l’application.
  - En d’autres termes :
    - soit en tant que développeur avoir cliqué sur le **Grant** sur le portail Azure pour vous-même, bouton
    - ou un administrateur de locataires a appuyé sur le **Grant/revoke consentement de l’administrateur de domaine du locataire {}** situé dans le **autorisations d’API** onglet de l’inscription de l’application (consultez [ajouter des autorisations pour web Access API](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - ou vous avez fourni un moyen pour les utilisateurs à donner son consentement à l’application (consultez [solliciter le consentement de l’utilisateur individuel](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - ou vous avez fourni un moyen de l’administrateur de client à donner son consentement pour l’application (consultez [consentement de l’administrateur](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Ce flux est activé pour les applications de bureau .net, .net core et Windows universelle (UWP) des applications. Sur .NET core uniquement la surcharge qui prend le nom d’utilisateur est disponible, la plateforme .NET Core ne peut pas demander le nom d’utilisateur pour le système d’exploitation.
  
Pour plus d’informations sur le consentement, consultez [v2.0 autorisations et consentement](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Comment l’utiliser ?

Vous devez normalement qu’un seul paramètre (`scopes`). Cependant, en fonction de la façon dont votre administrateur de Windows a configuré les stratégies, il est possible que les applications sur votre ordinateur windows ne sont pas autorisées à rechercher l’utilisateur connecté. Dans ce cas, utilisez une deuxième méthode `.WithUsername()` et passez le nom d’utilisateur de l’utilisateur connecté en tant que format UPN - `joe@contoso.com`.

L’exemple suivant présente le cas plus récente, avec des explications du genre d’exceptions, que vous pouvez obtenir et leurs solutions d’atténuation

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Pour la liste des modificateurs possible sur AcquireTokenByIntegratedWindowsAuthentication, consultez [AcquireTokenByIntegratedWindowsAuthParameterBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Nom d’utilisateur / mot de passe

Vous pouvez également acquérir un jeton en fournissant le nom d’utilisateur et le mot de passe. Ce flux est limité et pas recommandé, mais il existe toujours utiliser les cas où il est nécessaire.

### <a name="this-flow-isnt-recommended"></a>Ce flux n’est pas recommandé.

Ce flux est **déconseillé** , car votre application de demander à un utilisateur pour leur mot de passe n’est pas sécurisée. Pour plus d’informations sur ce problème, consultez [cet article](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Le flux par défaut pour acquérir un jeton en mode silencieux sur les ordinateurs joints au domaine de Windows est [l’authentification Windows intégrée](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Sinon, vous pouvez également utiliser [flux de code d’appareil](https://aka.ms/msal-net-device-code-flow)

> Bien que cela est utile dans certains cas (scénarios DevOps), si vous souhaitez utiliser le nom d’utilisateur/mot de passe dans des scénarios interactifs où vous fournissez votre onw l’interface utilisateur, vous devez considérer vraiment sur le déplacement depuis celle-ci. À l’aide du nom d’utilisateur/mot de passe vous sont en donnant à distance un certain nombre de choses :

> - principaux clients d’identité moderne : mot de passe obtient capturé, relus. Étant donné que nous avons ce concept d’une clé secrète de partage qui peut être intercepté.
> Cela est incompatible avec sans mot de passe.
> - les utilisateurs qui ont besoin pour effectuer l’authentification Multifacteur ne pourront pas se connecter (comme il n’existe aucune interaction)
> - Les utilisateurs ne pourront pas l’authentification unique

### <a name="constraints"></a>Contraintes

Les contraintes suivantes s’appliquent également :

- Le flux de nom d’utilisateur/mot de passe n’est pas compatible avec l’accès conditionnel et l’authentification multifacteur : Par conséquent, si votre application s’exécute dans un locataire Azure AD où l’administrateur client requiert l’authentification multifacteur, vous ne pouvez pas utiliser ce flux. De nombreuses organisations le faire.
- Il fonctionne uniquement pour le travail et les comptes scolaires (pas MSA)
- Le flux est disponible sur les applications de bureau .net et .net core, mais pas sur UWP

### <a name="b2c-specifics"></a>Spécificités de B2C

[Plus d’informations sur l’utilisation de ROPC avec B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Comment l’utiliser ?

`IPublicClientApplication`contient la méthode `AcquireTokenByUsernamePassword`

L’exemple suivant présente un cas simplifié

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

L’exemple suivant présente le cas plus récente, avec des explications du genre d’exceptions, que vous pouvez obtenir et leurs solutions d’atténuation

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See http://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Pour plus d’informations sur tous les modificateurs peuvent être appliqués à `AcquireTokenByUsernamePassword`, consultez [AcquireTokenByUsernamePasswordParameterBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Outil de ligne de commande (sans le navigateur web)

### <a name="device-code-flow-why-and-how"></a>Code de l’appareil flux pourquoi ? Et comment ?

Si vous écrivez un outil de ligne de commande (n’ayant pas les contrôles Web) et ne peut pas ou ne souhaitez pas utiliser les flux précédents, vous devrez utiliser `AcquireTokenWithDeviceCode`.

Authentification interactive avec Azure AD nécessite un navigateur web (pour plus d’informations, consultez [l’utilisation des navigateurs web](https://aka.ms/msal-net-uses-web-browser)). Toutefois, pour authentifier les utilisateurs sur des appareils ou des systèmes d’exploitation qui ne fournissent pas un navigateur Web, flux de code d’appareil permet à l’utilisateur utiliser un autre appareil (par exemple un autre ordinateur ou un téléphone mobile) pour vous connecter manière interactive. En utilisant le flux de code d’appareil, l’application obtient des jetons via un processus en deux étapes spécialement conçues pour ces appareils/système d’exploitation. Exemples de ces applications sont des applications s’exécutant sur iOT ou des outils de ligne de commande (CLI). L’idée est que :

1. Chaque fois que l’authentification de l’utilisateur est requise, l’application fournit un code et invite l’utilisateur à utiliser un autre appareil (par exemple, un smartphone connecté à internet) pour accéder à une URL (par exemple, `http://microsoft.com/devicelogin`), où l’utilisateur est invité à entrer le code. Que fait, la page web entraîne l’utilisateur via une expérience d’authentification normal, y compris les invites de consentement et l’authentification multifacteur si nécessaire.

2. Après une authentification réussie, l’application de ligne de commande reçoit les jetons requis via un canal arrière et utilisera pour effectuer les appels d’API web dont il a besoin.

### <a name="code"></a>Code

`IPublicClientApplication`contient une méthode nommée `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Cette méthode prend comme paramètres :

- Le `scopes` pour demander un jeton d’accès pour
- Un rappel qui recevra le `DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

L’exemple de code suivant présente le cas plus récente, avec des explications du genre d’exceptions, que vous pouvez obtenir et l’atténuation des risques.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
  {
       // This will print the message on the console which tells the user where to go sign-in using
       // a separate browser and the code to enter once they sign in.
       // The AcquireTokenWithDeviceCode() method will poll the server after firing this
       // device code callback to look for the successful login of the user via that browser.
       // This background polling (whose interval and timeout data is also provided as fields in the
       // deviceCodeCallback class) will occur until:
       // * The user has successfully logged in via browser and entered the proper code
       // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
       // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
       //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
       Console.WriteLine(deviceCodeResult.Message);
       return Task.FromResult(0);
  }).ExecuteAsync();

  Console.WriteLine(result.Account.Username);
  return result;
 }
 catch (MsalServiceException ex)
 {
  // Kind of errors you could have (in ex.Message)

  // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
  // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
  // your public client application with the following authorities:
  // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

  // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
  // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

  // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
  // no active subscriptions for the tenant. Check with your subscription administrator.
  // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
  // tenantId (GUID) or tenant domain name.
 }
 catch (OperationCanceledException ex)
 {
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>Cache de jeton basée sur les fichiers

Dans MSAL.NET, un cache de jeton en mémoire est fourni par défaut.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>La sérialisation est personnalisable dans les applications de bureau Windows et web apps/web API

Dans le cas de .NET Framework et .NET core, si vous ne le faites pas toute information supplémentaire, le cache de jeton en mémoire dure toute la durée de l’application. Pour comprendre pourquoi la sérialisation n’est pas fournie dès le départ, n’oubliez pas les MSAL .NET applications de bureau/core peuvent être console ou les applications Windows (qui ont accès au système de fichiers), **mais également** applications Web ou API web. Ces applications Web et API web peuvent utiliser des mécanismes de cache spécifique comme bases de données, les caches distribués, et ainsi de suite les caches redis. Pour une application de cache de jeton persistant dans les applications de bureau .NET ou Core, vous devez personnaliser la sérialisation.

Classes et interfaces impliquées dans la sérialisation du cache de jeton sont les suivants :

- ``ITokenCache``, qui définit les événements pour vous abonner à des demandes de sérialisation de cache de jeton, ainsi que des méthodes pour sérialiser ou désérialiser le cache au niveau de différents formats (v3.0 ADAL, MSAL 2.x et la bibliothèque MSAL 3.x = ADAL v5.0)
- ``TokenCacheCallback`` un rappel transmis aux événements afin que vous pouvez gérer la sérialisation. elles amèneront être appelées avec des arguments de type ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` fournit uniquement la ``ClientId`` de l’application et une référence à l’utilisateur pour lequel le jeton est disponible

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET crée les caches de jeton pour vous et vous fournit le `IToken` mettre en cache lorsque vous appelez d’une application `GetUserTokenCache` et `GetAppTokenCache` méthodes. Vous ne sont pas censées pour implémenter l’interface vous-même. Votre responsabilité, lorsque vous implémentez une sérialisation cache de jeton personnalisé, est de :
>
> - Réagir aux `BeforeAccess` et `AfterAccess` « événements ». Le`BeforeAccess` délégué est responsable de désérialiser le cache, tandis que le `AfterAccess` un est responsable de la sérialisation du cache.
> - Partie de ces événements stocker ou de charger des objets BLOB, qui est transmises via l’argument d’événement pour tout stockage que vous souhaitez.

Les stratégies sont différents en fonction de si vous écrivez une sérialisation du cache de jetons pour une application de client public (bureau), ou une application cliente confidentielle (web application/API web, application démon).

Depuis la version 2.x de MSAL vous disposez de plusieurs options, selon si vous souhaitez sérialiser le cache uniquement pour le format MSAL.NET (cache format unifié qui est courant avec MSAL, mais également entre les plateformes), ou si vous souhaitez également prendre en charge la [hérités](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Sérialisation de cache de jeton d’ADAL V3.

La personnalisation de la sérialisation de cache de jeton pour partager l’état de l’authentification unique entre ADAL.NET 3.x, ADAL.NET 5.x et MSAL.NET est expliqué dans la partie de l’exemple suivant : [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Sérialisation du cache de jetons simple (MSAL uniquement)

Voici un exemple d’une implémentation naïve de sérialisation personnalisée d’un cache de jeton pour les applications de bureau. Voici le cache de jetons d’utilisateur dans un fichier dans le même dossier que l’application.

Une fois que vous générez l’application, vous activez la sérialisation en appelant ``TokenCacheHelper.EnableSerialization()`` en passant de l’application `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Cette classe d’assistance ressemble à l’extrait de code suivant :

```CSharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Un aperçu d’un cache de jeton qualité produit basé sur fichier un sérialiseur pour les applications de client public (applications de bureau s’exécutant sur Windows, Mac et linux) est disponible à partir de la [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) bibliothèque Open source. Vous pouvez l’inclure dans vos applications à partir du package nuget suivant : [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> Exclusion de responsabilité. La bibliothèque Microsoft.Identity.Client.Extensions.Msal est une extension sur MSAL.NET. Classes dans ces bibliothèques peuvent devenir leur façon MSAL.NET à l’avenir, en l’état ou avec les dernières modifications.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Sérialisation d’un cache de jeton double (cache MSAL unifiée + ADAL V3)

Si vous souhaitez implémenter la sérialisation du cache de jeton à la fois avec l’unifiés cache format (communes à ADAL.NET 4.x et MSAL.NET 2.x et avec les autres MSALs de la même génération ou une version antérieure, sur la même plateforme), vous pouvez Soyez inspiré par le code suivant :

```CSharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Cette fois la classe d’assistance se présente comme le code suivant :

```CSharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appeler une API web à partir de l’application de bureau](scenario-desktop-call-api.md)
