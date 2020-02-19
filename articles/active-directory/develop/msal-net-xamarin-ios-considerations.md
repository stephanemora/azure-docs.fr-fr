---
title: Considérations relatives à Xamarin iOS (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez les points à prendre en considération lors de l’utilisation de Xamarin iOS avec la Bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132470"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Considérations relatives à l’utilisation de Xamarin iOS avec MSAL.NET
Lorsque vous utilisez la Bibliothèque d’authentification Microsoft pour .NET (MSAL.NET) sur Xamarin iOS, vous devez : 

- Écraser et implémenter la fonction `OpenUrl` dans `AppDelegate`.
- Activer les groupes de trousseaux.
- Activer le partage du cache de jeton.
- Activer l’accès au trousseau.
- Comprendre les problèmes connus avec iOS 12 et l’authentification.

## <a name="implement-openurl"></a>Implémenter OpenUrl

Remplacez la méthode `OpenUrl` de la classe dérivée `FormsApplicationDelegate` et appelez `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`. Voici un exemple :

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Effectuez également les tâches suivantes : 
* Définissez un modèle d’URL.
* Exigez des autorisations pour permettre à votre application d’appeler une autre application.
* Prévoyez un formulaire spécifique pour l’URL de redirection.
* Inscrivez l’URI de redirection sur le [portail Azure](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Activer l’accès au trousseau

Pour activer l’accès au trousseau, assurez-vous que votre application a un groupe d’accès au trousseau. Vous pouvez définir le groupe d’accès au trousseau lorsque vous créez votre application à l’aide de l’API `WithIosKeychainSecurityGroup()`.

Pour tirer parti du cache et de l’authentification unique, attribuez la même valeur au groupe d’accès au trousseau dans toutes vos applications.

Cet exemple de configuration utilise MSAL 4.x :
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Activez également l’accès au trousseau dans le fichier `Entitlements.plist`. Utilisez le groupe d’accès suivant ou votre propre groupe d’accès.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Lorsque vous utilisez l’API `WithIosKeychainSecurityGroup()`, MSAL ajoute automatiquement votre groupe de sécurité à la fin de l’*ID d’équipe* de l’application (`AppIdentifierPrefix`). MSAL ajoute votre groupe de sécurité parce que, lorsque vous générez votre application dans Xcode, elle en fait de même. C’est la raison pour laquelle les droits d’utilisation dans le fichier `Entitlements.plist` doivent inclure `$(AppIdentifierPrefix)` devant le groupe d’accès au trousseau.

Pour plus d’informations, voir la [documentation sur les droits d’utilisation iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Activer le partage du cache de jeton entre des applications iOS

Depuis MSAL 2.x, vous pouvez spécifier un groupe d’accès au trousseau pour conserver le cache de jeton dans plusieurs applications. Ce paramètre vous permet de partager le cache de jeton entre plusieurs applications qui ont le même groupe d’accès au trousseau. Vous pouvez partager le cache de jeton des applications [ADAL.NET](https://aka.ms/adal-net), des applications MSAL.NET Xamarin.iOS et des applications iOS natives développées dans [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Le partage du cache de jeton permet une authentification unique entre toutes les applications qui utilisent le même groupe d’accès au trousseau.

Pour activer ce partage du cache, utilisez la méthode `WithIosKeychainSecurityGroup()` pour définir le groupe d’accès au trousseau sur la même valeur dans toutes les applications qui partagent le cache. Le premier exemple de code de cet article montre comment utiliser la méthode.

Plus haut dans cet article, vous avez appris que MSAL ajoute `$(AppIdentifierPrefix)` chaque fois que vous utilisez l’API `WithIosKeychainSecurityGroup()`. MSAL ajoute cet élément parce que l’ID d’équipe `AppIdentifierPrefix` garantit que seules des applications créées par le même serveur de publication peuvent partager un accès au trousseau.

> [!NOTE]
> La propriété `KeychainSecurityGroup` est déconseillée.
> 
> À partir de MSAL 2.x, les développeurs ont été contraints d’inclure le préfixe `KeychainSecurityGroup` quand ils utilisaient la propriété `TeamId`. Toutefois, depuis MSAL 2.7. x, lorsque vous utilisez la nouvelle propriété `iOSKeychainSecurityGroup`, MSAL résout le préfixe `TeamId` lors de l’exécution. Lorsque vous utilisez cette propriété, n’incluez pas le préfixe `TeamId` dans la valeur. Le préfixe n’est pas obligatoire.
>
> Étant donné que la propriété `KeychainSecurityGroup` est obsolète, utilisez la propriété `iOSKeychainSecurityGroup`.

### <a name="use-microsoft-authenticator"></a>Utiliser Microsoft Authenticator

Votre application peut utiliser Microsoft Authenticator en tant que répartiteur pour activer les fonctionnalités suivantes :

- **SSO** : Lorsque vous activez la SSO, vos utilisateurs n’ont pas besoin de se connecter à chaque application.
- **Identification de périphérique** : Utilisez l’identification de périphérique pour effectuer l’authentification en accédant au certificat de l’appareil. Ce certificat est créé sur l’appareil quand celui-ci est joint à l’espace de travail. Votre application sera prête si les administrateurs clients activent l’accès conditionnel lié aux appareils.
- **Vérification de l’identification de l’application** : Quand une application appelle le répartiteur, elle transmet son URL de redirection. Le répartiteur vérifie l’URL de redirection.

Pour plus de détails sur l’activation d’un répartiteur, voir [Utiliser Microsoft Authenticator ou le portail d’entreprise Microsoft Intune sur des applications Xamarin iOS et Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Problèmes connus avec iOS 12 et l’authentification
Microsoft a publié un [avis de sécurité](https://github.com/aspnet/AspNetCore/issues/4647) à propos d’une incompatibilité entre iOS 12 et certains types d’authentifications. L’incompatibilité interrompt les connexions aux réseaux sociaux, WSFed et OIDC. L’avis de sécurité aide les développeurs à comprendre comment supprimer les restrictions de sécurité ASP.NET de leurs applications pour les rendre compatibles avec iOS 12.  

Lors du développement d’applications MSAL.NET sur Xamarin iOS, vous pouvez rencontrer une boucle infinie lorsque vous tentez de vous connecter à des sites web à partir d’iOS 12. Ce comportement est similaire à ce [problème ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Vous pouvez également constater une interruption de l’authentification ASP.NET Core OIDC avec iOS 12 Safari. Pour plus d’informations, voir ce [problème WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les propriétés pour Xamarin iOS, voir le paragraphe [Considérations spécifiques d’iOs](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) dans le fichier README.md de l’exemple suivant :

Exemple | Plateforme | Description
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, plateforme Windows universelle (UWP) | Simple application Xamarin.Forms simple qui montre comment utiliser MSAL pour authentifier des comptes personnels Microsoft et Azure AD via le point de terminaison Azure AD 2.0. L’application montre également comment utiliser le jeton obtenu pour accéder à Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->