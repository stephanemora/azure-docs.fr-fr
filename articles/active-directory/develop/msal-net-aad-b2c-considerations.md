---
title: Azure AD B2C (bibliothèque d’authentification Microsoft pour .NET) | Azure
description: En savoir plus sur les considérations spécifiques lors de l’utilisation d’Azure AD B2C avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c26a5007c2dcaa5d41be46f685f0f259866ca2c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544070"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>MSAL.NET permet de connecter des utilisateurs avec des identités sociales

Vous pouvez utiliser MSAL.NET pour connecter les utilisateurs avec des identités sociales à l’aide de [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C repose sur la notion de stratégies. Dans MSAL.NET, en spécifiant une stratégie se traduit par fournissant une autorité.

- Lorsque vous instanciez l’application de client public, vous devez spécifier la stratégie d’autorité.
- Lorsque vous souhaitez appliquer une stratégie, vous devez appeler une substitution de `AcquireTokenInteractive` contenant un `authority` paramètre.

Cette page est de MSAL 3.x. Si vous êtes intéressé par MSAL 2.x, consultez [spécificités d’Azure AD B2C dans MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autorité pour un locataire d’Azure AD B2C et une stratégie

L’autorité à utiliser est `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` où :

- `tenant` est le nom du locataire Azure AD B2C, 
- `policyName` le nom de la stratégie à appliquer (par exemple « b2c_1_susi » pour les connexions/inscriptions-des).

La recommandation actuelle à partir d’Azure AD B2C consiste à utiliser `b2clogin.com` en tant que l’autorité. Par exemple : `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Pour plus d’informations, consultez ce [documentation](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>L’instanciation de l’application

Lorsque vous générez l’application, vous devez fournir l’autorité.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Acquérir un jeton pour appliquer une stratégie

L’acquisition d’un jeton pour un B2C de AD Azure API protégée dans une application de client public vous oblige à utiliser les remplacements avec une autorité de :

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

par :

- `policy` en cours de l’une des chaînes précédentes (par exemple `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` est une méthode qui trouve un compte pour une stratégie donnée. Exemple :

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

Appliquer une stratégie (par exemple laisser l’utilisateur final modifier son profil ou réinitialiser leur mot de passe) est actuellement effectuée en appelant `AcquireTokenInteractive`. Vous n’utilisez pas le jeton retourné dans le cas de ces deux stratégies d’authentification result.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Cas spécial de stratégies ResetPassword et de modifier le profil

Lorsque vous souhaitez fournir une expérience où vos utilisateurs finaux se connecter avec une identité sociale et ensuite modifier son profil que vous souhaitez appliquer la stratégie de modifier le profil Azure AD B2C. Pour ce faire, consiste en appelant `AcquireTokenInteractive` avec l’autorité spécifique pour cette stratégie et d’une invite de la valeur `Prompt.NoPrompt` afin d’éviter la boîte de dialogue de sélection de compte à afficher (car l’utilisateur est déjà connecté)

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Ressources propriétaire du mot de passe (ROPC) avec Azure AD B2C
Pour plus d’informations sur le flux ROPC, consultez cet [documentation](v2-oauth-ropc.md).

Ce flux est **déconseillé** , car votre application de demander à un utilisateur pour leur mot de passe n’est pas sécurisée. Pour plus d’informations sur ce problème, consultez [cet article](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

En utilisant le nom d’utilisateur/mot de passe, vous sont en donnant à distance un certain nombre de choses :
- principaux clients d’identité moderne : mot de passe obtient capturé, relus. Étant donné que nous avons ce concept d’une clé secrète de partage qui peut être intercepté. Cela est incompatible avec sans mot de passe.
- Les utilisateurs qui ont besoin pour effectuer l’authentification Multifacteur ne pourront se connecter (comme il n’existe aucune interaction).
- Les utilisateurs ne pourront pas l’authentification unique.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configurer le flux ROPC dans Azure AD B2C
Dans votre locataire Azure AD B2C, créez un nouveau flux d’utilisateur et sélectionnez **connectez-vous à l’aide de ROPC**. Cela permettra la stratégie ROPC pour votre client. Consultez [configurer les informations d’identification de mot de passe de propriétaire de la ressource de flux](/azure/active-directory-b2c/configure-ropc) pour plus d’informations.

`IPublicClientApplication` contient une méthode :
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Cette méthode prend comme paramètres :
- Le *étendues* pour demander un jeton d’accès pour.
- Un *nom d’utilisateur*.
- Une chaîne SecureString *mot de passe* pour l’utilisateur.

Pensez à utiliser l’autorité qui contient la stratégie ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Limitations du flux ROPC
 - Le flux ROPC **fonctionne uniquement pour les comptes locaux** (dans lequel vous inscrivez avec Azure AD B2C à l’aide d’un e-mail ou le nom d’utilisateur). Ce flux ne fonctionne pas si la fédération à l’un des fournisseurs d’identité pris en charge par Azure AD B2C (Facebook, Google, etc..).
 - Actuellement, il est **aucun jeton id_token ne retourné à partir d’Azure AD B2C** lors de l’implémentation du flux ROPC de la bibliothèque MSAL. Cela signifie que d’un objet de compte ne peut pas être créé, dans le cache, il y aura aucun compte et qu’aucun utilisateur. Le flux AcquireTokenSilent ne fonctionne pas dans ce scénario. Toutefois, ROPC n’affiche pas d’une interface utilisateur, par conséquent, il n’y aura aucun impact sur l’expérience utilisateur.

## <a name="google-auth-and-embedded-webview"></a>Authentification de Google et Embedded Webview

Si vous êtes un développeur Azure AD B2C à l’aide de Google comme fournisseur d’identité nous recommand vous utiliser le navigateur du système, comme Google n’autorise pas [l’authentification à partir de vues Web incorporées](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Actuellement, `login.microsoftonline.com` est une autorité approuvée avec Google. À l’aide de cette autorité fonctionnera avec embedded webview. Toutefois l’utilisation `b2clogin.com` n’est pas une autorité approuvée avec Google, donc les utilisateurs ne seront pas en mesure d’authentifier.

Nous fournissons une mise à jour le wiki et cela [problème](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) si les choses changent.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Mise en cache avec Azure AD B2C dans MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Problème connu avec Azure AD B2C

MSAL.Net prend en charge un [cache des jetons](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Le jeton de clé de la mise en cache est basé sur les revendications retournées par le fournisseur d’identité. Actuellement MSAL.Net a besoin de deux revendications pour générer une clé de cache de jeton :  
- `tid` qui est l’ID de locataire Azure AD, et 
- `preferred_username` 

Les deux de ces revendications sont manquantes dans la plupart des scénarios Azure AD B2C. 

L’impact sur le client est que lorsque vous tentez d’afficher le champ de nom d’utilisateur, vous obtenez « Manquant à partir de la réponse de jeton » comme valeur ? Dans ce cas, il s’agit, car Azure AD B2C ne retourne pas une valeur dans le IdToken pour le preferred_username en raison des limitations avec les comptes de réseaux sociaux et les fournisseurs d’identité externes (fournisseurs d’identité). Azure AD renvoie une valeur pour preferred_username, car il sait que l’utilisateur, mais pour Azure AD B2C, étant donné que l’utilisateur peut se connecter à un compte local, Facebook, Google, GitHub, etc. il n’est pas une valeur cohérente pour Azure AD B2C à utiliser pour preferred_username. Pour débloquer MSAL à partir du déploiement de compatibilité de cache avec la bibliothèque ADAL, nous décidé d’utiliser « Absents de la réponse de jeton » notre côté lorsque vous traitez avec les comptes Azure AD B2C lors de la IdToken ne retourne rien pour preferred_username. MSAL doit retourner une valeur pour preferred_username maintenir la compatibilité de cache entre les bibliothèques.

### <a name="workarounds"></a>Solutions de contournement

#### <a name="mitigation-for-the-missing-tenant-id"></a>Réduction des risques pour l’ID de client manquant

La solution de contournement suggérée consiste à utiliser le [par la stratégie de mise en cache](#acquire-a-token-to-apply-a-policy)

Vous pouvez également utiliser le `tid` de revendication, si vous utilisez le [stratégies personnalisées B2C](https://aka.ms/ief), car il fournit la fonctionnalité permettant de retourner des revendications supplémentaires à l’application. Pour en savoir plus sur [Transformation des revendications](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Atténuation pour « Manquant à partir de la réponse de jeton »
Une option consiste à utiliser la revendication « name » comme nom d’utilisateur par défaut. Le processus est mentionné dans ce [B2C doc](/azure/active-directory-b2c/active-directory-b2c-reference-policies#frequently-asked-questions) -> « dans la colonne de la revendication de retour, choisissez les revendications à retourner dans les jetons d’authentification à votre application après une expérience de modification de profil réussie. Par exemple, sélectionnez nom d’affichage, Code Postal ».

## <a name="next-steps"></a>Étapes suivantes 

Plus de détails sur l’acquisition de jetons de manière interactive avec MSAL.NET pour les applications Azure AD B2C sont fournies dans l’exemple suivant.

| Exemple | Plateforme | Description |
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Une application Xamarin Forms simple expliquant comment utiliser MSAL.NET pour authentifier les utilisateurs via Azure AD B2C et accéder à une API Web avec les jetons générés.|
