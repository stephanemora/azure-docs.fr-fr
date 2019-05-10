---
title: Considérations relatives à iOS Xamarin (bibliothèque d’authentification Microsoft pour .NET) | Azure
description: En savoir plus sur les considérations spécifiques lorsque vous utilisez Xamarin iOS avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: bb07fa00b9e1d917cb64df18fff6466dd5c0193d
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406988"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Considérations de spécifique à iOS Xamarin avec MSAL.NET
Sur Xamarin iOS, il existe plusieurs considérations que vous devez prendre en compte lors de l’utilisation de MSAL.NET

- [Problèmes connus avec iOS 12 et authentification](#known-issues-with-ios-12-and-authentication)
- [Substituez et implémentez la `OpenUrl` fonctionner dans le `AppDelegate`](#implement-openurl)
- [Activer les groupes de trousseau](#enable-keychain-groups)
- [Activer le partage du cache de jeton](#enable-token-cache-sharing-across-ios-applications)
- [Activer l’accès au trousseau](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Problèmes connus avec iOS 12 et authentification
Microsoft a publié un [avis de sécurité](https://github.com/aspnet/AspNetCore/issues/4647) pour fournir des informations à propos d’une incompatibilité entre iOS12 et certains types d’authentification. L’incompatibilité sauts sociale, les connexions ws-FED et OIDC. Cet avis fournit également des conseils sur ce que les développeurs peuvent faire pour supprimer les restrictions de sécurité actuel ajoutées par ASP.NET à leurs applications pour devenir compatibles avec iOS12.  

Lorsque vous développez des applications de MSAL.NET sur Xamarin iOS, vous pouvez voir une boucle infinie lorsque vous tentez de vous connecter à des sites Web à partir d’iOS 12 (semblable à celle-ci [ADAL problème](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Vous pouvez également voir une rupture dans l’authentification ASP.NET Core OIDC avec iOS 12 Safari, comme décrit dans cet [WebKit problème](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implémenter OpenUrl

Vous devez d’abord remplacer le `OpenUrl` méthode de la `FormsApplicationDelegate` dérivée de classe et appelez `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Vous devez également définir un schéma d’URL, nécessitent des autorisations pour votre application appeler une autre application, ont une forme spécifique pour l’URL de redirection et inscrire cette URL de redirection dans le [Azure portal](https://portal.azure.com).

## <a name="enable-keychain-groups"></a>Activer les groupes de trousseau

Afin de rendre le jeton de travail de cache et ont la `AcquireTokenSilentAsync` travail de la méthode, plusieurs étapes sont nécessaires :
1. Activer l’accès au trousseau dans votre *`* Entitlements.plist* fichier et spécifiez le **groupes de trousseaux** dans votre identificateur d’offre groupée.
2. Sélectionnez *`*Entitlements.plist*`* de fichiers dans le **droits personnalisés** champ dans la fenêtre des options projets iOS **vue de signature de Bundle**.
3. Lorsqu’un certificat de signature, assurez-vous que XCode utilise le même ID Apple.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Activer le cache de jeton partage entre applications iOS

À compter de la bibliothèque MSAL 2.x, vous pouvez spécifier un groupe de sécurité de trousseau à utiliser pour la persistance du cache de jeton entre plusieurs applications. Cela vous permet de partager le cache de jeton entre plusieurs applications ayant le même groupe de sécurité de trousseau dont celles développées avec [ADAL.NET](https://aka.ms/adal-net), Xamarin.iOS de MSAL.NET et des applications natives iOS développées avec [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Le cache de jeton de partage permet l’authentification unique (SSO) entre toutes les applications qui utilisent le même groupe de sécurité trousseau.

Pour activer l’authentification unique, vous devez définir le `PublicClientApplication.iOSKeychainSecurityGroup` propriété sur la même valeur dans toutes les applications.

Un exemple de ceci à l’aide de MSAL v3.x serait :
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Un exemple de ceci à l’aide de MSAL v2.7.x serait :

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> Le `KeychainSecurityGroup` propriété a été déconseillée. Auparavant, dans la bibliothèque MSAL 2.x, les développeurs ont été contraints d’inclure le préfixe d’ID d’équipe lorsque vous utilisez le `KeychainSecurityGroup` propriété. 
> 
> Maintenant, à compter de MSAL 2.7.x, MSAL résoudra le préfixe d’ID d’équipe lors de l’exécution lorsque vous utilisez le `iOSKeychainSecurityGroup` propriété. Lorsque vous utilisez cette propriété, la valeur ne doit pas contenir le préfixe d’ID d’équipe. 
> 
> Utilisez la nouvelle `iOSKeychainSecurityGroup` propriété, qui ne nécessite pas aux développeurs de fournir l’ID d’équipe. Le `KeychainSecurityGroup` propriété est désormais obsolète. 

## <a name="enable-keychain-access"></a>Activer l’accès au trousseau

Dans la bibliothèque MSAL 2.x et 4.x ADAL, l’ID d’équipe est utilisé pour accéder au trousseau, ce qui permet les bibliothèques d’authentification permettant l’authentification-unique (SSO) entre les applications du même éditeur. 

Quelle est la [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId) ? Il est un identificateur unique (entreprise ou personnels) dans l’App Store. L’AppId est unique pour une application. Si vous avez plusieurs applications, l’ID d’équipe pour toutes les applications seront les mêmes, mais l’AppId sera différente. Le groupe d’accès au trousseau est préfixé par ID d’équipe automatiquement pour chaque groupe par le système. Il est comment le système d’exploitation impose que les applications du même éditeur peuvent accéder au trousseau partagé. 

Lors de l’initialisation du `PublicClientApplication`, si vous recevez un `MsalClientException` avec le message : `TeamId returned null from the iOS keychain...`, vous devez effectuer les opérations suivantes dans l’application de Xamarin iOS :

1. Dans Visual Studio, sous l’onglet Déboguer, atteindre nameOfMyApp.iOS propriétés...
2. Puis accédez à la signature du Bundle iOS 
3. Droits personnalisés, cliquez sur le... Sélectionnez le fichier Entitlements.plist à partir de votre application
4. Dans le fichier csproj de l’application iOS, vous devez avoir cette ligne désormais incluse : `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **Reconstruire** le projet.

Il s’agit de *en outre* de permettre l’accès au trousseau dans le `Entitlements.plist` de fichiers, à l’aide la sous groupe d’accès ou votre propre :

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
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

Plus de détails sont fournis dans le [considérations spécifiques pour iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) paragraphe du fichier readme.md de l’exemple suivant :

Exemple | Plateforme | Description  
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Une application Xamarin Forms simple expliquant comment utiliser MSAL pour authentifier MSA et Azure AD via le point de terminaison V2.0 d’AAD et accéder à Microsoft Graph avec le jeton résultant. <br>![Topologie](media/msal-net-xamarin-ios-considerations/topology.png)