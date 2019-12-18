---
title: Configurer des applications mobiles appelant des API web - Plateforme d’identités Microsoft | Azure
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
ms.openlocfilehash: 44392882a7d3e1816b952969dbadb518e2762142
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919951"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Application mobile appelant des API web - Configuration du code

Une fois que vous avez créé votre application, vous allez apprendre à configurer le code à l’aide des paramètres d’inscription de l’application. Les applications mobiles ont également des spécificités complexes, qui sont liées à leur adaptation au framework utilisé pour créer ces applications

## <a name="msal-libraries-supporting-mobile-apps"></a>Bibliothèques MSAL prenant en charge des applications mobiles

Bibliothèques Microsoft prenant en charge des applications mobiles :

  Bibliothèque MSAL | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Pour développer des applications portables. Les plateformes prises en charge par MSAL.NET pour créer une application mobile sont UWP, Xamarin.iOS et Xamarin.Android.
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Pour développer des applications iOS natives avec Objective-C ou Swift
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Pour développer des applications Android natives dans Java pour Android

## <a name="instantiating-the-application"></a>Instanciation de l’application

### <a name="android"></a>Android

Les applications mobiles utilisent la classe `PublicClientApplication`. Voici comment l’instancier :

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Les applications mobiles sur iOS doivent instancier la classe `MSALPublicClientApplication`.

Objective-C :

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift :
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

Il existe des [propriétés MSALPublicClientApplicationConfig supplémentaires](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) qui peuvent remplacer l’autorité par défaut, spécifier un URI de redirection ou modifier le comportement de mise en cache du jeton MSAL. 

### <a name="xamarin-or-uwp"></a>Xamarin ou UWP

Le paragraphe suivant explique comment instancier l’application pour des applications Xamarin.iOS, Xamarin.Android et UWP.

#### <a name="instantiating-the-application"></a>Instanciation de l’application

Dans Xamarin ou UWP, le moyen le plus simple d’instancier l’application est le suivant, où `ClientId` est le GUID de votre application inscrite.

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

## <a name="xamarin-ios-specific-considerations"></a>Considérations propres à Xamarin iOS

Sur Xamarin iOS, il existe plusieurs considérations à prendre en compte lors de l’utilisation de MSAL.NET :

1. [Écraser et implémenter la fonction `OpenUrl` dans le `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Activer les groupes de trousseau](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Activer le partage du cache de jeton](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Activer l’accès au trousseau](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Les détails sont fournis dans les [considérations relatives à Xamarin iOS](msal-net-xamarin-ios-considerations.md).

## <a name="msal-for-ios-and-macos-specific-considerations"></a>Considérations spécifiques concernant MSAL pour iOS et macOS

Des considérations similaires s’appliquent lors de l’utilisation de MSAL pour iOS et macOS :

1. [Implémenter le rappel`openURL`](#brokered-authentication-for-msal-for-ios-and-macos)
2. [Activer les groupes d’accès au trousseau](howto-v2-keychain-objc.md)
3. [Personnaliser les navigateurs et les vues web](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Considérations spécifiques de Xamarin Android

Voici les spécificités de Xamarin Android :

- [S’assurer que le contrôle revient à la bibliothèque MSAL une fois la partie interactive du flux d’authentification terminée](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Mettre à jour le manifeste Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Utiliser la vue web incorporée (facultatif)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Dépannage](msal-net-xamarin-android-considerations.md#troubleshooting)

Les détails sont fournis dans les [considérations relatives à Xamarin Android](msal-net-xamarin-android-considerations.md).

Enfin, il existe des spécificités à connaître au sujet des navigateurs sur Android. Celles-ci sont expliquées dans [Considérations spécifiques à Xamarin Android avec MSAL.NET](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Considérations propres à UWP

Sur UWP, vous pouvez utiliser des réseaux d’entreprise. Pour plus d’informations sur l’utilisation de la bibliothèque MSAL avec UWP, consultez [Considérations propres à la plateforme Windows universelle avec MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Configuration de l’application pour utiliser le répartiteur

### <a name="why-use-brokers-in-ios-and-android-applications"></a>Pourquoi utiliser des répartiteurs dans des applications iOS et Android ?

Sur Android et iOS, les répartiteurs activent :

- L’authentification unique (SSO) quand un appareil est inscrit auprès d’AAD. Vos utilisateurs n’ont pas besoin de se connecter à chaque application.
- L’identification des appareils. Active des stratégies d’accès conditionnel Azure AD liées aux appareils, en accédant au certificat d’appareil créé sur l’appareil lorsque ce dernier a été joint à l’espace de travail.
- Vérification de l’identification de l’application. Lorsqu’une application appelle le répartiteur, elle transmet son URL de redirection et le répartiteur la vérifie.

### <a name="enable-the-broker-on-xamarin"></a>Activer le répartiteur sur Xamarin

Pour activer l’une de ces fonctionnalités, utilisez le paramètre `WithBroker()` lors de l’appel de la méthode `PublicClientApplicationBuilder.CreateApplication`. `.WithBroker()` a la valeur true par défaut. Pour [Xamarin.iOS](#brokered-authentication-for-xamarinios), procédez comme suit.

### <a name="enable-the-broker-for-msal-for-android"></a>Activer le répartiteur pour MSAL pour Android

Pour plus d’informations sur l’activation d’un répartiteur sur Android, voir [Authentification répartie dans Android](brokered-auth.md). 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Activer le répartiteur pour MSAL pour iOS et macOS

L’authentification répartie est activée par défaut pour les scénarios AAD dans MSAL pour iOS et macOS. Suivez les étapes ci-dessous pour configurer votre application pour la prise en charge de l’authentification répartie pour [MSAL pour iOS et macOS](#brokered-authentication-for-msal-for-ios-and-macos). Notez que certaines étapes diffèrent entre [MSAL pour Xamarin.iOS](#brokered-authentication-for-xamarinios) et [MSAL pour iOS et macOS](#brokered-authentication-for-msal-for-ios-and-macos).

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

Avec Xamarin iOS, vous n’avez normalement pas besoin de définir une fenêtre d’objet, mais dans ce cas, vous devez le faire pour envoyer et recevoir des réponses d’un répartiteur. Toujours dans `AppDelegate.cs`, définissez un ViewController.

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

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Authentification répartie MSAL pour iOS et macOS

L’authentification répartie est activée par défaut pour les scénarios AAD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Étape 1 : Mettre à jour AppDelegate pour gérer le rappel

Quand MSAL pour iOS et macOS appelle le répartiteur, celui-ci rappelle votre application via la méthode `openURL`. Étant donné que MSAL attend la réponse du répartiteur, votre application doit coopérer pour rappeler MSAL. Pour ce faire, vous devez mettre à jour le fichier `AppDelegate.m` pour remplacer la méthode ci-dessous.

Objective-C :

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift :

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Notez que, si vous avez adopté UISceneDelegate sur iOS 13 +, le rappel MSAL doit être placé dans les `scene:openURLContexts:` de UISceneDelegate à la place (voir la [documentation Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)). MSAL `handleMSALResponse:sourceApplication:` ne doit être appelé qu’une seule fois pour chaque URL.

#### <a name="step-2-register-a-url-scheme"></a>Étape 2 : Inscrire un schéma d’URL

MSAL pour iOS et macOS utilise des URL pour appeler le répartiteur, avant de retourner la réponse du répartiteur à votre application. Pour terminer l’aller-retour, vous devez inscrire un schéma d’URL pour votre application dans le fichier `Info.plist`.

Ajoutez le préfixe `msauth` à votre modèle d’URL personnalisée. Ajoutez ensuite votre **Identificateur de bundle** à la fin.

`msauth.(BundleId)`

**Par exemple :** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ce schéma d’URL fera partie du RedirectUri utilisé pour identifier de manière unique votre application lors de la réception de la réponse du répartiteur. Assurez-vous que le RedirectUri au format `msauth.(BundleId)://auth` est inscrit pour votre application dans le [portail Azure](https://portal.azure.com).

```XML
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-lsapplicationqueriesschemes"></a>Étape 3 : LSApplicationQueriesSchemes

**Ajoutez `LSApplicationQueriesSchemes`** pour autoriser l’appel à Microsoft Authenticator s’il est installé.
Notez que le schéma « msauthv3 » est nécessaire lors de la compilation de votre application avec Xcode 11 et versions ultérieures. 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Authentification répartie pour Xamarin.Android

MSAL.NET ne prend pas encore en charge les répartiteurs pour Android.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Acquisition d’un jeton](scenario-mobile-acquire-token.md)
