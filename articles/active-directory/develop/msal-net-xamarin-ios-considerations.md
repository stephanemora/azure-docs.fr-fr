---
title: Considérations Xamarin iOS (Microsoft Authentication Library pour .NET) | Azure
description: En savoir plus sur les considérations spécifiques lors de l’utilisation de Xamarin iOS avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
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
ms.openlocfilehash: bf236bff2300129ec97d3b8946c4c2a2748bca77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602136"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Considérations spécifiques à Xamarin iOS avec MSAL.NET
Sur Xamarin iOS, il existe plusieurs considérations à prendre en compte lors de l’utilisation de MSAL.NET

- [Problèmes connus avec iOS 12 et l’authentification](#known-issues-with-ios-12-and-authentication)
- [Écraser et implémenter la fonction `OpenUrl` dans le `AppDelegate`](#implement-openurl)
- [Activer les groupes de trousseau](#enable-keychain-groups)
- [Activer le partage du cache de jeton](#enable-token-cache-sharing-across-ios-applications)
- [Activer l’accès au trousseau](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Problèmes connus avec iOS 12 et l’authentification
Microsoft a publié un [avis de sécurité](https://github.com/aspnet/AspNetCore/issues/4647) pour fournir des informations à propos d’une incompatibilité entre iOS 12 et certains types d’authentification. L’incompatibilité interrompt les connexions aux réseaux sociaux, WSFed et OIDC. Cet avis fournit également des conseils sur ce que les développeurs peuvent faire pour supprimer les restrictions de sécurité actuelles ajoutées par ASP.NET à leurs applications pour qu’elles deviennent compatibles avec iOS 12.  

Lors du développement d’applications MSAL.NET sur Xamarin iOS, vous pouvez voir une boucle infinie lorsque vous tentez de vous connecter à des sites Web à partir d’iOS 12 (semblable à ce [problème ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Vous pouvez également constater une interruption de l’authentification ASP.NET Core OIDC avec iOS 12 Safari, comme décrit dans cet [problème WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implémenter OpenUrl

Tout d’abord, vous devez remplacer la méthode `OpenUrl` de la classe `FormsApplicationDelegate` dérivée et appeler `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Vous devez également définir un modèle d’URL, demander des autorisations pour que votre application appelle une autre application, disposer d’une forme spécifique pour l’URL de redirection et inscrire cette URL de redirection dans le [Portail Azure](https://portal.azure.com).

## <a name="enable-keychain-groups"></a>Activer les groupes de trousseau

Afin de faire fonctionner le cache de jeton et la méthode `AcquireTokenSilentAsync`, plusieurs étapes sont nécessaires :
1. Activer l’accès au trousseau dans votre fichier *`* Entitlements.plist* et spécifiez les **groupes de trousseaux** dans votre identificateur d’offres groupées.
2. Sélectionnez le fichier *`*Entitlements.plist*`* dans le champ **Droits personnalisés** dans la fenêtre **Vue de la signature d’offre groupée** des options de projets iOS.
3. Lors de la signature d’un certificat, assurez-vous que XCode utilise le même ID Apple.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Activer le partage du cache de jeton entre des applications iOS

En commençant par la bibliothèque MSAL 2.x, vous pouvez spécifier un groupe de sécurité de trousseau à utiliser pour la conservation du cache de jeton sur plusieurs applications. Cela vous permet de partager le cache de jeton entre plusieurs applications ayant le même groupe de sécurité de trousseau, dont celles développées avec [ADAL.NET](https://aka.ms/adal-net), les applications MSAL.NET Xamarin.iOS et les applications natives iOS développées avec [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Le partage du cache de jeton permet une authentification unique (SSO) entre toutes les applications qui utilisent le même groupe de sécurité de trousseau.

Pour activer l’authentification unique, vous devez définir la propriété `PublicClientApplication.iOSKeychainSecurityGroup` sur la même valeur dans toutes les applications.

Voici un exemple avec la bibliothèque MSAL v3.x :
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Voici un exemple avec la bibliothèque MSAL v2.7.x :

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> La propriété `KeychainSecurityGroup` est obsolète. Auparavant, dans la bibliothèque MSAL 2.x, les développeurs étaient contraints d’inclure le préfixe TeamId lorsqu’ils utilisaient la propriété `KeychainSecurityGroup`. 
> 
> Désormais, à partir de la bibliothèque MSAL 2.7.x, MSAL résoudra le préfixe TeamId lors de l’exécution lorsque vous utilisez la propriété `iOSKeychainSecurityGroup`. Lorsque vous utilisez cette propriété, la valeur ne doit pas contenir le préfixe TeamId. 
> 
> Utilisez la nouvelle propriété `iOSKeychainSecurityGroup`, qui ne nécessite pas de préfixe TeamId de la part des développeurs. La propriété `KeychainSecurityGroup` est désormais obsolète. 

## <a name="enable-keychain-access"></a>Activer l’accès au trousseau

Dans les bibliothèques MSAL 2.x et ADAL 4.x, le préfixe TeamId est utilisé pour accéder au trousseau, ce qui permet aux bibliothèques d’authentification de fournir une authentification unique (SSO) entre les applications du même éditeur. 

Qu’est-ce que le préfixe [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId) ? Il s’agit d’un identificateur unique (professionnel ou personnel) dans l’App Store. La valeur AppId est unique pour chaque application. Si vous avez plusieurs applications, le préfixe TeamId sera identique pour toutes les applications, mais la valeur AppId sera différente. Le groupe d’accès au trousseau présente automatiquement le préfixe TeamId pour chaque groupe du système. C’est de cette manière que le système d’exploitation force les applications du même éditeur à accéder au trousseau partagé. 

Lors de l’initialisation de `PublicClientApplication`, si vous recevez un `MsalClientException` avec le message : `TeamId returned null from the iOS keychain...`, vous devez effectuer les opérations suivantes dans l’application iOS Xamarin :

1. Dans Visual Studio, sous l’onglet Déboguer, accédez aux propriétés nameOfMyApp.iOS.
2. Puis accédez à Signature d’offre groupée iOS 
3. Sous Droits personnalisés, cliquez sur le ... puis sélectionnez le fichier Entitlements.plist depuis votre application
4. Dans le fichier csproj de l’application iOS, vous devez désormais trouver cette ligne incluse : `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **Régénérez** le projet.

Cela *s’ajoute* à l’activation de l’accès au trousseau dans le fichier `Entitlements.plist`, à l’aide du groupe d’accès ci-dessous ou de votre propre groupe d’accès :

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez plus de détails dans le paragraphe [Considérations spécifiques pour iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) du fichier readme.md de l’exemple suivant :

Exemple | Plateforme | Description 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Une application Xamarin Forms simple expliquant comment utiliser MSAL pour authentifier MSA et Azure AD via le point de terminaison AAD V2.0, et comment accéder à Microsoft Graph avec le jeton résultant. <br>![Topologie](media/msal-net-xamarin-ios-considerations/topology.png)