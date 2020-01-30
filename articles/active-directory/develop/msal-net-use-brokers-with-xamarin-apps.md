---
title: Utiliser des répartiteurs avec Xamarin, iOS et Android | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment migrer des applications Xamarin iOS de la bibliothèque Azure AD Authentication pour .NET (ADAL.NET) vers la bibliothèque Microsoft Authenticator pour .NET (MSAL.NET) en utilisant Microsoft Authenticator
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 839f62660096aaf3d7954acc45443f04d9ace77d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695141"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Utiliser Microsoft Authenticator ou le portail d’entreprise Microsoft Intune sur des applications Xamarin

Sur Android et iOS, des répartiteurs tels que Microsoft Authenticator ou le portail d’entreprise Microsoft Intune (Android uniquement) permettent d’effectuer les opérations suivantes :

- Authentification unique (SSO) Vos utilisateurs n’ont pas besoin de se connecter à chaque application.
- L’identification des appareils. Le répartiteur accède au certificat de l’appareil qui a été créé sur celui-ci lors de son rattachement à un espace de travail.
- Vérification de l’identification de l’application. Lorsqu’une application appelle le répartiteur, elle transmet son URL de redirection et le répartiteur la vérifie.

Pour activer l’une de ces fonctionnalités, les développeurs d’applications doivent utiliser le paramètre `WithBroker()` lorsqu'ils appellent la méthode `PublicClientApplicationBuilder.CreateApplication`. `.WithBroker()` a la valeur true par défaut. Pour les applications [iOS](#brokered-authentication-for-ios) ou [Android](#brokered-authentication-for-android), les développeurs doivent également suivre les étapes présentées ici.

## <a name="brokered-authentication-for-ios"></a>Authentification répartie pour iOS

Suivez ces étapes pour permettre à votre application Xamarin.iOS de communiquer avec l’application [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="step-1-enable-broker-support"></a>Étape 1 : Activer la prise en charge du répartiteur
La prise en charge du répartiteur est activée pour chaque application cliente publique (PublicClientApplication). Elle est désactivée par défaut. Utilisez le paramètre `WithBroker()` (dont la valeur est true par défaut) lorsque vous créez l’application cliente publique (PublicClientApplication) via le générateur d’application cliente publique (PublicClientApplicationBuilder).

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Étape 2 : Activer l’accès au trousseau

Pour activer l’accès au trousseau, votre application doit avoir un groupe d’accès au trousseau. Vous pouvez utiliser l’API `WithIosKeychainSecurityGroup()` pour définir votre groupe d’accès au trousseau lorsque vous créez votre application :

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Pour plus d'informations, consultez [Activer l’accès au trousseau](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Étape 3 : Mettre à jour AppDelegate pour gérer le rappel
Lorsque la bibliothèque d'authentification Microsoft pour .NET (MSAL.NET) appelle le répartiteur, ce dernier rappelle votre application via la méthode `OpenUrl` de la classe `AppDelegate`. MSAL attendant la réponse du répartiteur, votre application doit coopérer pour rappeler MSAL.NET. Pour permettre cette coopération, mettez à jour le fichier `AppDelegate.cs` pour remplacer la méthode suivante.

```csharp
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

Cette méthode est appelée chaque fois que l'application est lancée. Elle permet de traiter la réponse du répartiteur et d’accomplir le processus d’authentification initié par MSAL.NET.

### <a name="step-4-set-a-uiviewcontroller"></a>Étape 4 : Définir un UIViewController()
Toujours dans `AppDelegate.cs`, vous devez définir une fenêtre d’objet. Normalement, avec Xamarin iOS, vous n'êtes pas tenu de définir la fenêtre d'objet. Pour envoyer et recevoir des réponses du répartiteur, vous avez besoin d’une fenêtre d’objet. 

Pour ce faire, vous devez effectuer deux opérations. 
1. Dans `AppDelegate.cs`, affectez la valeur `App.RootViewController` à un nouveau `UIViewController()`. Cette affectation vérifie l’existence d’un UIViewController avec l’appel au répartiteur. S’il n’est pas défini correctement, vous pouvez recevoir cette erreur : `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Dans l’appel AcquireTokenInteractive, utilisez `.WithParentActivityOrWindow(App.RootViewController)` et transmettez la référence à la fenêtre d’objet que vous allez utiliser.

**Par exemple :**

Dans `App.cs` :
```csharp
   public static object RootViewController { get; set; }
```
Dans `AppDelegate.cs` :
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Dans l’appel d’acquisition de jeton :
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

### <a name="step-5-register-a-url-scheme"></a>Étape 5 : Inscrire un schéma d’URL
MSAL.NET utilise des URL pour appeler le répartiteur, avant de retourner la réponse du répartiteur à votre application. Pour terminer l’aller-retour, inscrivez un schéma d’URL pour votre application dans le fichier `Info.plist`.

Le nom `CFBundleURLSchemes` doit inclure le préfixe `msauth.`, suivi de votre `CFBundleURLName`.

`$"msauth.(BundleId)"`

**Par exemple :**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> Ce schéma d’URL fait partie intégrante de l’URI de redirection utilisé pour identifier de manière unique votre application lorsqu’elle reçoit la réponse du répartiteur.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Étape 6 : Ajouter l’identificateur du répartiteur à la section LSApplicationQueriesSchemes
MSAL utilise `–canOpenURL:` pour vérifier si le répartiteur est installé sur l’appareil. Dans iOS 9, Apple a verrouillé la liste des schémas dans lesquels une application peut lancer une requête. 

Ajoutez `msauthv2` à la section `LSApplicationQueriesSchemes` du fichier `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Étape 7 : Inscrire votre URI de redirection dans le portail d’applications
L’utilisation du répartiteur ajoute une exigence à votre URI de redirection. Le format de l'URI de redirection _doit_ être le suivant :
```csharp
$"msauth.{BundleId}://auth"
```
**Par exemple :**
```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Vous remarquerez que l'URI de redirection correspond au nom `CFBundleURLSchemes` que vous avez inclus dans le fichier `Info.plist`.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Étape 8 : Vérifier que l’URI de redirection est inscrit avec votre application

Cet URI de redirection doit être inscrit sur le portail d’inscription d’application (https://portal.azure.com) en tant qu’URI de redirection valide pour votre application. 

Le portail offre une nouvelle expérience d’inscription d’application pour vous aider à calculer l’URI de réponse réparti à partir de l’ID d’offre groupée.

1. Dans l’inscription d’application, choisissez **Authentification**, puis sélectionnez **Essayer la nouvelle expérience**.

   ![Essayer la nouvelle expérience d’inscription d’application](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Sélectionnez **Ajouter une plateforme**.

   ![Ajouter une plateforme](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Lorsque la liste des plateformes est prise en charge, sélectionnez **iOS**.

   ![Configurer iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Entrez votre ID d’offre groupée comme demandé, puis sélectionnez **Configurer**.

   ![Entrer l’ID d’offre groupée](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. L’URI de redirection est calculé pour vous.

   ![Copier URI de redirection](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Authentification répartie pour Android

MSAL.NET ne prend en charge que la plateforme Xamarin.iOS pour le moment. Elle ne prend pas encore en charge les répartiteurs pour la plateforme Xamarin.Android.

La bibliothèque native Android MSAL la prend déjà en charge. Pour plus d’informations, voir [Authentification répartie dans Android](brokered-auth.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez les [Considérations relatives à la plateforme Windows universelle avec MSAL.NET](msal-net-uwp-considerations.md).
