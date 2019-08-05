---
title: Application mobile appelant des API web (configuration du code) Plateforme d’identités Microsoft | Azure
description: Découvrez comment créer une application mobile qui appelle des API web (configuration du code de l’application)
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
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bdf9210eb88b2057cf861b208f19d3e6f562e9a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414794"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Application mobile appelant des API web - Configuration du code

Une fois que vous avez créé votre application, vous allez apprendre à configurer le code à partir des paramètres de l’application que vous avez obtenus lors de l’inscription de l’application. Les applications mobiles ont également des spécificités complexes, qui sont liées à leur adaptation au framework utilisé pour créer ces applications

## <a name="msal-libraries-supporting-mobile-apps"></a>Bibliothèques MSAL prenant en charge des applications mobiles

Bibliothèques Microsoft prenant en charge des applications mobiles :

  Bibliothèque MSAL | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Pour développer des applications portables. Les plateformes prises en charge par MSAL.NET pour créer une application mobile sont UWP, Xamarin.iOS et Xamarin.Android.
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Pour développer des applications iOS natives avec Objective C ou Swift
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Pour développer des applications Android natives dans Java pour Android

## <a name="configuring-the-application"></a>Configuration de l’application

Les applications mobiles utilisent la classe `PublicClientApplication` de MSAL. Voici comment l’instancier :

### <a name="android"></a>Android

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
}
```

### <a name="xamarin-or-uwp"></a>Xamarin ou UWP

Le paragraphe suivant explique comment configurer le code de l’application pour les applications Xamarin.iOS, Xamarin.Android et UWP. La première étape consiste à instancier l’application. Une étape facultative consiste à configurer le répartiteur.

#### <a name="instantiating-the-application"></a>Instanciation de l’application

Dans Xamarin, ou UWP, le moyen le plus simple d’instancier l’application est le suivant, `ClientId` où est le GUID de votre application inscrite.

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Des méthodes de *paramètres* supplémentaires permettent de définir le parent de l’interface utilisateur, de remplacer l’autorité par défaut, de spécifier un nom de client et une version (pour la télémétrie), de spécifier un URI de redirection, de spécifier la fabrique Http à utiliser (par exemple, pour gérer les proxies, spécifier la télémétrie et la journalisation). Il s’agit du sujet traité dans les paragraphes suivants.

##### <a name="specifying-the-parent-uiwindowactivity"></a>Spécification de l’interface utilisateur/fenêtre/activité parent(e)

Sur Android, vous devez transmettre l’activité parente avant d’effectuer l’authentification interactive. Sur iOS, lors de l’utilisation d’un répartiteur, vous devez transmettre le ViewController. De la même façon sur UWP, vous pouvez transmettre la fenêtre parente. Cela est possible lorsque vous obtenez le jeton, mais il est également possible de spécifier un rappel au moment de la création de l’application, un délégué retournant l’interface utilisateur parente.

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Sur Android, nous vous recommandons le `CurrentActivityPlugin` [ici](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Votre code de générateur `PublicClientApplication` ressemble alors à ceci :

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>Autres paramètres de génération d’applications

- Pour obtenir la liste de tous les modificateurs disponibles sur `PublicClientApplicationBuilder`, consultez la documentation de référence [publicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Pour la description de toutes les options exposées dans `PublicClientApplicationOptions`, consultez [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), dans la documentation de référence.

#### <a name="xamarin-ios-specific-considerations"></a>Considérations propres à Xamarin iOS

Sur Xamarin iOS, il existe plusieurs considérations à prendre en compte lors de l’utilisation de MSAL.NET :

1. [Écraser et implémenter la fonction `OpenUrl` dans le `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Activer les groupes de trousseau](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Activer le partage du cache de jeton](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Activer l’accès au trousseau](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Les détails sont fournis dans les [considérations relatives à Xamarin iOS](msal-net-xamarin-ios-considerations.md).

#### <a name="other-xamarin-android-specific-considerations"></a>Autres considérations propres à Xamarin Android

Voici les spécificités de Xamarin Android :

- [S’assurer que le contrôle revient à la bibliothèque MSAL une fois la partie interactive du flux d’authentification terminée](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Mettre à jour le manifeste Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Utiliser la vue web incorporée (facultatif)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Dépannage](msal-net-xamarin-android-considerations.md#troubleshooting)

Les détails sont fournis dans les [considérations relatives à Xamarin Android](msal-net-xamarin-android-considerations.md).

Enfin, il existe des spécificités à connaître au sujet des navigateurs sur Android. Celles-ci sont expliquées dans [Considérations spécifiques à Xamarin Android avec MSAL.NET](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Considérations propres à UWP

Sur UWP, vous pouvez utiliser des réseaux d’entreprise. Pour plus d’informations sur les spécificités UWP, consultez [Considérations relatives à la plateforme Windows universelle avec MSAL.NET](msal-net-uwp-considerations.md)

## <a name="configuring-the-application-to-use-the-broker"></a>Configuration de l’application pour utiliser le répartiteur

### <a name="why-use-brokers-on-xamarinios-and-xamarinandroid-applications"></a>Pourquoi utiliser des répartiteurs sur des applications Xamarin.iOS et Xamarin.Android ?

Sur Android et iOS, les répartiteurs activent :

- L’authentification unique. Vos utilisateurs n’ont pas besoin de se connecter à chaque application.
- L’identification des appareils. Active des stratégies d’accès conditionnel Azure AD liées aux appareils, en accédant au certificat de l’appareil qui a été créé sur l’appareil lorsque ce dernier a été joint à l’espace de travail.
- Vérification de l’identification de l’application. Lorsqu’une application appelle le répartiteur, elle transmet son URL de redirection et le répartiteur la vérifie.

### <a name="enable-the-brokers-on-xamarin"></a>Activer les répartiteurs sur Xamarin

Pour activer l’une de ces fonctionnalités, utilisez le paramètre `WithBroker()` lors de l’appel de la méthode `PublicClientApplicationBuilder.CreateApplication`. `.WithBroker()` a la valeur true par défaut. Pour [iOS](#brokered-authentication-for-xamarinios), procédez comme suit.

### <a name="brokered-authentication-for-xamarinios"></a>Authentification répartie pour Xamarin.iOS

Suivez les étapes ci-dessous pour permettre à votre application Xamarin.iOS de communiquer avec l’application [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458).

#### <a name="step-1-enable-broker-support"></a>Étape 1 : Activer la prise en charge du répartiteur

La prise en charge du répartiteur est activée par `PublicClientApplication`. Elle est désactivée par défaut. Vous devez utiliser le paramètre `WithBroker()` (défini sur true par défaut) lors de la création de `PublicClientApplication` via `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Étape 2 : Mettre à jour AppDelegate pour gérer le rappel

Quand MSAL.NET appelle le répartiteur, ce dernier effectue à son tour un rappel à votre application via la méthode `AppDelegate.OpenUrl`. Étant donné que MSAL attend la réponse du répartiteur, votre application doit coopérer pour rappeler MSAL.NET. Pour ce faire, vous devez mettre à jour le fichier `AppDelegate.cs` pour remplacer la méthode ci-dessous.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Cette méthode est appelée chaque fois que l’application est lancée et permet de traiter la réponse du répartiteur et de terminer le processus d’authentification initié par MSAL.NET.

#### <a name="step-3-set-a-uiviewcontroller"></a>Étape 3 : Définir un UIViewController()

Avec Xamarin iOS, vous n’avez généralement pas besoin de définir une fenêtre d’objet, mais dans ce cas, vous devez envoyer et recevoir des réponses d’un répartiteur. Toujours dans `AppDelegate.cs`, définissez un ViewController.

Procédez comme suit pour définir la fenêtre de l’objet :

1) Dans `AppDelegate.cs`, affectez la valeur `App.RootViewController` à un nouveau `UIViewController()`. Cela permet de s’assurer qu’il y a un `UIViewController` avec l’appel au répartiteur. S’il n’est pas défini correctement, vous pouvez recevoir cette erreur : `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Dans l’appel AcquireTokenInteractive, utilisez `.WithParentActivityOrWindow(App.RootViewController)` et transmettez la référence à la fenêtre d’objet que vous allez utiliser.

**Par exemple :**

Dans `App.cs` :
```CSharp
   public static object RootViewController { get; set; }
```
Dans `AppDelegate.cs` :
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Dans l’appel d’acquisition de jeton :
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>Étape 4 : Inscrire un schéma d’URL

MSAL.NET utilise des URL pour appeler le répartiteur, avant de retourner la réponse du répartiteur à votre application. Pour terminer l’aller-retour, vous devez inscrire un schéma d’URL pour votre application dans le fichier `Info.plist`.

Donnez à `msauth` le préfixe `CFBundleURLSchemes`. Ajoutez `CFBundleURLName` ensuite à la fin.

`$"msauth.(BundleId)"`

**Par exemple :** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ce schéma d’URL fera partie du RedirectUri utilisé pour identifier de manière unique votre application lors de la réception de la réponse du répartiteur.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>Étape 5 : LSApplicationQueriesSchemes

MSAL utilise `–canOpenURL:` pour vérifier si le répartiteur est installé sur l’appareil. Dans iOS 9, Apple a verrouillé la liste des schémas dans lesquels une application peut lancer une requête.

**Ajoutez** **`msauthv2`** à la section `LSApplicationQueriesSchemes` du fichier `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Authentification répartie pour Xamarin.Android

MSAL.NET ne prend pas encore en charge les répartiteurs pour Android.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Acquisition d’un jeton](scenario-mobile-acquire-token.md)
