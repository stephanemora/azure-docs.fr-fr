---
title: Considérations relatives à Xamarin iOS (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: En savoir plus sur les considérations spécifiques lors de l’utilisation de Xamarin iOS avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f74c1f515df23a89af7cf50a208a9965865f6edf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424126"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Considérations spécifiques à Xamarin iOS avec MSAL.NET
Sur Xamarin iOS, il existe plusieurs considérations à prendre en compte lors de l’utilisation de MSAL.NET

- [Problèmes connus avec iOS 12 et l’authentification](#known-issues-with-ios-12-and-authentication)
- [Écraser et implémenter la fonction `OpenUrl` dans le `AppDelegate`](#implement-openurl)
- [Activer les groupes de trousseau](#enable-keychain-access)
- [Activer le partage du cache de jeton](#enable-token-cache-sharing-across-ios-applications)
- [Activer l’accès au trousseau](#enable-keychain-access)

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

### <a name="enable-keychain-access"></a>Activer l’accès au trousseau

Pour activer l’accès au trousseau, votre application doit avoir un groupe d’accès au trousseau.
Vous pouvez définir votre groupe d’accès au trousseau à l’aide de l’API `WithIosKeychainSecurityGroup()` lors de la création de votre application, comme indiqué ci-dessous :

Pour tirer parti du cache et de l’authentification unique, vous devez attribuer la même valeur au groupe d’accès Keychain de toutes vos applications.

Voici un exemple avec la bibliothèque MSAL v4.x :
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Cela *s’ajoute* à l’activation de l’accès au trousseau dans le fichier `Entitlements.plist`, à l’aide du groupe d’accès ci-dessous ou de votre propre groupe d’accès :

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Lors de l’utilisation de l’API `WithIosKeychainSecurityGroup()`, MSAL ajoute automatiquement votre groupe de sécurité à la fin de l’*ID d’équipe* de l’application (AppIdentifierPrefix), car lorsque vous générez votre application à l’aide de Xcode, elle fait de même. Pour plus d’informations, consultez la [documentation sur les droits iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). C’est pourquoi `$(AppIdentifierPrefix)` doit se trouver avant le groupe d’accès Keychain dans la liste `Entitlements.plist` des droits.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Activer le partage du cache de jeton entre des applications iOS

Depuis MSAL 2.x, vous pouvez spécifier un groupe d’accès au trousseau à utiliser pour la conservation du cache de jeton sur plusieurs applications. Ce paramètre vous permet de partager le cache de jeton entre plusieurs applications ayant le même groupe d’accès au trousseau, dont celles développées avec [ADAL.NET](https://aka.ms/adal-net), les applications MSAL.NET Xamarin.iOS et les applications natives iOS développées avec [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Le partage du cache de jeton permet une authentification unique entre toutes les applications qui utilisent le même groupe d’accès au trousseau.

Pour activer ce partage du cache, vous devez définir l’utilisation de la méthode « WithIosKeychainSecurityGroup() » pour définir le groupe d’accès au trousseau sur la même valeur dans toutes les applications qui partagent le même cache, comme indiqué dans l’exemple ci-dessus.

Il a été mentionné précédemment que MSAL ajoute le préfixe $ (AppIdentifierPrefix) chaque fois que vous utilisez l’API `WithIosKeychainSecurityGroup()`. Cela est dû au fait que AppIdentifierPrefix ou l’« ID d’équipe » est utilisé pour garantir que seules les applications créées par le même éditeur peuvent partager l’accès au trousseau.

> [!NOTE]
> **La propriété `KeychainSecurityGroup` est déconseillée.**
> 
> Auparavant, dans la bibliothèque MSAL 2.x, les développeurs étaient contraints d’inclure le préfixe TeamId lorsqu’ils utilisaient la propriété `KeychainSecurityGroup`.
>
>  À partir de MSAL 2.7.x, lors de l’utilisation de la nouvelle propriété `iOSKeychainSecurityGroup`, MSAL permet de résoudre le préfixe TeamId lors de l’exécution. Lorsque vous utilisez cette propriété, la valeur ne doit pas contenir le préfixe TeamId.
>  Utilisez la nouvelle propriété `iOSKeychainSecurityGroup`, qui ne nécessite pas de préfixe TeamId de votre part, car la propriété `KeychainSecurityGroup` précédente est obsolète.

### <a name="use-microsoft-authenticator"></a>Utiliser Microsoft Authenticator

Votre application peut utiliser Microsoft Authenticator (un répartiteur) pour activer :

- L’authentification unique. Vos utilisateurs n’ont pas besoin de se connecter à chaque application.
- L’identification des appareils. En accédant au certificat de l’appareil qui a été créé sur celui-ci lors de son rattachement à un espace de travail. Votre application sera prête si les administrateurs clients activent l’accès conditionnel lié aux appareils.
- Vérification de l’identification de l’application. Lorsqu’une application appelle le répartiteur, elle transmet son URL de redirection et le répartiteur la vérifie.

Pour plus de détails sur l’activation du répartiteur, voir [Utiliser le portail d'entreprise Microsoft Authenticator ou Microsoft Intune sur des applications Xamarin iOS et Android](msal-net-use-brokers-with-xamarin-apps.md).

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Exemple illustrant des propriétés spécifiques d’iOS Xamarin

Vous trouverez plus de détails dans le paragraphe [Considérations spécifiques pour iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) du fichier readme.md de l’exemple suivant :

Exemple | Plateforme | Description
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Une application Xamarin Forms simple expliquant comment utiliser MSAL pour authentifier MSA et Azure AD via le point de terminaison AAD V2.0, et comment accéder à Microsoft Graph avec le jeton résultant.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->

## <a name="known-issues-with-ios-12-and-authentication"></a>Problèmes connus avec iOS 12 et l’authentification
Microsoft a publié un [avis de sécurité](https://github.com/aspnet/AspNetCore/issues/4647) pour fournir des informations à propos d’une incompatibilité entre iOS 12 et certains types d’authentification. L’incompatibilité interrompt les connexions aux réseaux sociaux, WSFed et OIDC. Cet avis fournit également des conseils sur ce que les développeurs peuvent faire pour supprimer les restrictions de sécurité actuelles ajoutées par ASP.NET à leurs applications pour qu’elles deviennent compatibles avec iOS 12.  

Lors du développement d’applications MSAL.NET sur Xamarin iOS, vous pouvez voir une boucle infinie lorsque vous tentez de vous connecter à des sites web à partir d’iOS 12 (problème semblable à [ce problème ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)). 

Vous pouvez également constater une interruption de l’authentification ASP.NET Core OIDC avec iOS 12 Safari, comme décrit dans cet [problème WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).
